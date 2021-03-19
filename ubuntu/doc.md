# 우분투를 이용한 엔서블 연습

이미지 파일을 하나 만듭니다.

```bash
docker build --force-rm --tag ubuntu_ansible:0.1 .
```

아래 것이 정확하게 작동 안 합니다.

```bash
docker run ubuntu_ansible -p 1234:22 -p 1235:80
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

좀 더 깊게 사용하려면 다음 링크를 참고하세요!

- [Install and configure Nginx using Ansible](https://code-maven.com/install-and-configure-nginx-using-ansible)
- [How to setup an ssh server within a docker container - DEV Community](https://dev.to/s1ntaxe770r/how-to-setup-ssh-within-a-docker-container-i5i)
- [Ansible – Looking at commands & Playbooks (Raspberry Pi) – Geek Tech Stuff](https://geektechstuff.com/2019/06/27/ansible-looking-at-commands-playbooks-raspberry-pi/)
