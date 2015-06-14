# FastCode
Fast replacements for Embarcadero's standard libs for Delphi XE7 and above.  
Note that this is still early alpha code and needs further optimization in places.
A comprehensive testsuite is available in the Test directory.  

# FastDefaults  
Speeds up comparisons by using static class functions instead of interfaces.  
The functions are inline and inject only the short snippet of code need to compare the types in use.  

It does this by resolving the type at compile-time using the new `GetTypeKind` compiler intrinsic in XE7.  
Then it inlines just the code snippet needed.  
It also uses much more optimized code than the RTL; this is especially true for Win64 which has not been blessed by the 2007 FastCode project.  

#QuickerSort  
Adds class helper for `TArray<T>` that uses a more optimized version of quicksort.  



#Examples

**FastDefault**

    var
      I1, I2: integer;
      RFast: integer;
    begin
      I1:= 1;
      I2:= 2;
      //no overhead for one shot use.
      RFast:= FastDefaults.TComparer<integer>.Default.Compare(i1,i2);
      if RFast = 0 then Readln;
    end.


**CompareFast: even faster.**
If you don't need a generic comparer, you can use the `CompareFast` versions which will work much faster, because they work directly with register operands and are not forced to go via the stack.  

```
    var
      I1, I2: integer;
      RFast: integer;
    begin
      I1:= 1;
      I2:= 2;
      RFast:= CompareFast(i1,i2);
      ...
```

Generated code 
```
Project44.dpr.13: I1:= 1;
00433B31 B801000000       mov eax,$00000001
Project44.dpr.14: I2:= 2;
00433B36 BA02000000       mov edx,$00000002
Project44.dpr.15: Result:= CompareFast(i1,i2);
00433B3B 8BD8             mov ebx,eax
00433B3D 2BDA             sub ebx,edx
...
```

**Default Default**
```
var
  I1, I2: integer;
  RSlow: integer;
begin
  I1:= 1;
  I2:= 2;
  RSlow:= System.Generics.Defaults.TComparer<integer>.Default.Compare(i1,i2);
  if RSlow = 0 then Readln;
end.
```

