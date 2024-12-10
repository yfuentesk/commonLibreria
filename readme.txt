@echo off
setlocal enabledelayedexpansion

:: Prompt the user for the file and starting number
echo Select the .feature file to process:
set /p featureFile=Enter the full path of the file: 
set /p startNum=Enter the starting number: 

set tempFile=%featureFile%.tmp
set /a currentNum=%startNum%

:: Process the file
echo Processing the file...
(for /f "usebackq delims=" %%A in ("%featureFile%") do (
    set line=%%A
    if "!line:Scenario:=!"=="!line!" (
        :: If no 'Scenario:' prefix, leave the line unchanged
        echo !line! >> "!tempFile!"
    ) else (
        :: Check if 'Scenario:' already has a number
        echo "!line!" | findstr /r "^Scenario:[0-9]" >nul
        if not errorlevel 1 (
            :: Scenario already has a number, keep it as is
            echo !line! >> "!tempFile!"
        ) else (
            :: Scenario has no number, add the current sequential ID
            echo Scenario:!currentNum! !line:Scenario:=! >> "!tempFile!"
            set /a currentNum+=1
        )
    )
)) 

:: Replace the original file with the processed file
move /y "!tempFile!" "%featureFile%"
echo Processing complete! Updated file: %featureFile%

pause
