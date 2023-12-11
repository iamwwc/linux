此仓库要求：脱离buildroot也能正常开发调试

## linux开发流程

### x86_64

```bash
make CC=clang weichao_x86_64_defconfig
```

如果要写到 `arch/x86/configs/weichao_x86_64_defconfig`

### orangepi zero3

```bash
make ARCH=arm64 linux_sunxi64_defconfig
make ARCH=arm64 -j$(nproc)
```

**不要少了`ARCH=xx`，否则make编译会重新弹propmt**

> https://stackoverflow.com/questions/50405217/make-kernel-prompting-for-config-options-even-when-config-is-present

在x86机器上编译arm架构的kernel需要配置交叉编译环境，否则编译会失败

用buildroot编译会自动构建arm的编译器套件

### x86保存.config

```bash
make savedefconfig
cp defconfig arch/x86/configs/weichao_x86_64_defconfig
```

调试

```bash
qemu-system-x86_64 --kernel ./arch/x86/boot/bzImage -initrd ./rootfs.cpio -device e1000,netdev=eth0 -netdev user,id=eth0,hostfwd=tcp::5555-:22,net=192.168.76.0/24,dhcpstart=192.168.76.9  -append "nokaslr console=ttyS0" -S -nographic -gdb tcp::1234 -virtfs local,path=/,security_model=none,mount_tag=guestroot
```


kernel-dev 以 workspace 后，第一个debug config总是linux/，而预期使用buildroot/的文件系统，行为不符合预期，引入干扰

所以linux/ 默认不使用launch.json，

单独调试 linux/ 再将 `..vscode` 改为 `.vscode`

## 生成 compile commands

```bash
./scripts/clang-tools/gen_compile_commands.py
```
## rebase upstream kernel

lts: git://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git
master branch: git://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git

比如rebase lts 下的 v6.6.4

```bash
git remote add upstream git://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git

git fetch upstream tag v6.6.5 --no-tags
git rebase upstream/v6.6.4
```

## 代码分析

注释开始标记

- // CC-NET 
    网络代码CR
- // CC-NET-TCP
    TCP相关代码分析 
- // CC-NET-EPOLL
    epoll

## 开发tip
### 复制某tag下的文件

```bash
git remote set-url origin git@github.com:taikulawo/linux
git remote add git@github.com:torvalds/linux.git
git fetch upstream --tags
```

vscode `ctrl + k ctrl + o` 快捷键 open file on remote from

能够快速打开upstream 某个tag下的文件，方便分析代码复制url