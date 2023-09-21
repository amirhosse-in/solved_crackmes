# Solution to [b3nd3p's my_f1rs7_cr4ckm3](https://crackmes.one/crackme/6296c1ff33c5d45b75903c0f)

I used GDB to execute the program instruction by instruction. Before calling the `check` function, there were two `mov` instructions.

```asm
    1324:	89 de                	mov    %ebx,%esi
    1326:	89 c7                	mov    %eax,%edi
    1328:	e8 48 ff ff ff       	call   1275 <check>
```

I printed the values of `$ebx` and `$eax`:

```
(gdb) print $eax
$1 = 12
(gdb) print $ebx
$2 = 1337
```

`12` was my number. I ran the program and entered `1337` as the password, it worked! :))
