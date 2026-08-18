# TestApp


Pre-checks (30 sec)
A. Internet egress (needed to install ART):

(iwr https://raw.githubusercontent.com -UseBasicParsing -Method Head).StatusCode
If this errors, tell me - we'll stage ART another way.

B. Is Office installed? (decides which macro test):

Test-Path "$env:ProgramFiles\Microsoft Office\root\Office16\WINWORD.EXE"
True -> real Office macro test (T1204.002).
False -> VBScript macro-language test instead (T1059.005) - same script-execution behavior without needing Word.
Step 1 - Install Atomic Red Team (type these 3 lines)
Set-ExecutionPolicy Bypass -Scope Process -Force
IEX (IWR 'https://raw.githubusercontent.com/redcanaryco/invoke-atomicredteam/master/install-atomicredteam.ps1' -UseBasicParsing); Install-AtomicRedTeam -getAtomics -Force
Import-Module "C:\AtomicRedTeam\invoke-atomicredteam\Invoke-AtomicRedTeam.psd1" -Force
Step 2 - List the macro tests and pick one
Invoke-AtomicTest T1204.002 -ShowDetailsBrief
Look for the entry described as an Office/macro document spawning a process. Note its test number (e.g. -TestNumbers 1).

(If Office = False, use Invoke-AtomicTest T1059.005 -ShowDetailsBrief and pick the cscript/VBScript one.)

Step 3 - Preview exactly what it will do (don't skip - you want to know before it runs)
Invoke-AtomicTest T1204.002 -TestNumbers 1 -ShowDetails
Read the commands. Make sure it's benign (spawns cmd/calc/writes a marker file) - not one that downloads real malware. Then stage prereqs:

Invoke-AtomicTest T1204.002 -TestNumbers 1 -GetPrereqs
Step 4 - Start evidence capture
Start-Transcript -Path "$env:USERPROFILE\Desktop\ART_T1204_macro.txt"
Get-Date
(Note the timestamp - you'll match it in FortiEDR/Wazuh.)

Step 5 - Execute the macro test
Invoke-AtomicTest T1204.002 -TestNumbers 1
Step 6 - Observe in both consoles (this is the actual result)
FortiEDR -> Event Viewer, filter device VPC-IT-SOC03. Look for the event; note action = Blocked / Simulated-Block / Log. Screenshot it. (Grab it now - remember the 6-day retention.)
Wazuh -> filter agent.name: VPC-IT-SOC03, look for the Office-spawns-shell chain and the rule.description / MITRE tag. Screenshot it.
Step 7 - Cleanup + stop transcript
Invoke-AtomicTest T1204.002 -TestNumbers 1 -Cleanup
Stop-Transcript
