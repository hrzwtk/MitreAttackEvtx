# Mitre Att&ck の各Techniqueで出力されるイベントログ

主にPurpleSharpを実行して、関係するイベントログをエクスポートしています。順次、追加予定です。<br>
https://github.com/mvelazc0/PurpleSharp


基本的にEventLog配下をご参照ください。<br>
- T1003_001_OS_Credential_Dumping-LSASS_Memory-CommandLog.txt<br>
  実行時のログなど<br>
- T1003_001_OS_Credential_Dumping-LSASS_Memory.evtx<br>
  イベントログファイル<br>

evtx配下は実行結果の確認が不十分だったので、近いうちに削除します。

## クライアント環境
Windows 10 1809 [Version 10.0.17763.1554]<br>
Microsoft Edge Developerから評価用VMをダウンロードして使用<br>
https://developer.microsoft.com/ja-jp/microsoft-edge/tools/vms/

## イベントログ設定
Windowsのデフォルト設定では、イベントログにはほとんど出力されない。<br>
下記のサイトを参考に、イベントログ設定をチューニングした。<br>
Malware Archaeology Logging Level<br>
https://www.malwarearchaeology.com/cheat-sheets
<pre>
C:\Windows\system32>auditpol.exe /get /category:*
System audit policy
Category/Subcategory                      Setting
System
  Security System Extension               Success and Failure
  System Integrity                        Success and Failure
  IPsec Driver                            Success
  Other System Events                     Failure
  Security State Change                   Success and Failure
Logon/Logoff
  Logon                                   Success and Failure
  Logoff                                  Success
  Account Lockout                         Success
  IPsec Main Mode                         No Auditing
  IPsec Quick Mode                        No Auditing
  IPsec Extended Mode                     No Auditing
  Special Logon                           Success and Failure
  Other Logon/Logoff Events               Success and Failure
  Network Policy Server                   Success and Failure
  User / Device Claims                    No Auditing
  Group Membership                        Success
Object Access
  File System                             Success
  Registry                                Success
  Kernel Object                           No Auditing
  SAM                                     Success
  Certification Services                  Success and Failure
  Application Generated                   Success and Failure
  Handle Manipulation                     No Auditing
  File Share                              Success and Failure
  Filtering Platform Packet Drop          No Auditing
  Filtering Platform Connection           Success
  Other Object Access Events              Success and Failure
  Detailed File Share                     Success
  Removable Storage                       Success and Failure
  Central Policy Staging                  No Auditing
Privilege Use
  Non Sensitive Privilege Use             No Auditing
  Other Privilege Use Events              No Auditing
  Sensitive Privilege Use                 Success and Failure
Detailed Tracking
  Process Creation                        Success and Failure
  Process Termination                     No Auditing
  DPAPI Activity                          No Auditing
  RPC Events                              Success and Failure
  Plug and Play Events                    Success
  Token Right Adjusted Events             Success and Failure
Policy Change
  Audit Policy Change                     Success and Failure
  Authentication Policy Change            Success and Failure
  Authorization Policy Change             Success and Failure
  MPSSVC Rule-Level Policy Change         No Auditing
  Filtering Platform Policy Change        Success
  Other Policy Change Events              No Auditing
Account Management
  Computer Account Management             Success and Failure
  Security Group Management               Success and Failure
  Distribution Group Management           Success and Failure
  Application Group Management            Success and Failure
  Other Account Management Events         Success and Failure
  User Account Management                 Success and Failure
DS Access
  Directory Service Access                No Auditing
  Directory Service Changes               Success and Failure
  Directory Service Replication           No Auditing
  Detailed Directory Service Replication  No Auditing
Account Logon
  Kerberos Service Ticket Operations      Success and Failure
  Other Account Logon Events              Success and Failure
  Kerberos Authentication Service         Success and Failure
  Credential Validation                   Success and Failure
</pre>

### EventID:4688でコマンド実行時の引数も出力するように変更
<pre>
C:\Windows\system32>reg add "hklm\software\microsoft\windows\currentversion\policies\system\audit" /v ProcessCreationIncludeCmdLine_Enabled /t REG_DWORD /d 1
The operation completed successfully.

C:\Windows\system32>reg query "hklm\software\microsoft\windows\currentversion\policies\system\audit"

HKEY_LOCAL_MACHINE\software\microsoft\windows\currentversion\policies\system\audit
    ProcessCreationIncludeCmdLine_Enabled    REG_DWORD    0x1
</pre>

### PowerShellの詳細ログも出力するように変更
PowerShell Advanced Logging Level<br>
https://www.cyber.gov.au/sites/default/files/2020-06/PROTECT%20-%20Windows%20Event%20Logging%20and%20Forwarding%20%28June%202020%29.docx
<pre>
Group Policy Setting
Computer Configuration\Policies\Administrative Templates\Windows Components\Windows PowerShell
  Turn on Module Logging
    Enabled
    Module Names: *
  Turn on PowerShell Script Block Logging
    Enabled
</pre>

### Sysmonのログ設定
https://github.com/olafhartong/sysmon-modular
<pre>
Sysmon64.exe -i sysmon-modular-master\sysmonconfig.xml
</pre>
