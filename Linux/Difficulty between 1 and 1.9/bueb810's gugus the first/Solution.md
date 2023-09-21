
# Solution to [bueb810's gugus the first](https://crackmes.one/crackme/61e9983133c5d413767ca5ac)

I found a call to `strcmp` in the assembly code, so I ran it via `GDB`. Before calling `strcmp`, I used `x/s $eax` to print the pushed values on the stack.

```asm
    122b:   50                      push   %eax
    122c:   8d 45 d5                lea    -0x2b(%ebp),%eax
    122f:   50                      push   %eax
    1230:   e8 fb fd ff ff          call   1030 <strcmp@plt>
```

The first result of `x/s $eax` was the number I entered initially, and the second one was `gu!gu?s`. So, I tried `gu!gu?s`, and it was the key! :)))
