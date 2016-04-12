---
layout: post
title: Who are you, Server?
categories: [NHN Entertainment DevDays]
tags: [Linux, Server, 명령어]
comments: true
description: Server! 너 정체가 뭐야
---

## Linux에서 서버 정보확인 ##

### 서버 모델확인 ###

```
dmidecode | grep Name
```

<br>

### 시리얼 번호확인 ###

```
dmidecode | grep Serial
```

<br>

### CPU ###

```
cat /proc/cpuinfo
dmesg | grep CPU
```

<br>

### 메모리 ###

```
cat /proc/meminfo
dmesg | grep memory
```

<br>

### 기타 하드웨어 장비확인 ###

##### 전체검색 #####

```
cat /etc/sysconfig/hwconf
```

##### 상세검색 #####

```
cat /etc/sysconfig/hwconf | grep Mouse
cat /etc/sysconfig/hwconf | grep Graphic
cat /etc/sysconfig/hwconf | grep Net
```

<br>

### Kernel 확인 ###

```
cat /proc/sys/kernel/osrelease
uname -a
cat /proc/version
```

<br>

### 운영체제 확인 ###

```
cat /etc/issue.net
cat /etc/redhat-release
```

<br>

### RAID 정보확인 ###

```
cat /proc/mdstat
```

<br>

### HDD 용량확인 ###

```
df -h
```

<br>

### HDD 확인 ###

```
dmesg | grep SCSI
```

<br>

### 하드디스크 정보(SCSI) ###

```
cat /proc/scsi/scsi
```

<br>

### 네트워크 정보 ###

```
cat /proc/net/netlink
```

<br>

### 32비트 / 64비트 구분법 ###

##### OS #####

```
getconf LONG_BIT
```
```
uname -mpi
```

##### CPU #####

```
cat /proc/cpuinfo
```

flags값에 **IM**이라고 있으면 64비트