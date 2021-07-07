# Final Lab

### System OS : CentOs - RHEL 계열(레드헷)

- Centos 7.9

## System Configuration Checks

1. Check `vm.swappiness` on all your nodes
    - 메모리공간이 부족할 때 하드디스크 공간을 메모리 처럼 사용하는 것(스왑 영역)
    - 스왑을 빈번하게 하면 성능은 하락하는 대신, 안정적(OOM 방지?)
    - vm.swappiness = 1 이면 메모리 스왑을 최소화 하는 것임.
    - /proc/sys/vm/swapiness 에서 값 설정 가능 (재부팅 시 초기화됨)
    - [centos@ip-10-0-0-211 /]$ sudo vi /etc/sysctl.conf - 영구적 vm.swappiness = 1
2. Show mount attributes of your volumes
    - df -h : 파일시스템의 용량 및 마운트 정보 확인
    - fdisk -l : 현재 장착되어 있는 디스크 정보 확인

    ```sql
    [centos@ip-10-0-0-211 dev]$ df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        7.7G     0  7.7G   0% /dev
    tmpfs           7.7G     0  7.7G   0% /dev/shm
    tmpfs           7.7G   17M  7.7G   1% /run
    tmpfs           7.7G     0  7.7G   0% /sys/fs/cgroup
    /dev/nvme0n1p1  100G  854M  100G   1% /
    tmpfs           1.6G     0  1.6G   0% /run/user/1000

    [centos@ip-10-0-0-211 /]$ sudo fdisk -l
    Disk /dev/nvme0n1: 107.4 GB, 107374182400 bytes, 209715200 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk label type: dos
    Disk identifier: 0x000b0d11

            Device Boot      Start         End      Blocks   Id  System
    /dev/nvme0n1p1   *        2048   209715166   104856559+  83  Linux
    ```

    - NVMe, SATA : SSD 인데 방식의 차이인 듯..
3. Ext-based volumes, list the reserve space setting - XFS volumes do not support reserve space
    - Extended File System : ext2, ext3, ext4, xfs 등 리눅스에서 사용하는 파일시스템인 듯.

```sql
 [centos@ip-10-0-0-211 /]$ mount
sysfs on /sys type sysfs (rw,nosuid,nodev,noexec,relatime,seclabel)
proc on /proc type proc (rw,nosuid,nodev,noexec,relatime)
devtmpfs on /dev type devtmpfs (rw,nosuid,seclabel,size=8013892k,nr_inodes=2003473,mode=755)
securityfs on /sys/kernel/security type securityfs (rw,nosuid,nodev,noexec,relatime)
tmpfs on /dev/shm type tmpfs (rw,nosuid,nodev,seclabel)
devpts on /dev/pts type devpts (rw,nosuid,noexec,relatime,seclabel,gid=5,mode=620,ptmxmode=000)
tmpfs on /run type tmpfs (rw,nosuid,nodev,seclabel,mode=755)
tmpfs on /sys/fs/cgroup type tmpfs (ro,nosuid,nodev,noexec,seclabel,mode=755)
cgroup on /sys/fs/cgroup/systemd type cgroup (rw,nosuid,nodev,noexec,relatime,seclabel,xattr,release_agent=/usr/lib/systemd/systemd-cgroups-agent,name=systemd)
pstore on /sys/fs/pstore type pstore (rw,nosuid,nodev,noexec,relatime)
cgroup on /sys/fs/cgroup/hugetlb type cgroup (rw,nosuid,nodev,noexec,relatime,seclabel,hugetlb)
cgroup on /sys/fs/cgroup/cpu,cpuacct type cgroup (rw,nosuid,nodev,noexec,relatime,seclabel,cpuacct,cpu)
cgroup on /sys/fs/cgroup/perf_event type cgroup (rw,nosuid,nodev,noexec,relatime,seclabel,perf_event)
cgroup on /sys/fs/cgroup/blkio type cgroup (rw,nosuid,nodev,noexec,relatime,seclabel,blkio)
cgroup on /sys/fs/cgroup/pids type cgroup (rw,nosuid,nodev,noexec,relatime,seclabel,pids)
cgroup on /sys/fs/cgroup/memory type cgroup (rw,nosuid,nodev,noexec,relatime,seclabel,memory)
cgroup on /sys/fs/cgroup/net_cls,net_prio type cgroup (rw,nosuid,nodev,noexec,relatime,seclabel,net_prio,net_cls)
cgroup on /sys/fs/cgroup/freezer type cgroup (rw,nosuid,nodev,noexec,relatime,seclabel,freezer)
cgroup on /sys/fs/cgroup/cpuset type cgroup (rw,nosuid,nodev,noexec,relatime,seclabel,cpuset)
cgroup on /sys/fs/cgroup/devices type cgroup (rw,nosuid,nodev,noexec,relatime,seclabel,devices)
configfs on /sys/kernel/config type configfs (rw,relatime)
/dev/nvme0n1p1 on / type xfs (rw,relatime,seclabel,attr2,inode64,noquota)
rpc_pipefs on /var/lib/nfs/rpc_pipefs type rpc_pipefs (rw,relatime)
selinuxfs on /sys/fs/selinux type selinuxfs (rw,relatime)
systemd-1 on /proc/sys/fs/binfmt_misc type autofs (rw,relatime,fd=28,pgrp=1,timeout=0,minproto=5,maxproto=5,direct,pipe_ino=11657)
debugfs on /sys/kernel/debug type debugfs (rw,relatime)
hugetlbfs on /dev/hugepages type hugetlbfs (rw,relatime,seclabel)
mqueue on /dev/mqueue type mqueue (rw,relatime,seclabel)
tmpfs on /run/user/1000 type tmpfs (rw,nosuid,nodev,relatime,seclabel,size=1607580k,mode=700,uid=1000,gid=1000)
```

- /dev/nvme0n1p1 on/ type xfs : RHEL 7 부터 XFS 기본 으로 사용하고 있다고 하는데 위 mount 명령어로 확인 가능하다.
- XFS volumes do not support reserve space 이니 예약공간은 따로 신경 안써도 될듯.

4. Disable transparent hugepage support

- TLB 의 페이징을 갯수는 줄이고 사이즈를 늘리는 것?
- 문제가 있어서 끄는게 좋다고 함(더 찾고 정리할 것.)

```sql
cat /sys/kernel/mm/transparent_hugepage/enabled
```

5. 네트워크 인터페이스 config 명령 - ifconfig

6. Host 설정

7. Name Service Cache Demon : DNS 찾을 때 캐싱 해주는 서비스 인듯

8. NTPD : 서버 시간 동기화 해주는 것? : yum install, service start

## Cloudera Manager Install Lab

sudo yum install wget