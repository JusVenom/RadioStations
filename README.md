# Play-Radio-Stations.bat

### This is a .bat i created to play a Variety of PreDefined Radio Stations.

* #### Info:

  * __Portable (Move it anywhere -But may need to re-Locate Library -See Above PNG)__
  * __Customizable (Add/Edit/Remove Stations in StationList.bat or the .m3u/.pls Files)__
  * __Discreet (Starts in Mini Player View - A Small Box that Sits in Bottom Right Corner of Desktop Background)__


* #### How To:

  * __Extract the MusicBee.7z Archive.. Create a Shortcut to.. Play-Radio-Stations.bat Anywhere..__
  * __Run The Shortcut, Press C and Choose a Station (or press nothing and it will load the last played station)__
  * __ONLY ON FIRST RUN Will it Prompt you to Locate the Library.. Just Hit Locate and Open.. MusicBee\Library\MusicBeeLibrary.mbl__


##### Sharing is Caring, Enjoy :)
###### Easy Download Link -> https://github.com/JusVenom/RadioStations/raw/main/MusicBee.7z


* ###### The .bat Code:

```

@echo off
Title    -  Play Radio Stations  -
SETLOCAL ENABLEDELAYEDEXPANSION ENABLEEXTENSIONS
color f0
if exist "%~dp0PlayListDir.ini" (set /p _PlayListDir=<"%~dp0PlayListDir.ini")
if exist "%~dp0LastStation.ini" (set /p _LastStation=<"%~dp0LastStation.ini")
if exist "%~dp0LastStationName.ini" (set /p _LastStationName=<"%~dp0LastStationName.ini")
if exist "%~dp0StationCount.ini" (set /p _MBPCDC=<"%~dp0StationCount.ini")
if /i not "%~1" == "Return" goto :_SkipReturnCode
Call :_DefColors
shift
goto :%~1 2>nul
:_SkipReturnCode
Call :_DefColors

REM  -  Get Radio-PlayList URLS - Get .m3u and .pls Playlists -
:: If the Link You want Works Fine in say.. FirefoxWebBrowser it should work here for sure.
:: http://www.radiofeeds.co.uk/mp3.asp (Right Click -CopyUrlLocation)
:: http://media-ice.musicradio.com/ (Download .m3u)
:: https://directory.shoutcast.com/ (Click DownloadIcon -NextToPlayIcon)
:: https://www.internet-radio.com/ (Download .m3u/.pls)
:: https://directory.shoutcast.com/ (Download .m3u/.pls)
:: https://onlineradiobox.com/uk/ (Right click PlayIcon-InspectElement.. Copy URL)


:: Set MusicBee Location..
Set "_MusBe=%~dp0MusicBee.exe"
if "%_PlayListDir%" == "" (Set "_PlayListDir=%~dp0Library\Playlists")

:MainOptions
echo.&echo  AutoPlays Last Radio Station..%gc1% %_LastStationName% %rtc%
echo.&echo %gc1% C %mc1%-%bc1% Choose a Station%rtc% (StationList.bat - .m3u/.pls)
echo.&echo %gc1% M %mc1%-%bc1% Move PlayList Dir%rtc% (AutoDetects .m3u .pls)
echo.&echo %gc1% A %mc1%-%bc1% Add a New Station%rtc% (StationList.bat)
echo.&echo %gc1% D %mc1%-%bc1% Delete a Station%rtc% (StationList.bat)
choice /n /c cmadl /d l /t 8
if "%errorlevel%" == "1" Call :ChooseStation
if "%errorlevel%" == "2" Call :ChangePlayListDir
if "%errorlevel%" == "3" Call :AddStation
if "%errorlevel%" == "4" Call :DeleteStation
if "%errorlevel%" == "5" Call :LoadStation "%_LastStation%" &exit
goto MainOptions

:ChooseStation
cls&echo.&echo %mc1% --- %CC1% StationList.bat Content: %mc1% ---%rtc% &echo.
Set /a "MBPC=-1"
for /F "usebackq delims==" %%a in ("%~dp0StationList.bat") do (
	Set /a MBPC=!MBPC!+1
	Set "MBPT=%%a" & Set "MBPN=!MBPT:~4!"
	if not "%%a" == "@echo off" echo %bc1% !MBPC! %mc1%-%rtc% !MBPN!
	set "Res!MBPC!=!MBPN!"
	)

:: Maybe add the option to delete these m3u files too.. easy.. no..

echo.&echo %mc1% --- %CC1% %_PlayListDir:~-27%Content: *.m3u + *.pls %mc1% ---%rtc% &echo.


cd /d "%_PlayListDir%"
for /f "tokens=* delims=" %%a in ('dir/b/s/on "*.m3u" 2^>nul') do (
	Set /a MBPC=!MBPC!+1
	echo %bc1% !MBPC! %mc1%-%rtc% %%~nxa
	set "Res!MBPC!=%%a"
	)
for /f "tokens=* delims=" %%a in ('dir/b/s/on "*.pls" 2^>nul') do (
	Set /a MBPC=!MBPC!+1
	echo %bc1% !MBPC! %mc1%-%rtc% %%~nxa
	set "Res!MBPC!=%%a"
	)

echo !MBPC!>"%~dp0StationCount.ini"

if "%~1" == "Ret" exit /b
cd /d %~dp0
echo.&echo %bc1% Type the Number of the Station to Play%rtc%.. Then Press Enter.
Set /p "LSNum="
For /L %%i in (1,1,!MBPC!) Do (
	If "!LSNum!" EQU "%%i" (
		set "_LastStation=!res%%i!"
		Call :LoadStation "!_LastStation!"
		)
	)

exit

:LoadStation <StationName / Name.m3u / Name.pls>
Call StationList.bat >nul 2>&1
echo %~1>"%~dp0LastStationName.ini"
Set "_ChkLSLSPL=%~1"
Set "_ChkLSLSSL=!%_ChkLSLSPL%!"
if /i "!_ChkLSLSSL:~0,5!" == "http:" (Set _LSFOLnk=Y) ELSE (Set _LSFOLnk=N)
if "!_LSFOLnk!" == "Y" (
	echo !_ChkLSLSSL!>"%~dp0LastStation.ini"
	Start "" "%_MusBe%" /play !_ChkLSLSSL! )
if "!_LSFOLnk!" == "N" (
	echo !_ChkLSLSPL!>"%~dp0LastStation.ini"
	Start "" "%_MusBe%" /play !_ChkLSLSPL! )
exit

:ChangePlayListDir
cls&echo.&echo %bc1% Any .m3u or .pls Files in the Folder You Select will be%rtc%.. &timeout 1 >nul
echo.&echo  %bc1% Automatically Displayed in Results as Their File Name%rtc%.. &timeout 2 >nul
echo.&echo    The Default Playlist Folder is.. %%~dp0\Library\Playlists &timeout 2 >nul
echo.&echo    %bc1% Press the Cancel Button to Reset to Default%rtc%.. &timeout 8 >nul
"%~dp0OpenFolderBox.exe" /MD "%userprofile%" "Select a Folder!" >"%~dp0PlayListDir.ini"
set /p _PlayListDir=<"%~dp0PlayListDir.ini"
if "%errorlevel%" == "1" (echo %~dp0Library\Playlists >"%~dp0PlayListDir.ini")
exit /b

:AddStation
NET SESSION >nul 2>&1 || (
	cls&echo.&echo %rc1%Note: %bc1%Administrator Permission is Required..%gc1% Press Yes%rtc%..&timeout 3 >nul
	Start "" "%~dp0\elevate.exe" "%~f0" Return AddStation & exit
	)
Title    -  Add Radio Stations  -
SETLOCAL ENABLEDELAYEDEXPANSION ENABLEEXTENSIONS
echo.&echo %bc1% Type a Name For the New Station-URL%rtc%.. Then Hit Enter.
Set /p _name=
echo.&echo %bc1% Paste-Type the Station-Url%rtc%.. Then Hit Enter.
echo.&echo %gc1% Tip: %rtc%Download .m3u or .pls and Open With TextEditor to Get URL.
Set /p _url=
if not exist "%~dp0StationList.bat" echo ^@echo off>"%~dp0StationList.bat"
Call :AddAStat
goto MainOptions
::ALT - Setx "%_name%" "%_url%" /m
:AddAStat
Set "_NamUrl=!_Name!=!_Url!" &Set "_UrlExist="
:: If _Name contains Spaces.. Create _NamUrl Including Sorrounding Quotes
if not "!_Name!"=="!_Name: =!" (Set "_NamUrl="!_Name!=!_Url!"")
for /F "delims=" %%V in ('findstr /C:"!_Url!" "%~dp0StationList.bat"') do (
	Set _UrlExist=Y
	)
if "!_UrlExist!" == "" (echo Set %_NamUrl%>>"%~dp0StationList.bat")
exit /b

:DeleteStation
NET SESSION >nul 2>&1 || (
	cls&echo.&echo %rc1%Note: %bc1%Administrator Permission is Required..%gc1% Press Yes%rtc%..&timeout 3 >nul
	Start "" "%~dp0\elevate.exe" "%~f0" Return DeleteStation & exit
	)
Title    -  Delete Radio Stations  -
::SETLOCAL ENABLEDELAYEDEXPANSION ENABLEEXTENSIONS
Call :ChooseStation Ret
echo.&echo %bc1% Type the Name of the Station to Delete%rtc%.. Then Press Enter.
echo.&echo  %rc2% Warning: %rc1%Any Lines/Stations Containing The Name Will Be Deleted%rtc%..
echo.&echo  %gc1% Note: To Delete All Stations Type.. %cc1%AllSL or AllPL%rtc%.. then Press Enter.
echo.&echo  %gc1% Note: %cc1%AllSL%mc1% (All From StationList.bat) %cc1% AllPL%mc1% (All From PlayListDir)%rtc%
Set /p Stat2DelN=
if /i "%Stat2DelN%" == "AllSL" (Del /f /q "%~dp0StationList.bat")
if /i "%Stat2DelN%" == "AllPL" (Del /f /q "%_Playlistdir%\*.*")

For /L %%i in (1,1,!_MBPCDC!) Do (
	If "!Stat2DelN!" EQU "%%i" (
	set "DelStat=!res%%i!")
	)

if "%DelStat:~-4,-3%" == "." (Del /f /q "%DelStat%") ELSE ( Call :DelStatRemLines )
goto MainOptions
::ALT - REG delete "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /F /V %rtodel%
REM ALT - SetX Without /m Switch DelReg is.. HKEY_CURRENT_USER\Environment

:DelStatRemLines
:: DeQuote - Remove All Quotes
Set "DelStat=%DelStat:"=%"
REM - Remove All Lines Containing Certain Words.. From Files. (/c:"%DelStat%")
findstr /v /i /c:%DelStat% "%~dp0StationList.bat" >"%~dp0StationList2.bat"
Del /f /q "%~dp0StationList.bat"
cd /d %~dp0 & Ren "StationList2.bat" "StationList.bat" >nul
exit /b

:_DefColors
set "_OSBUILD="
for /f %%A in ('WMIC OS Get BuildNumber 2^>nul ^| findstr /r "[0-9]"') do set "_OSBUILD=%%A"
if %_OSBUILD% GEQ 10586 (
	set "_ResetColors=[0m" && set "_BKBRIGHT=[1m"
	:: Dark Text Colours:  Black - Red - Green - Yellow - Blue - Magenta - Cyan - White
	set "BLC1=[30m" && set "RC1=[31m" && set "GC1=[32m" &&	set "YC1=[33m" && set "BC1=[34m" && set "MC1=[35m" && set "CC1=[36m" && set "WC1=[97m"
	:: Bright Text Colours
	set "BLC2=[90m" && set "RC2=[91m" && set "GC2=[92m" &&	set "YC2=[93m" && set "BC2=[94m" && set "MC2=[95m" && set "CC2=[96m" && set "WC2=[97m"
	:: Dark Background Colours
	set "BKBLACK1=[40m" && set "BKRED1=[41m" && set "BKGREEN1=[42m" && set "BKYELLOW1=[43m" && set "BKBLUE1=[44m" && set "BKMAGENTA1=[45m"
	set "BKCYAN1=[46m" && set "BKWHITE1=[47m"
	:: Bright Background Colours
	set "BKBLACK2=[100m" && set "BKRED2=[101m" && set "BKGREEN2=[102m" && set "BKYELLOW2=[103m" && set "BKBLUE2=[104m" && set "BKMAGENTA2=[105m"
	set "BKCYAN2=[106m" && set "BKWHITE2=[107m"
)
Set "rtc=%BKwhite2%%blc1%"
Call :Reaper "" %rc1%
exit /b
:Reaper <Color / bc1 / mc1> <TagColor>
if not "%~1" == "" (Set "_ReCo=%~1") ELSE (Set "_ReCo=%rtc%")
if not "%~2" == "" (Set "_VRTag=%~2")
echo %_ReCo%                                ...%rtc%
echo %_ReCo%                              ;::::;%rtc%
echo %_ReCo%                            ;::::; :;%rtc%
echo %_ReCo%                          ;:::::'   :;%rtc%
echo %_ReCo%                         ;:::::;     ;.%rtc%
echo %_ReCo%                        ,:::::'   %_VRTag%V%_ReCo%   ;           OOO\%rtc%
echo %_ReCo%                        ::::::;   %_VRTag%3%_ReCo%   ;          OOOOO\%rtc%
echo %_ReCo%                        ;:::::;   %_VRTag%D%_ReCo%   ;         OOOOOOOO%rtc%
echo %_ReCo%                       ,;::::::;     ;'         / OOOOOOO%rtc%
echo %_ReCo%                     ;:::::::::`. ,,,;.        /  / DOOOOOO%rtc%
echo %_ReCo%                   .';:::::::::::::::::;,     /  /     DOOOO%rtc%
echo %_ReCo%                  ,::::::;::::::;;;;::::;,   /  /        DOOO%rtc%
echo %_ReCo%                 ;`::::::`'::::::;;;::::: ,#/  /          DOOO%rtc%
echo %_ReCo%                 :`:::::::`;::::::;;::: ;::#  /            DOOO%rtc%
echo %_ReCo%                 ::`:::::::`;:::::::: ;::::# /              DOO%rtc%
echo %_ReCo%                 `:`:::::::`;:::::: ;::::::#/               DOO%rtc%
echo %_ReCo%                  :::`:::::::`;; ;:::::::::##                OO%rtc%
echo %_ReCo%                  ::::`:::::::`;::::::::;:::#                OO%rtc%
echo %_ReCo%                  `:::::`::::::::::::;'`:;::#                O %rtc%
timeout 1 >nul
Set "_ReCo=" &Set "_VRTag="
exit /b
exit
:: Default Station Set
Set KissFM=http://149.56.185.82:8131/
Set Kisstory=http://www.radiofeeds.co.uk/bauerflash.pls?station=kisstory.mp3.m3u
Set FreeRadio=http://www.radiofeeds.co.uk/bauerflash.pls?station=freebirmingham.mp3.m3u
Set CapitalUK=http://media-ice.musicradio.com:80/CapitalMP3
Set Hot108JamzHitz=http://108.61.30.179:4000/
Set 1FMTop40=http://185.33.22.15:11251
Set 1FMAbTop40=http://185.33.22.15:11251
Set AnBTop40=http://mp3channels.webradio.antenne.de/top-40
Set RegNation=http://listen.radionomy.com/ReggaeNation
Set RegDanHalPondEnds=http://s7.voscast.com:7000
Set HolinsRegat=http://us1.internet-radio.com:8070/
Set DanceWave=http://stream.dancewave.online:8080/dance.mp3

```


