# smali-trix
Some shell tricks for manipulating/collecting data from smali class files and other debugging tricks/tips I use.

#### Extracting all the types
```
grep -hr '^\.class.*;' <path to source root>/smali | sed -n 's/^\.class.*\s\(L.*;\)/\1/p' > types.txt
```
##### Output
```
Landroid/arch/a/b/b$e;
Landroid/arch/lifecycle/LiveData$a;
Landroid/support/c/a/h;
Landroid/support/v4/a/f;
...
```

#### Extract all strings
```
grep -hEr '^\s+const-string.*"' <path to source root>/smali | sed -n 's/^.*"\(.*\)"/\1/p' | sed -E '/^\s*$/d'
```
##### Output (note: empty lines or lines consisting of spaces are removed from the output)
```
al_applink_data
An entry modification is not supported
=
[
,
]
...
```

#### Changing a package name globally
Note: This will only change the package name in non-binary files. If the package name is found in any of the apk's binary files or inside the AndroidManifest.xml then the apk will become invalid. It should be relatively simple though to correct the name inside AndroidManifest.xml and other text-based files not found in the smali of the app.
```
cd <path to source root>/smali; sed -Ei 's/Lcom\/example\/pkg;/Lnew\/pkg\/name;'
```

#### Connecting GDB to an android app
Note: You must enable the `android:debuggable` flag inside the `<application />` tag in AndroidManifest.xml for this to work.
```
<enable usb debugging on your phone and connect your phone to your pc via usb>
adb push $NDK_PATH/prebuilt/android-<arch>/gdbserver/gdbserver /data/local/tmp
adb connect <LAN IP Address of your phone>
<optionally unplug device from pc>
adb shell

-- in device shell now --
run-as <pkg name of the app> cp /data/local/tmp/gdbserver .
run-as <pkg name of the app> chmod 755 gdbserver
<start the app>
run-as <pkg name of the app> ./gdbserver --once --attach $GDB_HOST_IP:$GDB_HOST_PORT $(pidof <pkg name of the app>)

-- on the host --
$NDK_PATH/prebuilt/<system>-<arch>/bin/gdb

-- in gdb --
set arch <your phone's architecture>
target remote <LAN IP Address of your phone>:<port given to gdbserver>
```

##### For future connections
Note: If you uninstall the app then you must reinstall gdbserver as shown above.
```
adb connect <LAN IP Address of your phone>
adb shell

-- in device shell now --
<start the app>
run-as <pkg name of the app> ./gdbserver --once --attach $GDB_HOST_IP:$GDB_HOST_PORT $(pidof <pkg name of the app>)

-- on the host --
$NDK_PATH/prebuilt/<system>-<arch>/bin/gdb

-- in gdb --
set arch <your phone's architecture>
target remote <LAN IP Address of your phone>:<port given to gdbserver>
```

#### Getting the mapping addresses of a file in /proc/\<pid>/maps in a neatly formatted fashion
```
adb shell

-- in device shell --
run-as <pkg name of the app> cat /proc/$(pidof <pkg name of the app>)/maps | grep <file name to search for> | sed -re 's/^([a-f0-9]+)-([a-f0-9]+).*/0x\1, end: 0x\2/g' | nl -w 1 -s ": " | sed -e 's/^/Mapping #/'
```
#### Output
```
Mapping #1: 0x93c62000, end: 0x93cea000
Mapping #2: 0x93ceb000, end: 0x93ced000
Mapping #3: 0x93ced000, end: 0x93cf0000
Mapping #4: 0xb2d52000, end: 0xb2d55000
Mapping #5: 0xb2d55000, end: 0xb2d57000
Mapping #6: 0xb2e3d000, end: 0xb2e3e000
Mapping #7: 0xb3a75000, end: 0xb3a76000
```
