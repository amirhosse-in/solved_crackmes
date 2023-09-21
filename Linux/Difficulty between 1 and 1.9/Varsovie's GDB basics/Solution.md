# Solution to [Varsovie's GDB basics](https://crackmes.one/crackme/645d3d4e33c5d43938913079)

After disassembling the program using Ghidra, I performed some code cleanup, and here is the result:

```c
int main(void)

{
    int input;
    int i;
    int c;

    c = 2;
    for(i = 2; i < 20; i = i + 1){
        c = c * i;
    }
    printf("Enter a number : ");
    __isoc99_scanf(&DAT_0010201a,&input);
    if(c == input) {
        puts("There is the flag : I_LOVE_YOU");
    }
    else {
        puts("I cannot provide you a flag !");
    }
    return 0;
}
```

so the answer is `2 * 19!`