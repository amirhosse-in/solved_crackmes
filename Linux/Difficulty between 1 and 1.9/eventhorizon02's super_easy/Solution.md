# Solution to [eventhorizon02's super_easy](https://crackmes.one/crackme/611e9bfb33c5d45db85dc2d7)

I used Ghidra to decompile the program, and I found this:

```c
int main(int argc, char **argv)
{
  int iVar1;
  
  iVar1 = check_pass(argc, argv[1]);
  if (iVar1 == 0) {
    puts("Access denied, wrong password, please try again.");
  } else {
    puts("Congratulations! You have cracked the code.");
    printf("Flag: {%s}", argv[1]);
  }
  return 0;
}

```c
int main(int argc,char **argv)

{
  int iVar1;
  
  iVar1 = check_pass(argc,argv[1]);
  if (iVar1 == 0) {
    puts("access denied, wrong password, try again");
  }
  else {
    puts("congratulations you have cracked the code");
    printf("flag{%s}",argv[1]);
  }
  return 0;
}

```

Then, I went to the `check_pass` function, and here is the code:

```c

int check_pass(int param_1,char *param_2)

{
  int iVar1;
  size_t sVar2;
  undefined8 uVar3;
  
  if (param_1 == 2) {
    sVar2 = strlen(param_2);
    iVar1 = atoi(param_2);
    if (((int)sVar2 % 3 == 0) && (uVar3 = is_prime(iVar1), (int)uVar3 != 0)) {
      return 1;
    }
  }
  return 0;
}
```

This function takes two parameters, `argc` and `argv[1]`, from the main call. It then checks that `argc` (`param_1`) equals `2` (indicating that the user entered an argument in the terminal) and verifies that the entered argument is a 3k-digit prime number. So, we can use `101` as a valid flag.