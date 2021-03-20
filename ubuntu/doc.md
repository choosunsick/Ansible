# 우분투를 이용한 엔서블 연습

이미지 파일을 하나 만듭니다.

```bash
docker build --force-rm --tag ubuntu_ansible:0.1 .
```

아래 것이 정확하게 작동 안 합니다.

```bash
docker run -p 1234:22 -p 1235:80 ubuntu_ansible
```

자 들어가보겠습니다.

```bash
# PW: raspberry
# 만약 이미지를 다시 만들어서 안 들어가면 아랫 줄을 입력
# ssh-keygen -R "[localhost]:1234"
ssh -p 1234 pi@localhost
```

`sshpass`을 사용하겠습니다.

```bash
sshpass -p raspberry ssh -o StrictHostKeyChecking=no -p 1234 pi@localhost
```

앤서블 `Hello World`인 `ping`!

```bash
ansible-playbook ping.yml -i hosts -e 'ansible_ssh_port=1234'
```

아래와 같이 하면 nginx가 설치되면서 작동합니다.

여기서는 왜 `-u pi --ask-become-pass`을 넣어주어야 작동할까?
하여간 작동은 합니다.

```bash
ansible-playbook nginx.yml -i hosts -e 'ansible_ssh_port=1234' -u pi --ask-become-pass
```

다음으로 들어가면 작동하는 모습을 볼 수 있습니다.

[http://localhost:1235](http://localhost:1235)
