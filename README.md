# 钉钉wine指导

这里使用2019.10.14的最新wine，成功运行最新DingTalk。除了RichEdit部分功能不正常，其余功能均正常。

## 教程

### 1 依赖安装

我这里以archlinux为例，pakku和pacman是我的包管理器。请自己更换成你的发行版的包管理器。我假设你已经安装wine全家桶。

```bash
sudo pacman -S libwbclient samba lib32-curl
pakku -S multilib/lib32-gnutls multilib/lib32-libcurl-gnutls
```

然后你需要手动安装riched20.dll和字体。我这里的WINEPREFIX为`~/.wine`，请更改为适合你的路径。

**请不要使用winetricks中的riched20或30。它们的版本过低，DingTalk会崩溃。**

```bash
cp res/riched20.dll ~/.wine/drive_c/windows/system32/
cp res/wqy-microhei.ttc ~/.wine/drive_c/windows/Fonts/
wine regedit # Please import res/chn_fonts.reg manually.
```

然后在打开的注册表管理器中，手动导入`res/chn_fonts.reg`。

### 2 安装DingTalk

为了防止DingTalk更新造成其他问题，建议直接使用这里提供的的钉钉安装包。来自www.dingtalk.com官网。

[Download from DropBox](https://www.dropbox.com/s/wnf7bwww7h8wt4g/DingTalk_v4.7.10.32.exe?dl=0)

然后直接使用wine运行这个安装包，正常安装即可。

### 3 已知问题

- Bad RichEdit

因为未知问题，我输入进RichEdit的字符都不会显示出来。这或许可以通过编辑`/home/recolic/.wine/drive_c/Program Files (x86)/DingDing/main/current_new/uiresources/new/common/layout`来解决，
也有可能是riched20.dll版本仍然不正确。如果你通过winedbg或修改xml发现改进方案，请提交Pull Request。

但是这些输入的字符还可以正常发送，所以勉强能满足正常使用。

- Bad AliSystemSrv

AliSystemSrv进程会运行一段时间后崩溃，这是由于缺少QtGui4.dll。你可以通过下载32位的QtGui4.dll并放入`~/.wine/drive_c/ProgramData/Alibaba/AliSystemSrv/`来解决，
但是这个错误并不会影响钉钉正常使用。我认为这个进程可能有恶意行为，因此不推荐修复这个问题。

