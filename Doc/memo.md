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


### 참고 링크

- [ansible.builtin.apt – Manages apt-packages — Ansible Documentation](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_module.html)
- [머가필요해](https://www.whatwant.com/entry/Ansible-세번째-발걸음-apt-get-upgrade)
