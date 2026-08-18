'<html><head><script language="VBScript">Set s=CreateObject("WScript.Shell"):s.Run "cmd /c whoami > %TEMP%\hta_test.txt",0,True:window.close</script></head></html>' | Out-File $env:TEMP\test.hta -Encoding ascii

mshta.exe $env:TEMP\test.hta

Get-Content $env:TEMP\hta_test.txt

