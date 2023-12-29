<# : batch portion (begins PowerShell multiline comment block)
@echo off & setlocal

set /P "=Waiting for KeyPress... "<NUL

rem # re-launch self with PowerShell interpreter
powershell -noprofile "iex (${%~f0} | out-string)"

echo Pressed.  Toodles.

goto :EOF
: end batch / begin PowerShell chimera #>

# import GetAsyncKeyState()
Add-Type user32_dll @'
    [DllImport("user32.dll")]
    public static extern short GetAsyncKeyState(int vKey);
'@ -namespace System

# for Keys object constants
Add-Type -As System.Windows.Forms

function keyPressed($key) {
    return [user32_dll]::GetAsyncKeyState([Windows.Forms.Keys]::$key) -band 32768
}
# Set your Key to Kill cs2.exe
while ($true) {
    $num9 = keyPressed "Numpad9"
    if ($num9) { taskkill /IM cs2.exe /F }
    start-sleep -milliseconds 40
}

$Host.UI.RawUI.FlushInputBuffer()