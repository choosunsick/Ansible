# 라즈베리파이을 이용한 Ansible 고급 기초

이제 마지막 기초입니다. 앞선 글은 다음과 같습니다.

- [라즈베리파이을 이용한 Ansible 기초](https://github.com/LOPES-HUFS/Ansible/blob/main/Raspberry_Pi_intro/doc_1.md)
- [라즈베리파이을 이용한 앤서블의 본격적인 기초](https://github.com/LOPES-HUFS/Ansible/blob/main/Raspberry_Pi_intro/doc_2.md)

앞 글에서 각종 내용이 들어 있는 인벤토리 파일을 암호화해서 앤서블 `ping`을 해봤습니다.

```bash
❯ ansible all -m ping --ask-vault-password -i encrypt_hosts
Vault password: 
lopes.hufs.ac.kr | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

이번에는 `ansible-playbook`을 이용하여 우리가 따로 따로 실행했던, `ping`과 `df -h`를 한꺼번에 처리해보겠습니다. `ping_and_df.yml`라는 이름을 가진 파일을 만들어서, 다음과 같이 입력합니다.

```yaml
- hosts: all
  user: pi
  tasks:
    - name: Ping all hosts
      ping:
    - name: Let's see how much disk is left.
      shell: df -h
      register: df_meg
    - debug: var=df_meg["stdout_lines"]
```
