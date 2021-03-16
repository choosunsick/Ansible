# 라즈베리파이을 이용한 앤서블의 본격적인 기초

앞의 글, [라즈베리파이을 이용한 Ansible 기초](https://github.com/LOPES-HUFS/Ansible/blob/main/Raspberry_Pi_intro/doc_1.md)에서 앤서블의 `Hello World!`인 앤서블 `ping`을 해봤습니다. 아래 코드까지 진행했습니다.

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

관리할 노드에 대한 IP나 유저 ID, 패스워드를 암호화하지 않고 단순한 텍스트 파일에 넣어 인벤토리로 사용하는 것은 언제나 찝찝합니다. 관리할 노드에 대한 IP나 유저 ID, 패스워드를 저장하는 인벤토리 파일을 암호화해서 사용해 보겠습니다.

`encrypt_hosts`라는 이름을 가진 파일을 만들고, 여기에 `new_hosts` 파일과 동일한 다음과 같은 내용을 입력합니다. 당연히 저장해야 합니다.

```bash
[all]
raspberrypi.local

[all:vars]
ansible_python_interpreter=/usr/bin/python3
ansible_user=pi
ansible_ssh_pass=raspberry

```

그런 다음 아래와 같이 `ansible-vault`을 이용하여 암호화(encrypt)합니다. 전 `New Vault password`으로 `1234`을 입력했습니다.

```bash
❯ ansible-vault encrypt encrypt_hosts
New Vault password: 
Confirm New Vault password: 
Encryption successful
```

암호화되었기 때문에 당연히 파일 내용은 알아볼 수 없게 되었습니다.

```bash
❯ cat encrypt_hosts
$ANSIBLE_VAULT;1.1;AES256
39616563343130623162663438613762636332306230323536333332376136353035383962333132
6362633530373662356430386631376234333862373265340a393434326662316132616664636265
64383132623036643536343036353765343532613362663630333337653161623864343335646533
3664346163303464660a303461643637316538643933613138356137396136643430396336656263
66303530376466626163633135636262386636383965343664383734643338346465393335393031
30353330343531666135653064353863373461373962316437333162393663393736393963353739
61616233343665616132333135613063323933616661626535326133343130653064643635663164
64343066336661626161333662316361353837663765313662633565373361623738653663656263
31306336346664363964363636363061633131643236353831656466313563303133623134396439
6161626230303762626433633438316333353964326635343364
```

입력한 것을 확인하려면 다음과 같이 `ansible-vault view`를 이라는 명령을 사용하면 됩니다. `Vault password:`은 앞서 입력한 것은 사용하면 됩니다. 전 앞에서 설정한 `1234`을 입력했습니다. `Vault password: `을 입력하고 파일 내용이 왔지만, 다음 과정으로 넘어가지 못한다면, `esc` 키를 누르신 다음, `q!`을 입력하신 다음 `return`키를 누르시면 됩니다.

```bash
❯ ansible-vault view encrypt_hosts
Vault password: 
```

자 이제 암호화한 `encrypt_hosts` 인벤토리 파일을 사용해봅시다. 사용하는 방법은 다음과 같습니다. `--ask-vault-password`은 암호화 비밀번호를 요청하라는 옵션입니다.

```bash
ansible all -m ping --ask-vault-password -i encrypt_hosts
```

결과는 아래와 같이 앞에서 나왔던 결과랑 똑같습니다.

```bash
❯ ansible all -m ping --ask-vault-password -i encrypt_hosts
Vault password: 
lopes.hufs.ac.kr | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

어짜피 `SSH password: ` 비번을 치는 것이나, `Vault password: ` 비번을 치는 것이 매한가지 일이 아니냐고 반문하실 수도 있습니다. 그러나 `ansible-vault`를 사용하면 많은 것을 암호화할 수 있으며, 단 한 번의 입력으로 많은 내용을 처리할 수 있으며, 만약 파일이 유츌되어도 엔서블을 이용하여 관리하고 있는 서버에 대한 보안 문제에 대해서도 어느 정도 안심할 수 있습니다.
