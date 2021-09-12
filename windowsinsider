@echo off

::Credits to @jimmyblue00. You can mod it to create the version you want 
For /f "tokens=4,5,6 delims=[]. " %%G in ('ver') Do (set _major=%%G& set _minor=%%H& set _build=%%I)

if %_build% LSS 17763 (
    echo =========================================================
    echo Only Windows 10 RS5 and later are compatible to run this.
    echo =========================================================
    echo.
    pause
    goto :EOF
)

REG QUERY HKU\S-1-5-19\Environment >NUL 2>&1
IF %ERRORLEVEL% EQU 0 goto :START_SCRIPT

echo =============================================
echo You need to execute this as an administrator.
echo =============================================
echo.
pause
goto :EOF

:START_SCRIPT
set "scriptver=2.5.0"
set "FlightSigningEnabled=0"
bcdedit /enum {current} | findstr /I /R /C:"^flightsigning *Yes$" >NUL 2>&1
IF %ERRORLEVEL% EQU 0 set "FlightSigningEnabled=1"

:CHOICE_MENU
cls
set "choice="
echo Windows Insider Program v%scriptver%
echo.
echo 1 - Enroll to Dev Channel
echo Ideal for highly technical users. Be the first to access the latest Windows 11 builds earliest in the development cycle with the newest code. There will be some rough edges and low stability.
echo.
echo 2 - Enroll to Beta Channel (Recommend)
echo Ideal for early adopters. These Windows 11 builds will be more reliable than builds from our Dev Channel, with updates validated by Microsoft. Your feedback has the greatest impact here.
echo.
echo 3 - Enroll to Release Preview Channel
echo Ideal if you want to preview fixes and certain key features, plus get optional access to the next version of Windows 10 before it's generally available to the world. This channel is also recommend for commercial users.
echo.
echo 4 - Stop receiving Insider Preview builds (requires Reboot)
echo Ideal if you want to stop receiving and unenrolling to the Windows Insider Preview builds. Your machine will on the current Windows Insider Preview build, but you will not receive any more Windows Insider Preview build.
echo.
echo 5 - Quit without making any changes
echo.
echo.
set /p choice="Choice: "
echo.
if /I "%choice%"=="1" goto :ENROLL_DEV
if /I "%choice%"=="2" goto :ENROLL_BETA
if /I "%choice%"=="3" goto :ENROLL_RP
if /I "%choice%"=="4" goto :STOP_INSIDER
if /I "%choice%"=="5" goto :EOF
goto :CHOICE_MENU

:ENROLL_RP
set "Channel=ReleasePreview"
set "Fancy=Release Preview Channel"
set "BRL=8"
goto :ENROLL

:ENROLL_BETA
set "Channel=Beta"
set "Fancy=Beta Channel"
set "BRL=4"
goto :ENROLL

:ENROLL_DEV
set "Channel=Dev"
set "Fancy=Dev Channel"
set "BRL=2"
goto :ENROLL

:RESET_INSIDER_CONFIG
reg delete "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsSelfHost\Account" /f
reg delete "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsSelfHost\Applicability" /f
reg delete "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsSelfHost\Cache" /f
reg delete "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsSelfHost\UI" /f
reg delete "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\WindowsUpdate\SLS\Programs\WUMUDCat" /f
reg delete "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\WindowsUpdate\SLS\Programs\RingExternal" /f
reg delete "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\WindowsUpdate\SLS\Programs\RingPreview" /f
reg delete "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\WindowsUpdate\SLS\Programs\RingInsiderSlow" /f
reg delete "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\WindowsUpdate\SLS\Programs\RingInsiderFast" /f
reg delete "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" /v AllowTelemetry /f
reg delete "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /f
reg delete "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate" /v BranchReadinessLevel /f
goto :EOF

