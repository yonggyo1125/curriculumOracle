# 도커 명령어 

- 도커 이미지 pull  : docker image pull 레파지토리명\[:태그명\]
- docker images : 다운받은 이미지 확인 
- docker rmi 이미지 ID  : 이미지 삭제

- docker ps : 실행중인 컨테이너 목록 확인
- docker ps -a : 전체 컨테이너 목록 확인
- docker start 컨테이너ID  : 컨테이너 시작
- docker attach 컨테이너ID  :  컨테이너 접속
- docker stop 컨테이너ID : 컨테이너 멈춤
- docker run 컨테이너ID : 컨테이너 생성 및 시작
- docker run -it 컨테이너ID : 컨테이너 생성 및 시작 및 접속
- docker rm 컨테이너ID : 컨테이너 삭제
- docker exec -it 컨테이너ID /bin/bash  : 실행되고 있던 컨테이너 접속
- exit  : 컨테이너 빠져나오기

# 오라클 11g 엔터프라이즈 버전 설치 
- [도커 이미지](https://hub.docker.com/r/loliconneko/oracle-ee-11g)