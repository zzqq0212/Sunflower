Table of Contents
- [1. Linux Kernel Enriched Corpus Contructed by  leveraging exploits and PoCs for Fuzzers](#1-linux-kernel-enriched-corpus-for-fuzzers)
  - [1.1. Using Enriched corpus with Syzkaller](#11-using-enriched-corpus-with-syzkaller)
  <!-- - [1.2. Using Enriched corpus with HEALER](#12-using-enriched-corpus-with-healer) -->
  <!-- - [1.2. Citing](#13-citing) -->
  - [1.2. Corpus Construction](#14-diy)
    - [1.2.1. Collecting expoloits Manually](#141-fetching-corpus-manually)
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

### 1.2.1. Collecting expoloits Manually

<!-- [collect.py](./collect.py) : currently fetches `syz` reproducers from all fixed Linux Kernel upstream crashes in [syzbot](https://syzkaller.appspot.com/upstream/fixed).

This script can be modified to fetch corpus programs from other kernel versions and to fetch "C" Programs instead of `syz` reproducers. -->

### 1.2.2. Generating corpus.db File

<!-- If you have a collection of `syz` programs that need to be converted to a syzkaller comptaible `corpus.db` file, you can use `syz-db.go pack` from syzkaller.

An implementation of this is available in the GitHub actions workflow [here](./.github/workflows/corpusgen.yml). -->

<!-- ## 1.3. Corpus Files Available  -->
<!-- [![Up to date Reproducers](https://github.com/cmu-pasta/linux-kernel-enriched-corpus/actions/workflows/fetch.yml/badge.svg)](https://github.com/cmu-pasta/linux-kernel-enriched-corpus/actions/workflows/fetch.yml) -->
<!-- 1. [corpus.db](./corpus.db) : Enriched Corpus (version 0 for `syz-db`) -->
<!-- 2. [ci-qemu-upstream-corpus.db](./ci-qemu-upstream-corpus.db) : Corpus Obtained from Syz-CI (Google's syzbot) (version latest per `syz-db`)
3. [enriched-ci-qemu-upstream-corpus.db](./enriched-ci-qemu-upstream-corpus.db) : Enriched Version of the Corpus Obtained from Syzbot (version 0 for `syz-db`)
A detailed comparison of the three is provided in the [research document](https://www.proquest.com/docview/2812311865). More documentation to follow. -->

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

<!-- * [CVE-2023-26544](https://www.cve.org/CVERecord?id=CVE-2023-26544)
* [CVE-2023-26605](https://www.cve.org/CVERecord?id=CVE-2023-26605)
* [CVE-2023-26606](https://www.cve.org/CVERecord?id=CVE-2023-26606)
* [CVE-2023-26607](https://www.cve.org/CVERecord?id=CVE-2023-26607) -->

### 1.3.3. New Bugs Reported:
<!-- * https://lkml.org/lkml/2023/2/20/128 -->
<!-- * https://lkml.org/lkml/2023/2/20/773
* https://lkml.org/lkml/2023/2/20/785
* https://lkml.org/lkml/2023/2/20/860
* https://lkml.org/lkml/2023/2/21/1353
* https://lkml.org/lkml/2023/2/22/3 
* https://lkml.org/lkml/2023/4/13/39
* https://lkml.org/lkml/2023/4/13/45
* https://lkml.org/lkml/2023/4/13/1317
* https://lkml.org/lkml/2023/4/13/1350
* https://lkml.org/lkml/2023/6/3/90
* https://lkml.org/lkml/2023/6/3/91
* https://lkml.org/lkml/2023/6/3/95
* https://lkml.org/lkml/2023/6/3/98
* https://lkml.org/lkml/2023/6/3/146
* https://lkml.org/lkml/2023/6/3/148
[and many more](https://twitter.com/oswalpalash/status/1627776397828853760) -->

### 1.4.6. More bugs discovered (includes bugs that were found sooner than syzbot & bugs undiscovered by syzbot)

| Title                                                     | Found in #Instance | Date of Discovery | Branch (if found by syzbot)| New/Earlier |
|-----------------------------------------------------------|--------------------|-------------------|--------------------|------------------|
| UBSAN: shift-out-of-bounds in ntfs_fill_super              | 10                 | 2/28/23           | 6.2.0              | Yes              |
| UBSAN: shift-out-of-bounds in nilfs_load_super_block       | 10                 | 10/25/22          | net-6.1-rc3-1       | Yes              |
| UBSAN: shift-out-of-bounds in dbAllocAG                    | 10                 | 9/28/22           | 6.0.0-rc7          | Yes              |
| KASAN: use-after-free Read in si470x_int_in_callback       | 10                 | N/A               | regression         | Yes              |
| KASAN: use-after-free Read in run_unpack                   | 10                 | N/A               | new                | Yes              |
| KASAN: use-after-free Read in ntfs_trim_fs                  | 10                 | N/A               | new                | Yes              |
| KASAN: slab-out-of-bounds Read in hdr_find_e                | 10                 | N/A               | new                | Yes              |
| KASAN: out-of-bounds Read in leaf_paste_entries             | 10                 | N/A               | regression         | Yes              |
| KASAN: null-ptr-deref Write in f2fs_stop_discard_thread     | 10                 | N/A               | new                | Yes              |
| KASAN: slab-out-of-bounds Read in ntfs_attr_find             | 8                  | N/A               | new/regression     | Yes              |
| KASAN: use-after-free Read in em28xx_init_extension         | 6                  | 3/30/22           | 5.17.0-syzkaller-   | Yes              |
| KASAN: use-after-free Read in do_garbage_collect            | 6                  | 11/13/22          | 6.1.0-rc4-syzkaller | Yes              |
| KASAN: slab-out-of-bounds Read in do_garbage_collect         | 6                  | 11/13/22          | 6.1.0-rc4-syzkaller | Yes              |
| KASAN: use-after-free Read in cfusbl_device_notify           | 5                  | 11/12/22          | 5.18.0-rc1-syzkaller| Yes              |
| KASAN: use-after-free Read in notifier_call_chain            | 4                  | 11/18/22          | 5.18.0-rc3-         | Yes              |
| KASAN: use-after-free Write in nr_release                    | 3                  | N/A               | regression         | Yes              |
| KASAN: use-after-free Read in task_work_run                  | 2                  | N/A               | new                | Yes              |
| KASAN: use-after-free Read in inode_cgwb_move_to_attached    | 2                  | N/A               | new                | Yes              |
| KASAN: use-after-free Read in __fib6_clean_all               | 2                  | N/A               | new                | Yes              |
| KASAN: use-after-free Read in tcp_retransmit_timer           | 1                  | N/A               | regression         | Yes              |
| KASAN: use-after-free Read in nexthop_flush_dev              | 1                  | N/A               | new                | Yes              |
| KASAN: use-after-free Read in lock_sock_nested               | 1                  | 9/1/20            | 5.9.0-rc3          | Yes              |
| KASAN: slab-out-of-bounds Read in mi_enum_attr               | 1                  | N/A               | new                | Yes              |