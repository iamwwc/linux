kernel-dev workspace 化后第一个 debug launch config 总是 linux/，而预期使用buildroot/的文件系统，行为不符合预期，引入干扰

linux/ 默认不使用 launch.json，

单独调试 linux/ 再将 `launch.json.bak` 改为 `launch.json`

Visual Studio Code project for Linux kernel sources
===================================================

Ensure the kernel is built (at least, all `*.cmd` files should be generated):

    $ make defconfig
    $ make

Clone this repository as ".vscode":

    $ git clone git@github.com:amezin/vscode-linux-kernel.git .vscode

Generate compile_commands.json:

    $ python .vscode/generate_compdb.py

If you are not compiling kernel for x64, change `intelliSenseMode` in
`c_cpp_properties.json`. Possible values as of vscode-cpptools 1.0.1:

* `gcc-x86`
* `gcc-x64`
* `gcc-arm`
* `gcc-arm64`

Open the project:

    $ code .

Out-of-tree builds
------------------

https://github.com/amezin/vscode-linux-kernel/issues/4

Kernel can be built with separate output directory:

    $ make O=../linux-build defconfig
    $ make O=../linux-build

In this case, you should pass the directory to `generate_compdb.py`:

    $ python .vscode/generate_compdb.py -O ../linux-build

`compile_commands.json` will still be generated in the current directory (root of the `linux` repository).
Unfortunately, `tasks.json` will not work out of the box in this configuration (TODO).

Out-of-tree module development
------------------------------

If you build your module with this command:

    $ make -C $KDIR M=$PWD modules

You could generate `compile_commands.json` with:

    $ python .vscode/generate_compdb.py -O $KDIR $PWD

Example: https://github.com/amezin/nzxt-rgb-fan-controller-dkms
