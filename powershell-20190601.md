# Welcome to the first PowerShell!
## WSUS : Windows Server Update Services 
### Step 1. [How to install and configure WSUS on Windows server 2016](https://0857.000webhostapp.com/windows/how-to-install-and-configure-wsus-on-windows-server-2016-part-1/)
```
* ALL Data/DB run away C:
* WID DB
* IIS
* share Flofer
* APIs(.Net)不要輕易更新
```
### Step 2. Client install
```
* check GPO OK
* Event Log : Detect、Install 、Results
* need to see error messages
```
### Step 3. Server
```
* Updates
    Check Related Information
    Approve or Decline    
* Client
    State :Download、Reboot、Install......
```
### Step 4. Maintain
```
* Decline Expired Updates
* Run clean Wizard ()
* Re-Index DB
* Tips：不要在 WSUS Server 進行同步的時候作管理或維護的動作，以免導致WSUS DB索引錯亂!!
```
## PowerShell 
### How to check "Powershell Version"
```
 * for os:  winver
 * for PS:  $PSversionTable
 * for EV:  ENV:(Use  'Get-item')  >>  Get-Item Env:
```
### 查詢指令
```
  * powershell.exe /?
  * Get-command
  * Get-Alias
  * Get-Help
```
### PowerShell 語法
```
  * PowerShell = Array + String
  * Special CmdLet: | (管線)
      Usage：AD cmdlet or EMS on Exchange...etc.
  * Default format : String
```
* 定義時字串、整數要分清楚 
#### 舉例："1","2","33","4423","322" |% {$_} |sort
```
1
2
322
33
4423
```
#### 舉例："1","2","33","4423","322" |% {[int]$_} |sort
````
1
2
33
322
4423
````
### PowerShell Live
```
* Running on "Memory" !
* Watch on "Task Manager"
* It's "Process"
* YA.... "Unlimited" !!!
* Default by "32 thread" as pricess
* "session"?! -> "Runspace"
```
### PowerShell Work
```
* How to "Run"?
   Executed by "Line"
   But "Function"......?
* What to do about "non-PS file"
   'bat'
   'exe'
   寫成function:
   Start-Sleep -Seconds 10
   $batfile = [diagnostics.process]::Start(D:\demo\my_script.bat)
   $batfile.WatiForExit()
```
### PowerShell Null 空值
```
* $a = " "
* $a = ''
* $a = $Null
* $a != "" != '' != $Null
* Up to your "Powershell Version"
各版可能有些許差異
```
### PowerShell Array
* 預設是一維陣列
### PowerShell Filter
####How to cut the "Messages"?
```
* a "-Replace("")"
* a "-Split("")"
* a -match $Regex
Use Special Characters like "`n `r `t"
```
### PowerShell History
#### How to Check "Previous Command"?
```
* "Get-Histoyr"
* Alias "H"
* Added "| ?{$_.CommandLuine -eq 'H'}"
* r -ld "Number"
* Use "F7" >>> "Read-Host"
```
### PowerShell Record
```
* Default "Saved" & "Sotred"
* Use " Strart -Transcript"
* Stored Path "$PROFILE"
  All User's Profile Path "$PROFILE.AllUsersAllHosts"
* TO end "Stop-Transcript"
* On Remote Hosts "Use PS-Session"
```
### PowerShell BITS
OOPS!
[PS使用教學](https://books.goalkicker.com/PowerShellBook/)
### PowerShell PSObject
```
[PSCustomObject] in PS v3.0
No Type "PSObject"!
NO "Hashtable"!
Usaully use with "Foreach".
```
### 參考資料
1. [Basic Explanation]
2. [Microsoft Website]
3. [Active Directory with PowerShell](https://www.books.com.tw/products/0010697249)
4. [Learn Windows PowerShell](https://www.books.com.tw/products/0010809471)
