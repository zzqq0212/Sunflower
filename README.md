Table of Contents
- [1. Linux Kernel Enriched Corpus Contructed by  leveraging exploits and PoCs for Fuzzers](#1-linux-kernel-enriched-corpus-for-fuzzers)
  - [1.1. Using Enriched corpus with Syzkaller](#11-using-enriched-corpus-with-syzkaller)
  <!-- - [1.2. Using Enriched corpus   with HEALER](#12-using-enriched-corpus-with-healer) -->
  <!-- - [1.2. Citing](#13-citing) -->
  - [1.2. Corpus Construction](#14-diy)
    - [1.2.1. Collecting exploits Manually](#141-fetching-corpus-manually)
    - [1.2.2. Generating corpus.db File](#142-generating-corpusdb-file)
  - [1.3. Results](#16-results)
    - [1.3.1. Coverage over time](#161-coverage-over-time)
    - [1.3.2. CVEs:](#164-cves)
    - [1.3.3. New Bugs Reported:](#165-new-bugs-reported)
    <!-- - [1.4.6. More bugs discovered (includes bugs that were found sooner than syzbot \& bugs undiscovered by syzbot)](#166-more-bugs-discovered-includes-bugs-that-were-found-sooner-than-syzbot--bugs-undiscovered-by-syzbot) -->


# 1. Linux Kernel Enriched Corpus for Fuzzers

Documentation for using and generating the Enriched corpus provided here.

<!-- For more questions, feel free to email [Palash Oswal](https://oswalpalash.com) or [Rohan Padhye](https://rohan.padhye.org). -->


## 1.1. Using Enriched corpus with Syzkaller

The latest copy of the Corpus file [corpus.db](https://github.com/zzqq0212/Sunflower/releases/download/latest/corpus.db) is available in the releases for this repository. 

Download it to [syzkaller](https://github.com/google/syzkaller) workdir and start syzkaller.
```
mkdir workdir
cd workdir
wget https://github.com/zzqq0212/Sunflower/releases/download/latest/corpus.db
```

## 1.2. Corpus Construction

### 1.2.1. Collecting expoloits tracefiles

you can show the files folder that contains the collected exploits's tracefile by converting the exploits's compiling and executing.

### 1.2.2. Generating corpus.db File

you can use syz-db.go pack from syzkaller when you have a collection of syz programs that need to be converted to a syzkaller comptaible corpus.db file.

## 1.3. Results

1.Host Machine System Configuration
   ```
  CPU: 128 core
  Memory: 32GB
  Ubuntu 22.04.4 LTS jammy 
 ```
2.Virtal Machine Resource Configration
  ```
  2 core CPU + 2GB Memory
  ```
3.Test targeted Linux Version

We chose Linux kernel v5.15, v6.1, v6.3.4, and v6.5 as our test kernel targets. In detail, the Linux v6.5 is the latest release version when we were conducting experiments. Each version of the kernel uses the same compilation configuration, while KCOV and KASAN options are enabled in order to collect code coverage and detect memory errors. When setting up the KCSAN configuration, the same configuration is  used in the control test. 


### 1.3.1. Coverage over time 
10 VM (2vCPU and 2G RAM) average for 48 hours.

![image](https://github.com/zzqq0212/Sunflower/blob/main/assets/coverage-sunflower.png)  
  
<!-- ![image](https://github.com/cmu-pasta/linux-kernel-enriched-corpus/assets/6431196/ce9a8da6-870d-4004-afbe-3951a7d78b82) -->
<!-- 
### 1.4.2. Unique Crashes over time
1 VM (2vCPU and 4G RAM) for 24 hours.  
![image](https://github.com/cmu-pasta/linux-kernel-enriched-corpus/assets/6431196/7a4cdbd5-1d70-49a5-8633-21fc17156f45)  
8 VM (2vCPU and 4G RAM) for 24 hours.  
![image](https://github.com/cmu-pasta/linux-kernel-enriched-corpus/assets/6431196/83ba1e65-475f-42e8-a7ee-a15329e227aa)


### 1.4.3. Total Crashes over time
1 VM (2vCPU and 4G RAM) for 24 hours.  
![image](https://github.com/cmu-pasta/linux-kernel-enriched-corpus/assets/6431196/c67bc7b8-6574-4208-8caa-e509eb5900b7)  
8 VM (2vCPU and 4G RAM) for 24 hours.  
![image](https://github.com/cmu-pasta/linux-kernel-enriched-corpus/assets/6431196/86ff9b82-68f4-4311-a453-92311cc5223b) -->


### 1.3.2. CVEs:

* [CVE-2023-40791](https://nvd.nist.gov/vuln/detail/CVE-2023-40791)
* [CVE-2023-40793](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2023-40793)

### 1.3.3. New Bugs Reported Detailed Description:
| Modules           | Versions | Locations                                       | Bug Types      | Bug Descriptions                                                   |
|-------------------|----------|-------------------------------------------------|----------------|--------------------------------------------------------------------|
| fs/ext4           | v6.5     | ext4\_es\_insert\_extent                        | use-after-free | incorrect read task access causes use-after-free error             |
| arch/x86/kvm      | v6.3.4   | kvm\_vcpu\_reset                                | logic error    | kvm virtual cpu reset process causes error                         |
| net/8021q         | v6.5     | unregister\_vlan\_dev                           | logic error    | invalid opcode at net/8021q/vlan.c causes error                    |
| fs/dcache         | v6.3.4   | \_\_d\_add                                      | data race      | contention with read operation at \_\_d\_add function              |
| net/ipv4          | v6.3.4   | \_\_netlink\_create                             | memory leak    | unreleased memory objects causes leaks                             |
| net/ipv6          | v6.5     | ip6\_tnl\_exit\_batch\_net                      | logic error    | unregistering process of network devices results error             |
| mm/slab           | v6.3.4   | cache\_grow\_begin                              | memory leak    | unreferenced object causes memory leak                             |
| net/can           | v6.5     | raw\_setsockopt                                 | deadlock       | circular lock acquisition results in a deadlock                    |
| fs/proc           | v6.3.4   | proc\_pid\_status                               | data race      | data race invoking tasks causes system hang                        |
| mm/memory         | v6.3.4   | copy\_page\_range                               | data race      | unsynchronized access to shared data by threads results in error   |
| fs/dcache         | v6.3.4   | dentry\_unlink\_inode                           | data race      | file unlinking operations results error                            |
| fs/proc           | v6.3.4   | task\_dump\_owner                               | data race      | unsynchronized thread access to shared data leads to error         |
| fs/f2fs           | v6.3.4   | f2fs\_truncate\_data\_blocks\_range             | out-of-bounds  | incorrect read operation results out-of-bounds error               |
| fs/buffer         | v6.3.4   | submit\_bh\_wbc                                 | logic error    | incorrect write operation causes invalid opcode error              |
| fs/xfs            | v6.3.4   | xfs\_btree\_lookup\_get\_block                  | logic error    | invalid memory access results error                                |
| drivers/block/aoe | v6.3.4   | aoecmd\_cfg                                     | logic error    | jump labels operation causes kernel hang error                     |
| mm/mmap           | v6.3.4   | do\_vmi\_munmap                                 | logic error    | incorrect instruction execution causes kernel panic                |
| fs/udf            | v6.3.4   | udf\_finalize\_lvid                             | use-after-free | invoking deprecated mand mount option results use-after-free bug   |
| drivers/block     | v6.5     | sock\_xmit                                      | use-after-free | incorrect memory deallocation causes the use-after-free error      |
| kernel/sched      | v6.3.4   | run\_rebalance\_domains                         | logic error    | incorrect scheduling operation causes RCU (Read-Copy-Update) error |
| block/bdev        | v6.3.4   | blkdev\_flush\_mapping                          | dead lock      | incorrect filesystem operation causes error                        |
| mm/swap           | v6.3.4   | folio\_batch\_move\_lru / folio\_mark\_accessed | data race      | unsynchronized thread access to shared data leads to error         |
| lib/find_bit      | v6.3.4   | \_find\_first\_bit                              | data race      | unsynchronized thread access to shared data causes error           |
| mm/filemap        | v6.3.4   | filemap\_fault / page\_add\_file\_rmap          | data race      | inconsistent read and write operations results data race           |
| fs/ext4           | v6.3.4   | ext4\_do\_writepages / ext4\_mark\_iloc\_dirty  | data race      | unsynchronized thread access to shared data causes race contention |