:ADD_INSIDER_CONFIG
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\WindowsUpdate\Orchestrator" /t REG_DWORD /v EnableUUPScan /d 1 /f
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\WindowsUpdate\SLS\Programs\RingExternal" /t REG_DWORD /v Enabled /d 1 /f
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\WindowsUpdate\SLS\Programs\WUMUDCat" /t REG_DWORD /v WUMUDCATEnabled /d 1 /f
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsSelfHost\Applicability" /t REG_DWORD /v EnablePreviewBuilds /d 2 /f
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsSelfHost\Applicability" /t REG_DWORD /v IsBuildFlightingEnabled /d 1 /f
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsSelfHost\Applicability" /t REG_DWORD /v IsConfigSettingsFlightingEnabled /d 1 /f
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsSelfHost\Applicability" /t REG_DWORD /v TestFlags /d 32 /f
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsSelfHost\Applicability" /t REG_DWORD /v RingId /d 11 /f
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsSelfHost\Applicability" /t REG_SZ /v Ring /d "External" /f
rem reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsSelfHost\Applicability" /t REG_SZ /v ContentType /d "Mainline" /f
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsSelfHost\Applicability" /t REG_SZ /v BranchName /d "%Channel%" /f
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsSelfHost\UI\Strings" /t REG_SZ /v StickyXaml /d "<StackPanel xmlns="^""http://schemas.microsoft.com/winfx/2006/xaml/presentation"^""><TextBlock Style="^""{StaticResource BodyTextBlockStyle }"^"">This device is enrolled in the Windows Insider Program. You will receive Windows Insider builds based on your selected Channel. <Hyperlink NavigateUri="^""https://docs.microsoft.com/en-us/windows-insider/get-started"^"" TextDecorations="^""None"^"">Learn more</Hyperlink></TextBlock><TextBlock Text="^""Applied configuration"^"" Margin="^""0,20,0,10"^"" Style="^""{StaticResource SubtitleTextBlockStyle}"^"" /><TextBlock Style="^""{StaticResource BodyTextBlockStyle }"^"" Margin="^""0,0,0,5"^""><Run FontFamily="^""Segoe MDL2 Assets"^"">&#xECA7;</Run> <Span FontWeight="^""SemiBold"^"">%Fancy%</Span></TextBlock><TextBlock Text="^""Channel: %build%"^"" Style="^""{StaticResource BodyTextBlockStyle }"^"" /><TextBlock Text="^""Content: Mainline"^"" Style="^""{StaticResource BodyTextBlockStyle }"^"" /><TextBlock Text="^""Telemetry settings notice"^"" Margin="^""0,20,0,10"^"" Style="^""{StaticResource SubtitleTextBlockStyle}"^"" /><TextBlock Style="^""{StaticResource BodyTextBlockStyle }"^"">Windows Insider Program requires your diagnostic data collection settings to be set to <Span FontWeight="^""SemiBold"^"">Full</Span>. You can verify or modify your current settings in <Span FontWeight="^""SemiBold"^"">Diagnostics &amp; feedback</Span>.</TextBlock><Button Command="^""{StaticResource ActivateUriCommand}"^"" CommandParameter="^""ms-settings:privacy-feedback"^"" Margin="^""0,10,0,0"^""><TextBlock Margin="^""5,0,5,0"^"">Open Diagnostics &amp; feedback</TextBlock></Button></StackPanel>" /f
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsSelfHost\UI\Visibility" /t REG_DWORD /v UIHiddenElements /d 65535 /f
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsSelfHost\UI\Visibility" /t REG_DWORD /v UIDisabledElements /d 65535 /f
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsSelfHost\UI\Visibility" /t REG_DWORD /v UIServiceDrivenElementVisibility /d 0 /f
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsSelfHost\UI\Visibility" /t REG_DWORD /v UIErrorMessageVisibility /d 192 /f
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\DataCollection" /t REG_DWORD /v AllowTelemetry /d 3 /f
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate" /t REG_DWORD /v BranchReadinessLevel /d %BRL% /f
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsSelfHost\UI\Strings" /t REG_SZ /v StickyMessage /d "{"^""Message"^"":"^""You are currently on %Channel% Channel."^"","^""LinkTitle"^"":"^"""^"","^""LinkUrl"^"":"^"""^"","^""DynamicXaml"^"":"^""<StackPanel xmlns=\\"^""http://schemas.microsoft.com/winfx/2006/xaml/presentation\\"^""><TextBlock Style=\\"^""{StaticResource BodyTextBlockStyle }\\"^"">This device is enrolled in the Windows Insider Program. You will receive Windows Insider builds based on your selected Channel. <Hyperlink NavigateUri=\\"^""https://docs.microsoft.com/en-us/windows-insider/get-started\\"^"" TextDecorations=\\"^""None\\"^"">Learn more</Hyperlink></TextBlock><TextBlock Text=\\"^""Applied configuration\\"^"" Margin=\\"^""0,20,0,10\\"^"" Style=\\"^""{StaticResource SubtitleTextBlockStyle}\\"^"" /><TextBlock Style=\\"^""{StaticResource BodyTextBlockStyle }\\"^"" Margin=\\"^""0,0,0,5\\"^""><Run FontFamily=\\"^""Segoe MDL2 Assets\\"^"">&#xECA7;</Run> <Span FontWeight=\\"^""SemiBold\\"^"">%Fancy%</Span></TextBlock><TextBlock Text=\\"^""Build number: %_major%.%_minor%.%_build% \\"^"" Style=\\"^""{StaticResource BodyTextBlockStyle }\\"^"" /><TextBlock Text=\\"^""Content: Mainline\\"^"" Style=\\"^""{StaticResource BodyTextBlockStyle }\\"^"" /><TextBlock Text=\\"^""Telemetry settings notice\\"^"" Margin=\\"^""0,20,0,10\\"^"" Style=\\"^""{StaticResource SubtitleTextBlockStyle}\\"^"" /><TextBlock Style=\\"^""{StaticResource BodyTextBlockStyle }\\"^"">Windows Insider Program requires your diagnostic data collection settings to be set to <Span FontWeight=\\"^""SemiBold\\"^"">Full</Span>. You can verify or modify your current settings in <Span FontWeight=\\"^""SemiBold\\"^"">Diagnostics &amp; feedback</Span>.</TextBlock><Button Command=\\"^""{StaticResource ActivateUriCommand}\\"^"" CommandParameter=\\"^""ms-settings:privacy-feedback\\"^"" Margin=\\"^""0,10,0,0\\"^""><TextBlock Margin=\\"^""5,0,5,0\\"^"">Open Diagnostics &amp; feedback</TextBlock></Button></StackPanel>"^"","^""Severity"^"":0}" /f
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsSelfHost\UI\Visibility" /t REG_DWORD /v UIHiddenElements_Rejuv /d 65534 /f
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\WindowsSelfHost\UI\Visibility" /t REG_DWORD /v UIDisabledElements_Rejuv /d 65535 /f
goto :EOF

:ENROLL
echo Applying changes...
call :RESET_INSIDER_CONFIG 1>NUL 2>NUL
call :ADD_INSIDER_CONFIG 1>NUL 2>NUL
bcdedit /set {current} flightsigning yes >NUL 2>&1
echo Done.

echo.
IF %FlightSigningEnabled% NEQ 1 goto :ASK_FOR_REBOOT
pause
goto :EOF

:STOP_INSIDER
echo Applying changes...
call :RESET_INSIDER_CONFIG 1>NUL 2>NUL
bcdedit /deletevalue {current} flightsigning >NUL 2>&1
echo Done.

echo.
IF %FlightSigningEnabled% NEQ 0 goto :ASK_FOR_REBOOT
pause
goto :EOF

:ASK_FOR_REBOOT
set "choice="
echo A reboot is required to finish applying changes.
set /p choice="Would you like to reboot your PC? (y/N) "
if /I "%choice%"=="y" shutdown -r -t 0
goto :EOF
