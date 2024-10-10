<!-- Table of Contents
- [1. Linux Kernel Enriched Corpus Contructed by leveraging exploits and PoCs for Fuzzers](#1-linux-kernel-enriched-corpus-for-fuzzers)
  - [1.1. Using Enriched corpus with Syzkaller](#11-using-enriched-corpus-with-sunflower)
  - [1.2. Corpus Construction](#12-corpus-construction)
    - [1.2.1. Collecting exploits and pocs](#121-collecting-expoloits-and-pocs)
    - [1.2.2. Generating corpus File](#122-generating-corpus-file)
  - [1.3. Experiment Results](#13-experiment-results)
    - [1.3.1. Coverage over time](#131-coverage-over-time)
    - [1.3.2. CVEs:](#132-cves)
    - [1.3.3. New Bugs Reported:](#133-new-bugs-reported) -->

# Sunflower
Sunflower is an initial corpus generator that leverages existing exploits and proof-of-concept
examples that specifically designed to meet the criticalrequirements of industry deployments by facilitating the construction of a high-quality seed corpus based on bugs found in the wild. By collecting and analyzing numerous real-world exploits responsible for kernel vulnerabilities, the tool extracts essential system call sequences while also rectifying execution dependency errors.

we will provide installation steps that how to build and use sunflower for kernel fuzzing here.

## Build

The latest copy of the Corpus file [corpus.db](https://github.com/zzqq0212/Sunflower/releases/download/latest/corpus.db) is available in the releases for this repository. Meanwhile, The __explotis-datas__ folder contains the exploits raw data. The __files__ folder contains some trace examples that we obtained by executing the compiled expoits program with the __strace__ tool.
<!-- We use syzkaller for kernel fuzzing. [Syzkaller](https://github.com/google/syzkaller) is an unsupervised coverage-guided kernel fuzzer. -->

### Install Prerequisites

Basic dependencies install (take for example on debain or ubuntu):
``` bash
sudo apt update
sudo apt install make gcc flex bison libncurses-dev libelf-dev libssl-dev
```
### GCC

If your distro's GCC is older, it's preferable to get the latest GCC from [this](https://gcc.gnu.org/) list. Download and unpack into `$GCC`, and you should have GCC binaries in `$GCC/bin/`

>**Ubuntu 20.04 LTS**: You can ignore this section. GCC is up-to-date.

``` bash
ls $GCC/bin/
# Sample output:
# cpp     gcc-ranlib  x86_64-pc-linux-gnu-gcc        x86_64-pc-linux-gnu-gcc-ranlib
# gcc     gcov        x86_64-pc-linux-gnu-gcc-9.0.0
# gcc-ar  gcov-dump   x86_64-pc-linux-gnu-gcc-ar
# gcc-nm  gcov-tool   x86_64-pc-linux-gnu-gcc-nm
```
### Install golang
We use golang in Sunflower, so make sure golang is installed before build Sunflower.

``` bash
wget https://dl.google.com/go/go1.22.4.linux-amd64.tar.gz
tar -xf go1.22.4.linux-amd64.tar.gz
mv go goroot
mkdir gopath
export GOPATH=`pwd`/gopath
export GOROOT=`pwd`/goroot
export PATH=$GOPATH/bin:$PATH
export PATH=$GOROOT/bin:$PATH
```
### Prepare Kernel
In here we use Linux Kernel(Enable Real time Config) v6.5 as an example.
First we need to have have a compilable Linux
```bash
# Download linux kernel 
git clone https://github.com/torvalds/linux
cd linux
export Kernel=$pwd
git checkout -f 2dde18c

After we have the Linux Kernel, we need to compile it.
``` bash
# Modified configuration
make defconfig  
make kvmconfig
vim .config
```

``` vim
# modified configuration
CONFIG_KCOV=y 
CONFIG_DEBUG_INFO=y 
CONFIG_KASAN=y
CONFIG_KASAN_INLINE=y 
CONFIG_CONFIGFS_FS=y
CONFIG_SECURITYFS=y
```

make it!
``` bash
make olddefconfig
make -j32
```

Now we should have vmlinux (kernel binary) and bzImage (packed kernel image):
```bash
$ ls $KERNEL/vmlinux
$KERNEL/vmlinux
$ ls $KERNEL/arch/x86/boot/bzImage
$KERNEL/arch/x86/boot/bzImage
```

### Prepare Image
```bash 
sudo apt-get install debootstrap 
export IMAGE=$pwd
cd $IMAGE/
wget https://raw.githubusercontent.com/google/syzkaller/master/tools/create-image.sh -O create-image.sh
chmod +x create-image.sh
./create-image.sh
```
Now we have a image stretch.img and a public key.

### Compile sunflower
``` bash 
make 
mkdir workdir
cd workdir
wget https://github.com/zzqq0212/Sunflower/releases/download/latest/corpus.db
```
As the result compiled binaries should appear in the bin/ dir.

### Ready QEMU
Install QEMU:
``` bash
sudo apt-get install qemu-system-x86
```
Make sure the kernel boots and sshd starts:
``` bash 
qemu-system-x86_64 \
	-m 2G \
	-smp 2 \
	-kernel $KERNEL/arch/x86/boot/bzImage \
	-append "console=ttyS0 root=/dev/sda earlyprintk=serial net.ifnames=0" \
	-drive file=$IMAGE/stretch.img,format=raw \
	-net user,host=10.0.2.10,hostfwd=tcp:127.0.0.1:10021-:22 \
	-net nic,model=e1000 \
	-enable-kvm \
	-nographic \
	-pidfile vm.pid \
	2>&1 | tee vm.log
```
see if ssh works
``` bash 
ssh -i $IMAGE/stretch.id_rsa -p 10021 ``-o "StrictHostKeyChecking no" 
```

To kill the running QEMU instance press Ctrl+A and then X or run:
``` bash
kill $(cat vm.pid)
```
If QEMU works, the kernel boots and ssh succeeds, we can shutdown QEMU and try to run __sunflower__.

Now we can start to prepare a __config.json__ file.
``` json 
{
        "target": "linux/amd64",
        "http": "127.0.0.1:56295",
        "workdir": "./workdir",
        "cover": false,
        "kernel_obj": "$(Kernel)/vmlinux",
        "image": "$(image)/stretch.img",
        "sshkey": "$(image)/stretch.id_rsa",
        "syzkaller": "$pwd",
        "procs": 2,
        "type": "qemu",
        "vm": {
                "count": 2,
                "kernel": "$(Kernel)/bzImage",
                "cpu": 2,
                "mem": 4096
        }

```
Now we can run it.

``` bash
./bin/syz-manager -config config.json
```
The `syz-manager` process will wind up VMs and start fuzzing in them.
Found crashes, statistics and other information is exposed on the HTTP address specified in the manager config.

<!-- ## Corpus Construction

### 1.2.1. Collecting expoloits and Pocs 

We use

you can show the `files` folder that contains the collected exploits's tracefile by converting the exploits's compiling and executing.

### 1.2.2. Generating corpus File

you can use `syz-db` pack corpus.db when you have a collection of syzlang programs that need to be converted to a syzkaller comptaible `corpus.db` file.
```bash
cd tools/syz-db
go build syz-db.go
./tools/syz-db/syz-db pack [tracedir]
``` -->

##  Experiment Results

### Host Machine System Configuration
```bash
  CPU: 128 core
  Memory: 32GB
  Ubuntu 22.04.4 LTS jammy 
```

### Virtal Machine Resource Configration
``` bash
  2 core CPU + 2GB Memory
```

### Test targeted Linux Version

We chose Linux kernel v5.15, v6.1, v6.3.4, and v6.5 as our test kernel targets. In detail, the Linux v6.5 is the latest release version when we were conducting experiments. Each version of the kernel uses the same compilation configuration, while KCOV and KASAN options are enabled in order to collect code coverage and detect memory errors. When setting up the KCSAN configuration, the same configuration is  used in the control test. 


### Coverage over time 

10 VM (2vCPU and 2G RAM) average for 48 hours.

![image](https://github.com/zzqq0212/Sunflower/blob/main/assets/coverage-sunflower.png)  


### CVEs:

* [CVE-2023-40791](https://nvd.nist.gov/vuln/detail/CVE-2023-40791)
* [CVE-2023-40792](https://www.cve.org/CVERecord?id=CVE-2023-40792)
* [CVE-2023-40793](https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2023-40793)


### New Bugs Reported:
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
