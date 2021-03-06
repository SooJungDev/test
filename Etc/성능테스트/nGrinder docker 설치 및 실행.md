# nGrinder docker 설치 실행
1. Controller 설치 

- 목록확인
~~~
sudo docker search ngrinder
~~~

- 설치
~~~
sudo docker pull ngrinder/controller:3.4
~~~

- 실행
~~~
sudo docker run --name ngrinder_controller -d  -v ~/.ngrinder:/root/.ngrinder -p 8080:80 -p 16001:16001 -p 12000-12009:12000-12009 ngrinder/controller:3.4
~~~

- 확인
~~~
sudo docker ps
~~~

- 접속 
~~~
localhost:8080/login
~~~

- 종료
~~~
sudo docker stop ngrinder_controller
~~~

2.Agent 설치

- 설치
~~~
sudo docker pull ngrinder/agent:3.4
~~~

- 실행
~~~
sudo docker run --name ngrinder_agent -d -e CONTROLLER_ADDR=localhost:8080 ngrinder/agent:3.4
~~~

- 종료
~~~
sudo docker stop ngrinder_agent
~~~


## agent 가 붙지 않는 문제가생김
- agent 가 설치된 폴더로 들어가서 
~~~
./run_agent.sh -o
~~~
https://github.com/naver/ngrinder/issues/63


## 그냥 실행하는 방법
~~~
java -XX:MaxPermSize=200m -jar ngrinder-controller-3.4.2.war --port 8080
~~~


## 참고사이트
- [nGrinder docker 설치 실행](https://code-factory.tistory.com/32)
- [nGrinder 빨리 설치하기](https://multicloud.tistory.com/86)