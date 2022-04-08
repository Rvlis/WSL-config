# WSL2-config + cuda配置
- [安装WSL2](https://docs.microsoft.com/zh-cn/windows/wsl/install-win10#step-4---download-the-linux-kernel-update-package)
- [自定义WSL安装位置](https://zhuanlan.zhihu.com/p/263089007)

## 指定位置安装Ubuntu(18.04) + Ananconda + VSCode

1. 启用WSL和虚拟机功能
   <!-- ![1](res/img/控制面板.jpg) -->
  <div align="center">
      <img src="./res/img/1.png" width = "80%" alt="1" align=center />
  </div>
  或

   ```
   dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
   dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
   ```
2. 指定位置安装
  - [手动下载安装包](https://docs.microsoft.com/zh-cn/windows/wsl/install-manual)
  - 将后缀名`.appx`修改为`.zip`
  - 解压到指定位置
  - 双击`.exe`文件安装
  <div align="center">
      <img src="./res/img/2.png" width = "80%" alt="2" align=center />
  </div>
3. WSL2启动时提示参考的对象类型不支持尝试的操作（若有）
   - [解决](https://xinzsh.com/notes/WSL2-issues.html)
   

4. 换源（若需要）
   ```
   sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
   sudo vim /etc/apt/sources.list
   ```

   - 注释掉`sources.list`中的默认源
   - 添加[清华源](https://mirror.tuna.tsinghua.edu.cn/help/ubuntu/)
5. 打开VSCode, 安装Remote-WSL插件
   <div align="center">
      <img src="./res/img/Remote-WSL.jpg" width = "80%" alt="3" align=center />
  </div>
6. 打开Ubuntu系统终端，输入`code .`，安装VSCode Server
    - 若想在子系统中查看windows下的资源，windows资源都挂载在 `/mnt`路径下
  <div align="center">
      <img src="./res/img/vscode teiminal.jpg" width = "80%" alt="4" align=center />
  </div>
7. 安装Anaconda
   - Windows系统中下载[anaconda](https://www.anaconda.com/products/individual) Linux版本
   
   ```
   cd /mnt/your/path/
   bash Anaconda3-xxxx.xx-Linux-x86_64.sh
   ```

   - 在安装过程中，记住安装路径，后续将这个路径加入环境变量，其余一路yes就可
   - 添加环境变量, `sudo vim /etc/profile`
   - 添加 `export PATH=/home/rvlis/anaconda3/bin:$PATH`, 其中路径替换为自己的安装路径
   - 重新加载环境变量 `source /etc/profile`
   - [Linux环境变量配置详解](https://segmentfault.com/a/1190000038313883)

## WSL2中配置CUDA

9. 更新wsl
   ```bash
   wsl.exe --update
   ```
   如果提示更新失败，转到 `设置` -> `更新和安全` -> `高级选项` -> `打开更新windows时接收其他Microsoft产品的更新`

10. 在 __windows端__ 安装[Nvidia 驱动](https://developer.nvidia.com/cuda/wsl)
   <div align="center">
      <img src="./res/img/cuda-driver.jpg" width = "80%" alt="3" align=center />
   </div>
   <div align="center">
      <img src="./res/img/cuda-driver-2.jpg" width = "80%" alt="3" align=center />
   </div>

11. 在 __WSL__ 中安装docker
   ```bash
   curl https://get.docker.com | sh
   ```

12. 在 __WSL__ 中安装 Nvida Container Toolkit
   ```bash
   distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
   curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
   curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
   curl -s -L https://nvidia.github.io/libnvidia-container/experimental/$distribution/libnvidia-container-experimental.list | sudo tee /etc/apt/sources.list.d/libnvidia-container-experimental.list
   ```

13. 更新apt repositories并安装Nvida runtime
    ```bash
    sudo apt update && sudo apt install -y nvidia-docker2
    ```

14. 重启wsl并启动docker
    ```bash
    sudo service docker stop
    sudo service docker start
    ```
   如果启动失败，执行：
   ```bash
   sudo chmod 666 /var/run/docker.sock
   ```
15. 运行
    ```bash
    docker run --gpus all nvcr.io/nvidia/k8s/cuda-sample:nbody nbody -gpu -benchmark
    ```

16. 验证cuda是否安装成功
    ```python
    import torch
    torch.cuda.is_available()
    >>> True
    ```
    <div align="center">
      <img src="./res/img/验证安装成功.jpg" width = "80%" alt="3" align=center />
    </div>

## Keep going! 🐱‍🏍
