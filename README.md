# [ KASLD ] Kernel Address Space Layout Derandomization

A collection of various techniques to bypass KASLR and retrieve
the Linux kernel base virtual address on x86 / x86_64 architectures
as an unprivileged user.

The code is structed for easy re-use; however, leaked addresses
may need to be bit masked appropriately for the target kernel.

Various code snippets were taken from third-parties and may
have license restrictions. Refer to the reference URLs in the
comment headers available in each file for more information.

Android is not supported.


## Example Output

### Ubuntu 16.04 (x64)

```
$ ./kasld 
[ KASLD ] Kernel Address Space Layout Derandomization

Kernel release: 4.4.0-21-generic
Kernel version: #37-Ubuntu SMP Mon Apr 18 18:33:37 UTC 2016
Kernel arch:    x86_64

kernel base (arch default): ffffffff81000000

[.] checking /boot/config ...

[.] trying /proc/cmdline...

[.] trying /proc/kallsyms...
[-] kernel base not found in /proc/kallsyms

[.] trying /sys/kernel/slab/ ...
leaked init_net: ffffffff81ef3cc0
kernel base (possible): ffffffff81e00000
kernel base (possible): ffffffff81000000

[.] trying perf_event_open sampling ...
lowest leaked address: ffffffff81094f86
kernel base (likely): ffffffff81000000
kernel base (likely): ffffffff81000000

[.] trying syslog ...
leaked address: ffffffff820b2000
kernel base (likely): ffffffff81000000
kernel base (likely): ffffffff81000000

[.] trying 'pppd file /proc/kallsyms 2>&1' ...

[.] trying mincore info leak...
leaked address: ffffffff81220df0
kernel base (possible): ffffffff81200000
kernel base (possible): ffffffff81000000

[.] checking CPU TSX/RTM support ...
[-] CPU does not support TSX/RTM

[.] checking /sys/devices/system/cpu/vulnerabilities ...

```

### Ubuntu 12.04 (i686)

```
$ ./kasld 
[ KASLD ] Kernel Address Space Layout Derandomization

Kernel release: 3.2.0-23-generic-pae
Kernel version: #36-Ubuntu SMP Tue Apr 10 22:19:09 UTC 2012
Kernel arch:    i686

default.c: In function ‘get_kernel_addr_default’:
default.c:25:5: warning: large integer implicitly truncated to unsigned type [-Woverflow]
kernel base (arch default): c1000000

boot-config.c: In function ‘get_kernel_addr_cmdline’:
boot-config.c:37:5: warning: large integer implicitly truncated to unsigned type [-Woverflow]
[.] checking /boot/config ...
[.] Kernel appears to have been compiled without CONFIG_RELOCATABLE and CONFIG_RANDOMIZE_BASE
kernel base (likely): c1000000

cmdline.c: In function ‘get_kernel_addr_cmdline’:
cmdline.c:33:5: warning: large integer implicitly truncated to unsigned type [-Woverflow]
[.] trying /proc/cmdline...

[.] trying /proc/kallsyms...
kernel base (certain): c1000000

nf_conntrack.c:14:1: warning: large integer implicitly truncated to unsigned type [-Woverflow]
nf_conntrack.c:15:1: warning: large integer implicitly truncated to unsigned type [-Woverflow]
[-] unsupported: system is not 64-bit.

perf_event_open.c:19:1: warning: large integer implicitly truncated to unsigned type [-Woverflow]
perf_event_open.c:20:1: warning: large integer implicitly truncated to unsigned type [-Woverflow]
perf_event_open.c: In function ‘get_kernel_addr_perf’:
perf_event_open.c:51:12: warning: missing braces around initializer [-Wmissing-braces]
perf_event_open.c:51:12: warning: (near initialization for ‘self.sysname’) [-Wmissing-braces]
[.] trying perf_event_open sampling ...
lowest leaked address: c106f6aa
kernel base (likely): c1000000
kernel base (likely): c1000000

syslog.c:19:1: warning: large integer implicitly truncated to unsigned type [-Woverflow]
syslog.c:20:1: warning: large integer implicitly truncated to unsigned type [-Woverflow]
[.] trying syslog ...
[-] unsupported: system is not 64-bit.

[.] trying 'pppd file /proc/kallsyms 2>&1' ...
kernel base (certain): c1000000

mincore.c:13:1: warning: large integer implicitly truncated to unsigned type [-Woverflow]
mincore.c:14:1: warning: large integer implicitly truncated to unsigned type [-Woverflow]
mincore.c: In function ‘get_kernel_addr_mincore’:
mincore.c:34:11: warning: large integer implicitly truncated to unsigned type [-Woverflow]
mincore.c:52:17: warning: large integer implicitly truncated to unsigned type [-Woverflow]
mincore.c:59:5: warning: large integer implicitly truncated to unsigned type [-Woverflow]
[.] trying mincore info leak...
[-] mmap(): Invalid argument

[.] checking CPU TSX/RTM support ...
[-] CPU does not support TSX/RTM

[.] checking /sys/devices/system/cpu/vulnerabilities ...

```

### RHEL 7.6 (x64)

