# Solution to [pourmeadrinkwhileimfloating's licesnse_checker_3](https://crackmes.one/crackme/62327b0433c5d46c8bcc0335)

I opened the program in `Ghidra` and performed some code cleanup with the code generated in `Ghidra`. It was the result:

```c
int main(int count,char **args){
  int number;
  size_t len;
  long in_FS_OFFSET;
  char chr;
  int sum;
  int i;
  undefined8 local_10;
  
  local_10 = *(undefined8 *)(in_FS_OFFSET + 0x28);
  if (count != 2) {
    printf("Usage : %s <license pass code here [numbers only]>\n",*args);
                    /* WARNING: Subroutine does not return */
    exit(0);
  }
  sum = 0;
  i = 0;
  while( true ) {
    len = strlen(args[1]);
    if ((int)len <= i) break;
    chr = args[1][i];
    number = atoi(&chr);
    sum = sum + number;
    i = i + 1;
  }
  if (sum == 50) {
    puts("Premium access has been activated !");
                    /* WARNING: Subroutine does not return */
    exit(0);
  }
  puts("Wrong license code");
                    /* WARNING: Subroutine does not return */
  exit(0);
}
```

So you should enter a number whose digits' sum is equal to `50`.