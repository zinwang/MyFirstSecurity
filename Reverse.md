<br />

# PART1_程式編譯組譯與逆向(反編譯)
# c語言程式編譯與組譯
<br >

預處理階段
```
gcc -E test.c -o test.i
```



產生組語

產生AT&T語法格式的組語(gcc預設使用的格式)



gcc -S -masm=att XXXXX.c -o XXXXX_att.s


產生Intel語法格式的組語(微軟預設使用的格式)

gcc -S -masm=intel XXXXX.c -o XXXXX_intel.s

要去掉一堆註解:請加上參數-fno-asynchronous-unwind-tables

gcc -S -masm=intel XXXXX.c -o XXXXX_intel_OK.s -fno-asynchronous-unwind-tables
