# 1. docker 이미지 다운로드

  우선, 문제에 나와있는 대로 docker 이미지를 docker hub에서 다운로드 받아야 한다. 명령어는 다음과 같다.

`docker pull dreamhackofficial/blue-whale:1`

  이미지 다운로드 중 오류가 난다면 명령어 앞에 `sudo`를 붙여 관리자 권한으로 명령어를 실행시켜보거나 docker가 정상작동하는지 확인 후 진행하도록 하자.

# 2. dive 툴 다운로드

 이미지 다운로드가 끝났다면 우리의 목표는 이미지 안에서 삭제된 flag 파일을 찾아 플래그를 얻는 것이 목표이므로 문제 Hint에 나와있는 dive 도구를 설치해야 한다. 명령어는 다음과 같다.

`snap install dive`

  다른 패키지 다운로드와 똑같이 apt install을 사용하면 오류가 난다.

  여기서 dive 툴을 처음 접한 사람은 dive가 어떤 도구인지 모를 것이므로 간략히 설명을 하자면 Docker 이미지 내부 구조를 분석하는 Text UI(TUI) 도구이다. CTF에서 쓰이기도 하니 잘 알아두도록 해야 한다.

# 3. dive 툴 사용

  이미지를 다운받고 이미지를 분석할 도구까지 설치가 완료되었으니 이제 이미지를 분석하여 삭제된 flag 파일을 찾으면 된다.

`docker images`

위의 명령어를 입력하여 이미지 이름을 확인 후

`dive (이미지 이름)`

이렇게 입력하면 된다. 

![image.png](https://dreamhack-media.s3.amazonaws.com/attachments/b2339338b3ac0bc618d790d7e154216a77e4711417a66a46f0bc176a7df6abc8.png)

  정상적으로 작동하면 위의 화면처럼 이미지 분석 화면에 들어올 수 있다. 그러나

![image.png](https://dreamhack-media.s3.amazonaws.com/attachments/fa2b78fc6afcea0da766c75a12bc66c6249a9d85e5d1c97c9f06a57c8291beb5.png)

위의 사진과 같이 dive 실행이 되지 않을 수 있다 그럴 땐

 `alias dive="docker run -ti --rm -v /var/run/docker.sock:/var/run/docker.sock wagoodman/dive"`

이 명령어를 입력해주면 된다.
이 명령어는 dive 프로그램을 docker 이미지 안에서 실행시키는 명령어이다.

  다시 본론으로 돌아와서 왼쪽은 레이어에 대한 설명 부분과 오른쪽은 최근 레이어 내용에 대한 화면으로 나누어져 있다. 우선 왼쪽에서 flag와 비슷해 보이는 레이어를 찾는다.

![image.png](https://dreamhack-media.s3.amazonaws.com/attachments/89662397d74884f323cd6a3d55567ccf44115d332548736189f0b14c19757c38.png)

  왼쪽 레이어에서 flag를 출력하는 명령어가 있어 이 레이어에 고정해둔 후 tab키를 눌러 오른쪽 화면으로 넘어가 쭉 내리다 보니 flag를 찾아낼 수 있었다.