```
$ ./kasld 
[ KASLD ] Kernel Address Space Layout Derandomization

Kernel release: 3.10.0-957.el7.x86_64
Kernel version: #1 SMP Thu Oct 4 20:48:51 UTC 2018
Kernel arch:    x86_64

kernel base (arch default): ffffffff81000000

[.] checking /boot/config ...

[.] trying /proc/cmdline...

[.] trying /proc/kallsyms...
[-] kernel base not found in /proc/kallsyms

[.] trying /sys/kernel/slab/ ...
leaked init_net: ffffffff98511640
kernel base (possible): ffffffff98500000
kernel base (possible): ffffffff98000000

perf_event_open.c: In function ‘get_kernel_addr_perf’:
perf_event_open.c:51:12: warning: missing braces around initializer [-Wmissing-braces]
     struct utsname self = {0};
            ^
perf_event_open.c:51:12: warning: (near initialization for ‘self.sysname’) [-Wmissing-braces]
[.] trying perf_event_open sampling ...
[-] syscall(SYS_perf_event_open): Permission denied

[.] trying syslog ...

[.] trying 'pppd file /proc/kallsyms 2>&1' ...

[.] trying mincore info leak...
[-] kernel base not found in mincore info leak

[.] checking CPU TSX/RTM support ...
[-] CPU does not support TSX/RTM

[.] checking /sys/devices/system/cpu/vulnerabilities ...

```

## Debian 9.6 (x64)

```
$ ./kasld 
[ KASLD ] Kernel Address Space Layout Derandomization

Kernel release: 4.9.0-9-amd64
Kernel version: #1 SMP Debian 4.9.168-1 (2019-04-12)
Kernel arch:    x86_64

kernel base (arch default): ffffffff81000000

[.] checking /boot/config ...

[.] trying /proc/cmdline...

[.] trying /proc/kallsyms...
kernel base (certain): ffffffff8d000000

[.] trying /sys/kernel/slab/ ...
opendir(/sys/kernel/slab/): No such file or directory

[.] trying perf_event_open sampling ...
[-] syscall(SYS_perf_event_open): Permission denied

[.] trying syslog ...
[-] klogctl(SYSLOG_ACTION_SIZE_BUFFER): Operation not permitted

[.] trying 'pppd file /proc/kallsyms 2>&1' ...

[.] trying mincore info leak...
[-] kernel base not found in mincore info leak

[.] checking CPU TSX/RTM support ...
[-] CPU does not support TSX/RTM

[.] checking /sys/devices/system/cpu/vulnerabilities ...

```


## Addendum

Additional noteworthy techniques not included for one reason or another.

KASLD checks for TSX/RTM support and Meltdown vulnerability, but does not implement these techniques. Refer to:

* [vnik5287/kaslr_tsx_bypass](https://github.com/vnik5287/kaslr_tsx_bypass)
* [paboldin/meltdown-exploit](https://github.com/paboldin/meltdown-exploit)

[sctp_af_inet kernel pointer leak (CVE-2017-7558)](https://www.exploit-db.com/exploits/45919) requires `libsctp-dev`.

[inet_csk_listen_stop GPF (CVE-2017-18509)](https://pulsesecurity.co.nz/advisories/linux-kernel-4.9-inetcsklistenstop-gpf) requires unprivileged user namespaces (or `CAP_NET_ADMIN`) and access to `dmesg`.

[wait_for_kaslr_to_be_effective.c](https://grsecurity.net/~spender/exploits/wait_for_kaslr_to_be_effective.c)

[From IP ID to Device ID and KASLR Bypass](https://arxiv.org/pdf/1906.10478.pdf)

Bugs which triggers a kernel oops (ie: inet_csk_listen_stop GPF) can be used to leak kernel pointers by reading dmesg / syslog on systems without `dmesg_restrict`.

On Ubuntu systems, `dmesg_restrict` can be bypassed by users in the `adm` group, due to file read permissions on `/var/log/syslog`.

```
$ ls -la /var/log/syslog
-rw-r----- 1 syslog adm 985810 Dec 30 15:26 /var/log/syslog
```

Offsets to useful functions (`commit_creds`, `prepare_kernel_cred`, `native_write_cr4`, etc) from the base address can be pre-calculated for publicly available kernels, or retrieved from various locations (kallsyms, vmlinux, Sysmte.map, etc) using [jonoberheide/ksymhunter](https://github.com/jonoberheide/ksymhunter).

Privileged arbitrary read/write in kernel space can be used to bypass KASLR:

* https://github.com/salls/kernel-exploits/blob/master/CVE-2017-5123/exploit_no_smap.c
* https://ryiron.wordpress.com/2013/09/05/kptr_restrict-finding-kernel-symbols-for-shell-code/


## References

* [grsecurity - KASLR: An Exercise in Cargo Cult Security](https://grsecurity.net/kaslr_an_exercise_in_cargo_cult_security)
* [get_kernel_sym /proc/kallsyms](https://grsecurity.net/~spender/exploits/exploit.txt)
* [Randomize kernel base address on boot [LWN.net]](https://lwn.net/Articles/444556/)
* [Linux Kernel Driver DataBase: CONFIG_RANDOMIZE_BASE: Randomize the address of the kernel image (KASLR)](https://cateee.net/lkddb/web-lkddb/RANDOMIZE_BASE.html)
* [Linux Kernel Driver DataBase: CONFIG_RELOCATABLE: Build a relocatable kernel](https://cateee.net/lkddb/web-lkddb/RELOCATABLE.html)
* [nf_conntrack net_inet leak](https://www.openwall.com/lists/kernel-hardening/2017/10/05/5)
* [mincore heap page disclosure](https://bugs.chromium.org/p/project-zero/issues/detail?id=1431)
* [Breaking KASLR with perf](https://blog.lizzie.io/kaslr-and-perf.html)
* [pppd kptr_restrict bypass](https://www.openwall.com/lists/kernel-hardening/2013/10/14/2)
