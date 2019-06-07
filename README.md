# winestall
Bash script for installing and maintaining software in wine prefixes

## Introduction
This script is not a replacement for more sophisticated tools like PlayOnLinux and the like.
It tries to follow the "small is beautiful" philosophy of Unix-like operating systems.
It builds in sanity checks to prevent users from making possibly regrettable mistakes.

## Quick Help
Winestall runs a specified file, wine builtin, or native OS command
with reference to a specified wine prefix.

    USAGE: winestall <path> <program> <args>
           winestall <path> <program> winecfg <args>
           winestall <path> <program> nolog <args>
           winestall <path> <program> winecfg_nolog <args>

-   The 'winecfg' argument forces winecfg to run before the program.
-   The 'nolog' argument prevents logging.
-   The 'winecfg_nolog' argument does both the above.

One can use either absolute or relative paths.
The relative paths will be converted to absolute paths.

-   An absolute path is specified from root, e.g.: `/home/user/dir`
-   A relative path looks more like the following: `~/dir`

Set the `MY_WINE` environment variable if you have a custom
wine binary. Otherwise this script prefers a wine binary in
`/usr/local/bin` over `/usr/bin`.

The script logs console output to log files in `/var/tmp`, formatted as
`winestall_`YYYYMMDD_HHMMSS`.log` in order to preserve log file contents
should a failure situation cause a reboot.

## Post-install Tasks
Any `.desktop` files should be in `~/.local/share/applications/wine`, although
one also may see other files placed on the desktop.
Icons likely are under `~/.local/share/icons`. The script optionally lists
the files of these directories recursively, which then can be found in the log.

Editing `.desktop` files and managing icons are up to the user, especially given the diversity
of desktop environments and window managers.

## Design of the script
The winestall script does the following:

-   It tries to prevent nonsensical or malicious input.
-   It interacts with the user at critical points to mitigate potential damage.
-   It either sets up a new wine prefix or determines whether or not the
    specified path is a valid wine prefix.
-   After determining whether or not special arguments have been used, the script tries to
    execute, e.g., `somefile.exe` in wine.
-   Failing that, it tries to execute `some_program` that wine will see as an executable
    "built-in command." Note that `winestall /my_path winecfg` runs `winecfg` as a built-in
    command, while `winestall ~/my_path program winecfg` first runs `winecfg`, then `program`.
-   Failing that, it interprets `program` as a native OS command and tries to run it in the
    specified wine prefix.

Be warned, howver, that winestall does not guarantee how that command might work! For example,
`winestall ~/my_path ls nolog` creates no log file, fails to run `ls` as a wine built-in,
then runs `ls` in the current working directory, not in the wine prefix.
