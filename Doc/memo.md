# 메모

Ansible(이하 엔서블)을 공부하다가 알게된 것들을 적는 곳

## apt

원칙적으로 `apt-get update`라는 것을 엔서블에 적용하려면 다음과 같이 하면 된다.

```yaml
    - name: Update all packages to their latest version
      apt:
        name: "*"
        state: latest
```

그러나 아래와 같이 하면 `upgrade: yes`이 위의 것을 대산하게 될 수 있다.

```yaml
- hosts: all
  tasks:
    - name: Update and upgrade apt packages
      become: true
      apt:
        upgrade: yes
        update_cache: yes
        cache_valid_time: 86400 #One day
```

다음을 참고한다.

- [ansible.builtin.apt – Manages apt-packages — Ansible Documentation](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_module.html)
- [머가필요해](https://www.whatwant.com/entry/Ansible-세번째-발걸음-apt-get-upgrade)

## ssh 관련 에러

다음과 같은 에러가 나오는 경우 처리법. 참도로 다른 ssh 관련 에러도 이것으로 처리 가능하다.

```bash
The authenticity of host 'xxx.xxx.xxx.xxx (xxx.xxx.xxx.xxx)' can't be established.
RSA key fingerprint is xx:yy:zz:....
Are you sure you want to continue connecting (yes/no)?
```

```bash
192.168.96.16 | FAILED! => {
    "msg": "Using a SSH password instead of a key is not possible because Host Key checking is enabled and sshpass does not support this.  Please add this host's fingerprint to your known_hosts file to manage this host."
}
```

현재 작업하고 있는 폴더에 `ansible.cfg`을 만들어 아래와 같은 내용을 입력한다.

```ini
[defaults]
host_key_checking = False
```

또는 아래와 같이 터미널에서 입력해도 된다. 그러나 앞의 것을 추천한다.

```bash
export ANSIBLE_HOST_KEY_CHECKING=False
```

다음을 참고한다.

- [ANSIBLE FINGERPRINT 접속 오류](http://egloos.zum.com/genes1s/v/3100555)
