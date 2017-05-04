1. win7进程

	netstat -aon|findstr "8088"
	taskkill -f -pid 

2. linux tcp
lsof -i tcp:8900

3. 查看debug.keystore

	切换到目录.android/
	keytool -list -keystore debug.keystore
	输入密码：android

4. Homebrew使用

	搜索软件：brew search 软件名，如brew search wget

	安装软件：brew install 软件名，如brew install wget

	卸载软件：brew remove 软件名，如brew remove wget

	常见错误
       configure: error: cannot run C compiled programs.
解决方法：run `xcode-select --install`

4. logcat
Log.v("log_fhnews",消息)；
adb logcat  log_fhnews:V > C:/log_fhnews.txt
                     Tag           等级


5. Battery Historian
用于展示Android的电量消耗
