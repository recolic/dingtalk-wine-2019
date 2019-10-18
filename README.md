# 钉钉wine指导

## 教程

archlinux为例，pakku和pamcan是我的包管理器。这里用的钉钉是2019-10-14的最新版本。

### 1 依赖安装

我这里以archlinux为例，pakku和pacman是我的包管理器。请自己更换成你的发行版的包管理器。我假设你已经安装wine全家桶。

```bash
sudo pacman -S libwbclient samba lib32-curl
pakku -S multilib/lib32-gnutls multilib/lib32-libcurl-gnutls
```

然后你需要手动安装riched20.dll和字体。我这里的WINEPREFIX为`~/.wine`，请更改为适合你的路径。

**请不要使用winetricks中的riched20或30。它们的版本过低，DingTalk会崩溃。**

```bash
cp res/
cp res/wqy-microhei.ttc ~/.wine/drive_c/windows/Fonts
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

# 下面是使用了旧版本的riched20.dll的症状

## 当前问题

注意，这样仍然有问题，点击别人的消息就会崩溃，崩溃时的log如下。其余windows钉钉的所有功能都能正常运行。

```
etc_=vpgae_v1,spm_url=a2o5v.12290059.1.conv_click, 这一行是点击时，钉钉打到stdout的log。
Fontconfig warning: "/etc/fonts/conf.avail/05-reset-dirs-sample.conf", line 6: unknown element "reset-dirs" 我修复了这一行的问题，但没用。不是这里的问题。
0145:fixme:ver:GetCurrentPackageId (0x32f8a4 (nil)): stub
0145:fixme:ntdll:NtLockFile I/O completion on lock not implemented yet
0145:fixme:ver:GetCurrentPackageId (0x32f4a8 (nil)): stub
counterData={"data":[{"arg":"{\"appname\":\"dingtalk\",\"crash_hit\":\"true\"}","begin":1570602656,"count":1,"end":1570602656,"monitorPoint":"crash","page":"app","value":1}],"meta":{"bid":"","bv":"","hv":"","pid":"","pt":"","sdk-version":"1.3.0.4090"}}
,到这里的时候，程序已经崩溃了。
0147:fixme:toolhelp:CreateToolhelp32Snapshot Unimplemented: heap list snapshot
0147:fixme:toolhelp:Heap32ListFirst : stub
alarmData={"data":[{"arg":"{\"0145:fixme:msvcrt:__clean_type_info_names_internal (0x38ec5c) stub
Channel\":\"2012000084:fixme:ntdll:EtwEventUnregister (deadbeef) stub.
\⏎                                                                                                                                                                                            14:30@recolic ~/.w/d/P/D/m/current QwQ  
14:30@recolic ~/.w/d/P/D/m/current QwQ  
14:30@recolic ~/.w/d/P/D/m/current >_<  
14:30@recolic ~/.w/d/P/D/m/current `~`  
14:30@recolic ~/.w/d/P/D/m/current <.<  
```

附当时的安装包。https://www.dropbox.com/s/wnf7bwww7h8wt4g/DingTalk_v4.7.10.32.exe?dl=0

这里是点击别人的消息之后，更详细的log： https://paste.ee/p/VUgk4
我尝试了riched20和riched30，都有这个问题。
这里是崩溃时，winedbg给出的stacktrace： 

```
Register dump:
 CS:0023 SS:002b DS:002b ES:002b FS:0063 GS:006b
 EIP:772b5230 ESP:0032ce34 EBP:0032cef0 EFLAGS:00210246(  R- --  I  Z- -P- )
 EAX:0032ce50 EBX:0032cf20 ECX:00000006 EDX:0032ce84
 ESI:00000000 EDI:0032ce84
Stack dump:
0x000000000032ce34:  0032cfc4100d6e68 00000000772c3b69
0x000000000032ce44:  100b73f800002018 772b4ce8100b73fc
0x000000000032ce54:  100d6e380032d1f0 0000000000000000
0x000000000032ce64:  100d6da000000000 0000000000000000
0x000000000032ce74:  0000000000000000 0000000000000000
0x000000000032ce84:  0032cebc00000000 120bb1d00ce39200
0x000000000032ce94:  0032cec80032d220 00000070100d6e70
0x000000000032cea4:  100d6e6800000070 0000007000000070
0x000000000032ceb4:  0000000000000000 003200000000ced8
0x000000000032cec4:  0000000077200000 0000000000000000
0x000000000032ced4:  100d6eb800000000 0000000000000000
0x000000000032cee4:  00000000100d6e68 0032cf6c00000000
Backtrace:
=>0 0x00000000772b5230 EntryPoint+0x41a0() in riched20 (0x000000000032cef0)
  1 0x00000000772b5210 EntryPoint+0x417f() in riched20 (0x000000000032cf6c)
  2 0x00000000772b4e98 EntryPoint+0x3e07() in riched20 (0x000000000032d004)
  3 0x00000000772bafd9 EntryPoint+0x9f48() in riched20 (0x000000000032d060)
  4 0x00000000772bbc5a EntryPoint+0xabc9() in riched20 (0x000000000032d078)
  5 0x00000000772bbbd9 EntryPoint+0xab48() in riched20 (0x000000000032d098)
  6 0x00000000772b21de EntryPoint+0x114d() in riched20 (0x000000000032d218)
  7 0x0000000002fbbe79 EntryPoint+0xffffffffffffffff() in mainframe (0x000000000032d238)
  8 0x0000000002fbbef8 EntryPoint+0xffffffffffffffff() in mainframe (0x000000000032d24c)
  9 0x0000000002ac3642 EntryPoint+0xffffffffffffffff() in mainframe (0x000000000032d7ec)
  10 0x0000000002ab069c EntryPoint+0xffffffffffffffff() in mainframe (0x000000000032dcd8)
  11 0x0000000002f8a7cf EntryPoint+0xffffffffffffffff() in mainframe (0x000000000032dd8c)
  12 0x0000000002f9c046 EntryPoint+0xffffffffffffffff() in mainframe (0x000000000032dee0)
  13 0x0000000002f9cf48 EntryPoint+0xffffffffffffffff() in mainframe (0x000000000032df00)
  14 0x0000000002f9cd8a EntryPoint+0xffffffffffffffff() in mainframe (0x000000000032e060)
  15 0x0000000002f9ccca EntryPoint+0xffffffffffffffff() in mainframe (0x000000000032e080)
  16 0x0000000002f97f58 EntryPoint+0xffffffffffffffff() in mainframe (0x000000000032e098)
  17 0x0000000002f8dc35 EntryPoint+0xffffffffffffffff() in mainframe (0x000000000032e0ac)
  18 0x0000000002f88bc8 EntryPoint+0xffffffffffffffff() in mainframe (0x000000000032e2d0)
  19 0x0000000002d80c92 EntryPoint+0xffffffffffffffff() in mainframe (0x000000000032e2f8)
  20 0x0000000002ce1a8e EntryPoint+0xffffffffffffffff() in mainframe (0x000000000032e310)
  21 0x0000000002ac2231 EntryPoint+0xffffffffffffffff() in mainframe (0x000000000032e330)
  22 0x0000000002a88136 EntryPoint+0xffffffffffffffff() in mainframe (0x000000000032e358)
  23 0x0000000002f8cd18 EntryPoint+0xffffffffffffffff() in mainframe (0x000000000032e378)
  24 0x000000007e720bac WINPROC_wrapper+0x1b() in user32 (0x000000000032e3a8)
  25 0x000000007e72121d EditWndProcA+0x55c() in user32 (0x000000000032e3f8)
  26 0x000000007e723463 EditWndProcA+0x27a2() in user32 (0x000000000032e448)
  27 0x000000007e6e863e DispatchMessageW+0x7d() in user32 (0x000000000032e4f8)
  28 0x00000000048e5e33 EntryPoint+0xffffffffffffffff() in libcef (0x000000000032e574)
  29 0x00000000048e5a83 EntryPoint+0xffffffffffffffff() in libcef (0x000000000032e5d0)
  30 0x00000000048e57d5 EntryPoint+0xffffffffffffffff() in libcef (0x000000000032e5f8)
  31 0x00000000048bdba5 EntryPoint+0xffffffffffffffff() in libcef (0x000000000032e60c)
  32 0x000000000609b63c EntryPoint+0xffffffffffffffff() in libcef (0x000000000032e62c)
  33 0x0000000002a4a2eb EntryPoint+0xffffffffffffffff() in mainframe (0x000000000032e70c)
  34 0x00000000023476ea EntryPoint+0xffffffffffffffff() in mainframe (0x000000000032f6fc)
  35 0x000000000040b2d4 EntryPoint+0xffffffffffffffff() in dingtalk (0x000000000032f7c8)
  36 0x000000000040b995 EntryPoint+0xffffffffffffffff() in dingtalk (0x000000000032f8bc)
  37 0x000000000040f3df EntryPoint+0xffffffffffffffff() in dingtalk (0x000000000032fed4)
  38 0x000000000044bbea EntryPoint+0xffffffffffffffff() in dingtalk (0x000000000032ff20)
  39 0x000000007b46d099 call_process_entry+0x18() in kernel32 (0x000000000032ff48)
  40 0x000000007b47141b ExitProcess+0x436e() in kernel32 (0x000000000032ffd8)
  41 0x000000007b46d0aa call_process_entry+0x29() in kernel32 (0x000000000032ffec)
0x00000000772b5230 EntryPoint+0x41a0 in riched20: repe movsl	(%esi),%es:(%edi)
Wine-dbg>00b2:fixme:kernelbase:AppPolicyGetThreadInitializationType FFFFFFFA, 06A8FF04

```

我写了个脚本移除掉所有的RichEdit标签，问题就暂时掩盖了，钉钉正常工作。
```
14:59@recolic ~/.w/d/P/D/m/c/u/n/c/layout QwQ  cat remove_riched.sh 
#!/bin/bash
# Usage:$     find . -type f -name '*.xml' -exec ./remove_riched.sh '{}' ';'

fl="$1"

[ "_$fl" = "_" ] && echo "Usage: $0 <fileName.xml>" && exit 1

cat "$fl" | sed 's|RichEdit>|RichEdit-->|g' | sed 's|RichEditEx>|RichEditEx-->|g' | sed 's|<RichEdit|<!--RichEdit|g' > /tmp/dingtalk.riched.tmp.delete.me &&
cp /tmp/dingtalk.riched.tmp.delete.me "$fl"

exit $?


```
