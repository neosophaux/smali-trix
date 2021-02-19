# smali-trix
Some shell one-liners for manipulating/collecting data from smali class files.

#### Extracting all the types
```
grep -hr '^\.class.*;' <path to source root>/smali | sed -n "s/^\.class.*\s\(L.*;\)/\1/p" > types.txt
```
##### Output: types.txt
```
Landroid/arch/a/b/b$e;
Landroid/arch/lifecycle/LiveData$a;
Landroid/support/c/a/h;
Landroid/support/v4/a/f;
```
