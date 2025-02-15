# Minimal Elite Dangerous Launcher
Cross-platform launcher for the game Elite Dangerous. Created mainly to avoid the long startup
time of the default launcher when running on Linux. Can be used with Steam, Epic and Frontier
accounts on both Windows and Linux.

![preview-gif]

## Table of Contents
* [Features]
* [Usage]
    * [Setup]
        * [Steam]
        * [Epic]
        * [Frontier]
    * [Arguments]
        * [Shared]
        * [Min-Launcher-Specific]
    * [Settings]
    * [Multi-Account]
        * [Frontier account via Steam or Epic]
        * [Epic account via Steam]
    * [Logs]
    * [Cache]
* [Build]
    * [Release Artifacts]    

## Features
* **Minimal Interface**
  
  No waiting for a GUI to load. No waiting for the embedded web browser to load. No waiting
  for remote logs to be sent to FDev. For those that play on Linux, no waiting for the Wine
  environment.

* **Auto-run**
  
  Automatically starts the game instead of you having to click the _Play_ button. The default
  launcher also supports this by using the `/autorun` [flag].

* **Auto-launch Processes**
  
  Via the [Settings] file, you can specify additional programs to launch automatically. This
  includes things like [Elite Log Agent] and [VoiceAttack]. Processes are automatically closed
  when the game closes. This means you don't have to run the programs on computer startup or
  have to remember to start them before you launch the game.

* **Auto-quit**

  Automatically close the launcher when you close your game. The default launcher also supports
  this by using the `/autoquit` [flag].

* **Auto-restart**

  Automatically restart your game. This can be useful if you're [grinding] for manufactured 
  engineering materials. Off by default.

* **Multi-Account**

  Supports running your Steam, Epic and Frontier Store accounts with one game installation.
  See [details] for how this works.
  
## Usage

This launcher doesn't setup/link new accounts. You'll need to either launch Elite Dangerous at least once with
the default launcher or manually create and link your account(s).

### Setup
#### Steam
1. Download the [latest release] for your operating system
2. Extract executable from the zip/tar archive
3. Place `MinEdLauncher` in your Elite Dangerous install location so that it's in the same folder
   as `EDLaunch.exe`. `MinEdLauncher.Bootstrap` is for Epic only and may be ignored. To find your install
   directory:
    1. Right click _Elite Dangerous_ in your Steam library
    2. Select _properties_
    3. In the _Local Files_ tab, click _Browse Local Files..._
4. Update your launch options to point to `MinEdLauncher`.
    1. Right click _Elite Dangerous_ in your Steam library
    2. Select _properties_
    3. In the _general_ tab, click _Set Launch Options_
    4. Windows users - Set the value to `cmd /c "MinEdLauncher.exe %command% /autorun /autoquit /EDH"`
        
       Linux users - The command will depend on which terminal emulator you use. Examples for
       [alacritty], [gnome-terminal] and [konsole] are below.
          
       `alacritty -e ./MinEdLauncher %command% /autorun /autoquit /EDH`
       
       `gnome-terminal -- ./MinEdLauncher %command% /autorun /autoquit /EDH`
    
       `konsole -e ./MinEdLauncher %command% /autorun /autoquit /EDH`
5. Launch your game as you normally would in Steam
#### Epic
1. Download the [latest release] for Windows
2. Extract executables from the zip archive
3. Navigate to your Elite Dangerous install location (folder that contains `EDLaunch.exe`)
4. Copy `MinEdLauncher.exe`
5. Delete or rename `EDLaunch.exe` (e.g. `EDLaunch.exe.bak`)
6. Copy and rename `MinEdLauncher.Bootstrap.exe` to `EDLaunch.exe`. This step is required because
   Epic doesn't allow changing the game's startup application. If someone knows how to modify the
   game's manifest file (`EliteDangerous/.egstore/*.manifest`), this could be avoided. Please open
   a [new issue] if you can help with this. This also means any time the game updates or you verify
   your game files, you'll have to replace `EDLaunch.exe` with `MinEdLauncher.Bootstrap.exe`.
7. Update your launch options to auto start your preferred product
    1. Click _Settings_ in the Epic Games Launcher
    2. Scroll down to the _Manage Games_ section and click _Elite Dangerous_
    3. Check _Additional Command Line Arguments_
    4. Set the value to `/autorun /autoquit /EDH`
