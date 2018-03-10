This QEMU patch introduces irix/solaris userland emulation. It currently runs
only under linux (though BSD support would probably be feasable).

### compiling

Configure QEMU for irix/solaris userland emulation and compile:

```
configure --target-list=irix-linux-user,irixn32-linux-user,irix64-linux-user,solaris-linux-user
make && make install
```

### using

I recommend using binfmt. Prepare some wrapper scripts for each of the qemu
binaries for irix/solaris using this template:

```
#! /bin/sh

ex=$1; shift
a0=$1; shift

export QEMU_RESERVED_VA=1G
export QEMU_LD_PREFIX=<target rootfs>
export QEMU_SET_ENV=LANG=C

exec <qemu binary> -0 $a0 $ex "$@"
```

Install the binfmt (replace the QEMU* environment variables by the location of
the wrapper scripts):

```
echo :irix:M::'\x7fELF\x01\x02\x01\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x02\x00\x08\x00\x00\x00\x01\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00':'\xff\xff\xff\xff\xff\xff\xff\x00\xff\xff\xff\xff\xff\xff\xff\xff\xff\xfe\xff\xff\xff\xff\xff\xff\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\xef':${QEMUIRIX32}:P > /proc/sys/fs/binfmt_misc/register
echo :irixn32:M::'\x7fELF\x01\x02\x01\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x02\x00\x08\x00\x00\x00\x01\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x20':'\xff\xff\xff\xff\xff\xff\xff\x00\xff\xff\xff\xff\xff\xff\xff\xff\xff\xfe\xff\xff\xff\xff\xff\xff\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\xef':${QEMUIRIXN32}:P > /proc/sys/fs/binfmt_misc/register
echo :irix64:M::'\x7fELF\x02\x02\x01\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x02\x00\x08':'\xff\xff\xff\xff\xff\xff\xff\x00\xff\xff\xff\xff\xff\xff\xff\xff\xff\xfe\xff\xff':${QEMUIRIX64}:P > /proc/sys/fs/binfmt_misc/register
echo :solaris:M::'\x7fELF\x01\x02\x01\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00\x02\x00\x02':'\xff\xff\xff\xff\xff\xff\xff\x00\xff\xff\xff\xff\xff\xff\xff\xff\xff\xfe\xff\xff':${QEMUSOLARIS32}:P > /proc/sys/fs/binfmt_misc/register
```

Now you should be able to directly execute irix/solaris binaries from the shell.
As a rather simple test, try:

```
<target rootfs>/bin/ls
```

send bug reports, fixes etc to Kai-Uwe Bloem  (<derkub@gmail.com>)
