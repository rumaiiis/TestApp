'CreateObject("WScript.Shell").Run "cmd /c whoami > %TEMP%\macro_test.txt"' | Out-File $env:TEMP\macro_test.vbs -Encoding ascii; cscript //nologo $env:TEMP\macro_test.vbs

'CreateObject("WScript.Shell").Run "cmd /c whoami > %TEMP%\macro_test.txt"' | Out-File $env:TEMP\macro_test.vbs -Encoding ascii; cscript //nologo $env:TEMP\macro_test.vbs

Get-Content $env:TEMP\macro_test.txt

