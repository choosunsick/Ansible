# 서버에 단독으로 스파크 설치하기

현재 폴더 파일 확인합니다.

```bash
❯ ls  
Spark_Stand_Alone.yml ansible.cfg           doc.md                hosts
```

현재 `hosts`을 보시면, `three.local`만 설치하도록 되어있습니다. 이 이름을 바꾸시면 다른 곳에서도 설치할 수 있습니다. 또한 아래 `1.local`, `2.local`앞에 있는 `;`을 제거해서 주석처리를 해체하면 노드 3개에도 한꺼번에 설치할 수 있습니다.

```ini
[all]
three.local
;1.local
;2.local
```

엔서블 파일로 스파크 설치!

```bash
ansible-playbook Spark_Stand_Alone.yml -i hosts
```

설치화면

```bash
❯ ansible-playbook Spark_Stand_Alone.yml -i hosts

PLAY [all] ******************************************************************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************************************
ok: [three.local]

TASK [spark configuration] **************************************************************************************************************************************
[WARNING]: Consider using the unarchive module rather than running 'tar'.  If you need to use command because unarchive is insufficient you can add 'warn:
false' to this command task or set 'command_warnings=False' in ansible.cfg to get rid of this message.
changed: [three.local]

TASK [del spark file] *******************************************************************************************************************************************
ok: [three.local]

TASK [Creates directory] ****************************************************************************************************************************************
ok: [three.local]

TASK [Copy files from foo to bar] *******************************************************************************************************************************
ok: [three.local]

TASK [del /opt/spark-3.0.2-bin-hadoop2.7] ***********************************************************************************************************************
changed: [three.local]

TASK [chown spark] **********************************************************************************************************************************************
[WARNING]: Consider using the file module with owner rather than running 'chown'.  If you need to use command because file is insufficient you can add 'warn:
false' to this command task or set 'command_warnings=False' in ansible.cfg to get rid of this message.
changed: [three.local]

PLAY RECAP ******************************************************************************************************************************************************
three.local                : ok=7    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

시험 작동은 다음과 같이 합니다.

```bash
cd /opt/spark
./bin/run-example SparkPi 10
```

다음은 작동하는 화면입니다. 너무 길어서 가운데를 생략했습니다. `Pi is roughly 3.141879141879142`가 나온 것을 보면 제대로 스파크가 설치된 것 같습니다.

```log
21/03/24 15:45:06 WARN Utils: Your hostname, three resolves to a loopback address: 127.0.1.1; using 192.168.0.16 instead (on interface eth0)
21/03/24 15:45:06 WARN Utils: Set SPARK_LOCAL_IP if you need to bind to another address
21/03/24 15:45:09 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
Using Spark's default log4j profile: org/apache/spark/log4j-defaults.properties
21/03/24 15:45:12 INFO SparkContext: Running Spark version 3.0.2

...

Pi is roughly 3.141879141879142
21/03/24 15:45:31 INFO SparkUI: Stopped Spark web UI at http://192.168.0.16:4040
21/03/24 15:45:31 INFO MapOutputTrackerMasterEndpoint: MapOutputTrackerMasterEndpoint stopped!
21/03/24 15:45:31 INFO MemoryStore: MemoryStore cleared
21/03/24 15:45:31 INFO BlockManager: BlockManager stopped
21/03/24 15:45:31 INFO BlockManagerMaster: BlockManagerMaster stopped
21/03/24 15:45:31 INFO OutputCommitCoordinator$OutputCommitCoordinatorEndpoint: OutputCommitCoordinator stopped!
21/03/24 15:45:31 INFO SparkContext: Successfully stopped SparkContext
21/03/24 15:45:31 INFO ShutdownHookManager: Shutdown hook called
21/03/24 15:45:31 INFO ShutdownHookManager: Deleting directory /tmp/spark-8ddd8b76-eef9-40a9-b6c9-5c1b3f9948b4
21/03/24 15:45:31 INFO ShutdownHookManager: Deleting directory /tmp/spark-e6e04656-3d93-4796-96f6-903ab4abdfdf
```

## 현재 주의할 점

- 호스트 이름을 `3`과 같이 숫자로 시작하지 마세요. 오류발생!
- `ansible.cfg`파일이 있어야 엔서블 에러를 피할 수 있습니다.
- `Spark_Stand_Alone.yml`파일이 미숙한 점이 많아서 보완해야 합니다. 보완해야 할 점들이 주석으로 되어 있습니다.