8. Launch your game as you normally would in Epic
#### Frontier
1. Download the [latest release] for Windows
2. Extract executables from the zip archive
3. Place `MinEdLauncher.exe` in your Elite Dangerous install location so that it's in the same folder as `EDLaunch.exe`.
   `MinEdLauncher.Bootstrap` is for Epic only and may be ignored.
4. Create a shortcut to `MinEdLauncher.exe` by right-clicking it and selecting _create shortcut_
5. Right-click the newly made shortcut and select _properties_
6. Add the `/frontier profile-name` argument + your other desired arguments to the end of the _Target_ textbox (e.g. `C:\path\to\MinEdLauncher.exe /frontier profile-name /autorun /autoquit /edh`)
7. Click _Ok_

You can place this shortcut anywhere. It doesn't have to live in the Elite Dangerous install folder.

### Arguments
#### Shared
The following arguments are understood by both the vanilla launcher and the minimal launcher.
Note that specifying a product flag will override whatever option you select when Steam prompts
you to select a version of the game.

| Argument       | Effect                                                    |
|----------------|-----------------------------------------------------------|
| /autoquit      | Automatically close the launcher when the game closes     |
| /autorun       | Automatically start selected product when launcher starts |
| /ed            | Select Elite Dangerous as the startup product             |
| /edh           | Select Elite Dangerous Horizons as the startup product    |
| /eda           | Select Elite Dangerous Arena as the startup product       |
| /edo           | Select Elite Dangerous Odyssey as the startup product     |
| /vr            | Tell the game that you want to play in VR mode            |
| -auth_password | Epic exchange code. Used for authenticating with Epic     |

#### Min Launcher Specific
The following arguments are in addition to the above:

| Argument               | Effect                                                    |
|------------------------|-----------------------------------------------------------|
| /frontier profile-name | Use this argument to login with a Frontier Store account. Keep the profile name to letters, numbers, dashes and underscores only |
| /restart delay         | Restart the game after it has closed with _delay_ being the number of seconds given to cancel the restart (i.e `/restart 3`) |

### Settings
The settings file controls additional settings for the launcher that go beyond what the default
launcher supports. The location of this file is in the standard config location for your
operating system. If this file doesn't exist, it will be created on launcher startup.

Windows: `%LOCALAPPDATA%\min-ed-launcher\settings.json`

