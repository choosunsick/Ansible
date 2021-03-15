# 본격적인 기초

앤서블의 `Hello World!`인 앤서블 `ping`을 해봤습니다. 아래 코드까지 해봤습니다.

```bash
# PW: raspberry
ansible all -m ping --ask-pass --user=pi --inventory 'raspberrypi.local,' -e 'ansible_python_interpreter=/usr/bin/python3'
```

윗 명령어의 결과는 다음과 같이 나왔습니다.

```bash
❯ ansible all -m ping  --ask-pass --user=pi --inventory 'raspberrypi.local,' -e 'ansible_python_interpreter=/usr/bin/python3'
SSH password: 
raspberrypi.local | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

위에서 조금 번잡하게 쓴 코드를 조금 있어 보이게 작성해 봅시다. 우선 현재 폴더에 `hosts`라는 이름을 가진 파일을 만들어서 다음과 같은 내용을 추가합니다. 마지막에 빈 줄을 넣는 것이 좋습니다.

```bash
[all]
raspberrypi.local

[all:vars]
ansible_python_interpreter=/usr/bin/python3

```

짐작은 하시겠지만, 명령어에서 쓴 내용들을 파일에 적은 것입니다. 이런 파일을 앤서블에서는 inventory(이하 인벤토리)라고 합니다. 앞에 쓴 내용을 가지고 앞의 명령어를 줄여서 아래와 같이 해봅시다. `-k`는 `--ask-pass`을, `-u`는 `--user`을, `-i`는 `--inventory`을 줄인 것입니다. 앞의 것보다 아주 깔끔합니다. 앞에서 이야기를 안 했지만, `-m`은 `--module-name`을 줄인 것으로 뒤에 나오는 모듈을 사용하라는 옵션인데 여기서는 `ping`이라는 모듈을 사용하고 있습니다.

```bash
# PW: raspberry
ansible all -m ping -k -u pi -i hosts
```

결과는 다음과 같습니다. 위와 똑같은 결과가 나왔죠.

```bash
❯ ansible all -m ping -k -u pi -i hosts
SSH password: 
raspberrypi.local | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

앞에서는 `ping`이라는 모듈을 사용했지만, 아래와 같이 하면 현재 `raspberrypi.local`에 설치되어 있는 명령어도 사욜할 수 있습니다.

```bash
# PW: raspberry
ansible all -m raw -a 'df -h' -k -u pi -i hosts
```

결과는 다음과 같습니다. 만약 `raspberrypi.local`에 아무 것도 한 것이 없으면, 앞에서 `sshpass`을 연습한 결과가 거의 똑같은 결과가 나옵니다.

```bahs
❯ ansible all -a 'df -h' -k -u pi -i hosts
SSH password: 
raspberrypi.local | CHANGED | rc=0 >>
Filesystem      Size  Used Avail Use% Mounted on
/dev/root        59G  1.3G   55G   3% /
devtmpfs        430M     0  430M   0% /dev
tmpfs           463M     0  463M   0% /dev/shm
tmpfs           463M  6.2M  457M   2% /run
tmpfs           5.0M  4.0K  5.0M   1% /run/lock
tmpfs           463M     0  463M   0% /sys/fs/cgroup
/dev/mmcblk0p1  253M   47M  206M  19% /boot
tmpfs            93M     0   93M   0% /run/user/1000
```

 지금까지 `SSH password: `이라는 것이 나오면, PW를 입력해줘야 했습니다. 이것도 입력하지 않고 바로 결과가 나오도록 해보겠습니다. 우선 현재 폴더에 `new_hosts`라는 이름을 가진 파일을 만들어서 다음과 같은 내용을 추가합니다. 마지막에 빈 줄을 넣는 것이 좋습니다. 앞에서 작성한 `hosts`에 다음과 같이`ansible_user`과 `ansible_ssh_pass`을 추가한 것입니다. 기존에 있던 파일에 추가해도 되지만, 설명을 쉽게 하기 위해 새로 만들었습니다.

```bash
[all]
raspberrypi.local

[all:vars]
ansible_python_interpreter=/usr/bin/python3
ansible_user=pi
ansible_ssh_pass=raspberry
```

이번에는 `ansible-vault`를 사용하여 파일에 password를 입력해, 지금까지 `SSH password: `에 직접 입력하지 않고 자동적으로 입력되도록 설정해보자. 우선 현재 폴더에 `password.yml`라는 이름을 가진 파일을 만들어서 다음과 같은 내용을 추가합니다. 마지막에 빈 줄을 넣는 것이 좋습니다. 이제 `user`을 파일에 입력했으니, 명령어에서 `-u pi`를 제거했습니다. 또한 `ssh_pass`를 파일에 입력했으니 패스워드를 요구하는 옵션인 `--ask-become-pass`을 줄인 `-k`도 제거합니다.

```bash
ansible all -m ping -i new_hosts
```

결과는 아래와 같습니다. `SSH password: `이 나오지 않으니 값을 입력하지 않고도 바로 실행됩니다.

```bash
❯ ansible all -m ping -i new_hosts

raspberrypi.local | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

다음 것도 아주 쉽게 작동합니다.

```bash
❯ ansible all -a 'df -h' -i new_hosts  
raspberrypi.local | CHANGED | rc=0 >>
Filesystem      Size  Used Avail Use% Mounted on
/dev/root        59G  1.3G   55G   3% /
devtmpfs        430M     0  430M   0% /dev
tmpfs           463M     0  463M   0% /dev/shm
tmpfs           463M  6.2M  457M   2% /run
tmpfs           5.0M  4.0K  5.0M   1% /run/lock
tmpfs           463M     0  463M   0% /sys/fs/cgroup
/dev/mmcblk0p1  253M   47M  206M  19% /boot
tmpfs            93M     0   93M   0% /run/user/1000
```

이런 것을 암호화하지 않고 평문으로 넣는 것이 찝찝합니다. 이것을 암호화 해보겠습니다.

```bash
pi: raspberry
```

그런 다음 아래와 같이 암호화합니다. 전 `New Vault password`으로 `1234`을 입력했습니다.

```bash
❯ ansible-vault encrypt password.yml
New Vault password: 
Confirm New Vault password: 
Encryption successful
```

암호화되었기 때문에 당연히 아까 입력한 아이디와 비번은 알아볼 수 없게 되었습니다.

```bash
❯ cat password.yml                  
$ANSIBLE_VAULT;1.1;AES256
66656631336530386561376262333631393562346166666239373037376566303436613462643962
3838613939613931623532356635343466653731383330370a313739623234633166353966333531
66636230346262333765656231353131616663313838666164666334646632653462313161646330
6462363632323233320a356331393664386439366161313263346162366164353938613865366234
6238
```

입력한 것을 확인하려면 다음과 같이 `ansible-vault view`를 이용하시면 됩니다.

```bash
❯ ansible-vault view password.yml 
Vault password: 
```
