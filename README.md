# smali-trix
Some shell one-liners for manipulating/collecting data from smali class files.

#### Extracting all the types
```
grep -hr '^\.class.*;' <path to source root>/smali | sed -n "s/^\.class.*\s\(L.*;\)/\1/p" > types.txt
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