Linux: `$XDG_CONFIG_HOME/min-ed-launcher/settings.json` (`~/.config` if `$XDG_CONFIG_HOME` isn't set)

| Settings               | Effect                                                                                                                 |
|------------------------|------------------------------------------------------------------------------------------------------------------------|
| apiUri                 | FDev API base URI. Should only be changed if you are doing local development                                           |
| watchForCrashes        | Determines if the game should be launched by `WatchDog64.exe` or not                                                   |
| gameLocation           | Path to game's install folder. Specify this if the launcher can't figure it out by itself                              |
| language               | Sets the game's language. Supported values are _en_ and the names of the language folders in Elite's install directory |
| autoUpdate             | Automatically update games that are out of date                                                                        |
| maxConcurrentDownloads | Maximum number of simultaneous downloads when downloading updates                                                      |
| forceUpdate            | Be default, Steam and Epic updates are handled by their respective platform. In cases like the Odyssey alpha, FDev doesn't provide updates through Steam or Epic. This allows the launcher to force updates to be done via FDev servers by providing a comma delimited list of SKUs |
| processes              | Additional applications to launch before launching the game                                                            |

When specifying a path for either `gameLocation` or `processes.fileName` on Windows, it's required to escape backslashes. Make sure to use a
double backslash (`\\`) instead of a single backslash (`\`).

```json
{
    "apiUri": "https://api.zaonce.net",
    "watchForCrashes": false,
    "gameLocation": null,
    "language": "en",
    "autoUpdate": true,
    "maxConcurrentDownloads": 4,
    "forceUpdate": "PUBLIC_TEST_SERVER_OD",
    "processes": [
        {
            "fileName": "C:\\path\\to\\app.exe",
            "arguments": "--arg1 --arg2"
        },
        {
            "fileName": "C:\\path\\to\\app2.exe",
            "arguments": "--arg1 --arg2"
        }
    ]
}
```

### Multi-Account
#### Frontier account via Steam or Epic
By using the `/frontier profile-name` argument, you can login with any number of Frontier accounts with a
single game installation. Your launch command might look like the following

Windows: `cmd /c "MinEdLauncher.exe %command% /frontier profile-name /autorun /autoquit /EDH"`

Linux: `alacritty -e ./MinEdLauncher %command% /frontier profile-name /autorun /autoquit /EDH`

See the [setup] section above for how you might run this on your platform.

If you have multiple frontier accounts, use a different profile name for each of them. After successfully
logging in, there will be a `.cred` file created in either `%LOCALAPPDATA%\min-ed-launcher\` or 
`$XDG_CONFIG_DIR/min-ed-launcher/` (depending on OS). This file stores your username, password and machine
token. On Windows, the password and machine token are encrypted via DPAPI (same as the vanilla launcher).
On Linux, no encryption happens but the file permissions are set to 600. If you login once and then decide
you want to login again (refresh machine token), you can delete the appropriate `.cred` file.

#### Epic account via Steam
There is rudimentary support for running your Epic account via Steam. Running a Steam account without Steam installed and running is not supported.

In order to authenticate with an Epic account:
1. You will first need to make sure two files are in your Steam installation directory. You'll likely have to start installing via Epic and
   then when `EosSdk.dll` and `EosIF.dll` are downloaded, you can stop the install (and delete the rest of the files). Once you have those
   two files, copy them to your Steam install directory (so that they are in the same folder as `MinEdLauncher`. It's possible this step will
   become obsolete if FDev updates their Steam depot to include these files.

2. Get an Epic exchange code. This part is really clunky and will need to be done for every launch as the exchange code expires after one use.
   
    Within the Epic launcher, click your username and select manage account. This will open a browser. The URL will contain an `exchangeCode=code`
    parameter. Copy the code before the page is redirected (can just hit the stop button in your browser).
3. Add the `-auth_password=code` argument to your launch options. `cmd /c "MinEdLauncher.exe %command% /autoquit /EDH -auth_password=code"`

   You can also create a separate shortcut. Right click game in your Steam library and create desktop shortcut. Edit the properties of the shortcut
   to include the `-auth_password=code` argument. `steam://rungameid/359320// -auth_password=code` The two trailing slashes are important. Then just
   edit this shortcut with the new exchange code each time instead of changing your Steam launch options.

### Logs
Debug logging is placed in the file `logs/min-ed-launcher.log`

### Cache
When updating your game files, the launcher downloads updates into a temporary directory. You may delete these files at any time.
The location of these files are in the standard cache location for your operating system.

Windows: `%LOCALAPPDATA%\min-ed-launcher\cache`

Linux: `$XDG_CACHE_HOME/min-ed-launcher` (`~/.cache` if `$XDG_CACHE_HOME` isn't set)

## Build
1. Install the [.Net 5 SDK]
2. Run `dotnet build`

### Release Artifacts
Run either `publish.sh` or `publish.ps1` depending on your OS. These scripts make use of `dotnet publish`. Artifacts will end up in the `artifacts`folder.

Note that the bootstrap project uses [NativeAOT] to link and compile the app and the .net 5 runtime into a single native executable. It
specifically targets Windows and won't publish on a non-Windows machine.

[preview-gif]: https://rfvgyhn.blob.core.windows.net/elite-dangerous/min-ed-launcher-demo.gif
[Settings]: #settings
[flag]: #arguments
[setup]: #setup
[Elite Log Agent]: https://github.com/DarkWanderer/Elite-Log-Agent
[VoiceAttack]: https://voiceattack.com/
[details]: #multi-account
[grinding]: https://www.reddit.com/r/EliteDangerous/comments/ggffqq/psa_2020_farming_engineering_materials_a_compiled/
[latest release]: https://github.com/Rfvgyhn/min-ed-launcher/releases
[new issue]: https://github.com/Rfvgyhn/min-ed-launcher/issues
[alacritty]: https://github.com/alacritty/alacritty
[gnome-terminal]: https://wiki.gnome.org/Apps/Terminal
[konsole]: https://konsole.kde.org/
[.Net 5 SDK]: https://dotnet.microsoft.com/download/dotnet/5.0
[NativeAOT]: https://github.com/dotnet/runtimelab/tree/feature/NativeAOT
[Features]: #features
[Usage]: #usage
[Steam]: #steam
[Epic]: #epic
[Frontier]: #frontier
[Arguments]: #arguments
[Shared]: #shared
[Min-Launcher-Specific]: #min-launcher-specific
[Multi-Account]: #multi-account
[Frontier account via Steam or Epic]: #frontier-account-via-steam-or-epic
[Epic account via Steam]: #epic-account-via-steam
[Logs]: #logs
[Cache]: #cache
[Build]: #build
[Release Artifacts]: #release-artifacts
