# G-140W-MD_Optical_modem_hacking
移动诺基亚贝尔 G-140W-MD 光猫破解超级密码和宽带账户密码

# 准备

登录光猫管理页面，转到 `网络->远程管理->Password认证` 记录下 Password，这个密码用于光猫重置后重新获取配置，务必保存好

# telnet 访问和 root 密码

- 拔掉光纤，捅住光猫 Reset 键重置光猫
- 待光猫重启完成后，使用默认超密：用户名 `CMCCAdmin` 密码 `aDm8H%MdA` 登录光猫
- 访问 [http://192.168.1.1/system.cgi?telnet](http://192.168.1.1/system.cgi?telnet) 开启 telnet
- 在光猫 Web 管理页面 `应用->家庭存储->FTP` 勾选启用 FTP 并保存，`应用->家庭存储->SAMBA共享` 勾选启用 Samba 并保存
- telnet 登录光猫，用户名和密码用光猫背面写的普通账户的用户名和密码
- `su user_ftp` 切换到 ftp 用户，密码和 user 用户一样
- 切换成功后此时已有 root 权限，输入 `su` 切换到 root 用户，输入 `passwd` 重置 root 用户的密码

# Web 超级密码

- 插上光纤，退出 Web 管理页面登录的账户，在登录页面点击设备注册，填入之前记录的 Password 并保存，等待光猫完成配置
  - 若注册进度卡在 99% 显示超时，可继续下一步，只是宽带账户密码没有下发，后续超级管理员登录后自行填写即可

- 此时 CMCCAdmin 账户的密码已经被下发的配置修改，但是之前开的 telnet 没有被关闭，用 root 登录 telnet（如果 root 用户密码被下发配置改了，使用 `user_ftp` 用户登录，一样有 root 权限），输入 `cfgcli -s InternetGatewayDevice.DeviceInfo.X_CT-COM_TeleComAccount.Password aDm8H%MdA` 更改超级密码，可以随意设定
  - root 用户密码被更改后使用 `user_ftp` 登录步骤与上一步相同，先用普通用户登录后切换

- 使用新的超级密码登录 Web 管理页面

# 宽带账户密码获取

宽带账户未下发则可致电 10086 重置密码，以下步骤忽略

- 以超级密码登录 Web 管理页面后，访问 [http://192.168.1.1/dumpdatamodel.cgi](http://192.168.1.1/dumpdatamodel.cgi) 点击 Refresh 获取光猫配置
- 搜索宽带账户名（一般是手机号），定位到一个 Username 段，下一行的 `<Password dv="" ml="64" rw="RW" t="string" v="iBI7yawqR32pivXtjlLhpQ==" ealgo="ab"></Password>` 就是宽带密码，v=后面的内容是加密过的密码
- 下载 [nokia-router-cfg-tool.py](https://gist.github.com/Lokotito/5ab7b8627d43d583fd888a0af39d6b12)，运行脚本 `python nokia-router-cfg-tool.py -d iBI7yawqR32pivXtjlLhpQ==` 来解密密码

# 删除 TR069

tr069是一个网络设置，可能用于运营商远程控制光猫

TR069 的“删除”按钮可能呈灰色，无法直接点击。这通常是管理网页给按钮添加了 `disabled` 属性

- 进入 `网络 → 宽带设置`，选择 TR069
- 按 `F12` 打开浏览器开发者工具。
- 进入 Elements／元素页面，点击开发者工具左上角 `↖ 选择页面元素`
- 鼠标点一下灰色的 **删除** 按钮
- 删除按钮代码中的 `disabled` 属性
- 按钮恢复可用后，点击“删除”。刷新宽带设置页面，确认TR069 已经消失
