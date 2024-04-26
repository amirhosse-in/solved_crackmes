# Solution to [yariza's Super Random Guessing | Yariza Crackme 3](https://crackmes.one/crackme/60da626933c5d410b88430b0)

After decoding the program using Ghidra, I made some modifications to the variable names to improve readability. The code generates a random password using the `rand()` function, seeded with the current time using `time(NULL)`. By adding a random number between 0 and 25 to the address of `chars_1`, which is located below `chars_2` (and `chars_3` and `chars_4`) in the stack, the code accesses a character whose hexadecimal value falls within the range of `&chars_1` to `&chars_4+7`. We can create a straightforward script that adheres to the algorithm for generating a password with the same seed (`time(NULL)`). Afterwards, we can execute both the script and the program simultaneously using `./out & ./random_guessing_game`.


Decoded binary:
```c
undefined8 main(void)

{
  int random_value;
  time_t tVar1;
  undefined8 chars_1;
  undefined8 chars_2;
  undefined8 chars_3;
  undefined8 chars_4;
  undefined8 local_68;
  undefined4 local_60;
  undefined2 local_5c;
  char char_array [61];
  char user_input [15];
  int i;
  
  chars_1 = 0x6867666564636261;
  chars_2 = 0x706f6e6d6c6b6a69;
  chars_3 = 0x313060232a402d2f;
  chars_4 = 0x3938373635343332;
  local_68 = 0;
  local_60 = 0;
  local_5c = 0;
  tVar1 = time((time_t *)0x0);
  srand((uint)tVar1);
  for (i = 0; i < 15; i = i + 1) {
    random_value = rand();
    char_array[i] = *(char *)((long)&huge_1 + (long)(random_value % 26));
  }
  printf("\n\x1b[36m[*] Password: ");
  fgets(user_input,15,stdin);
  puts("\n[*] Checking your password..");
  random_value = strncmp(user_input,char_array,4);
  if (random_value == 0) {
    puts("\n\x1b[32m[+] Good Job!");
  }
  else {
    printf("\n\x1b[31m[-] Maybe next time, password was: %s\n",char_array);
  }
  return 0;
}
```

Script:
```c
#include<stdio.h>
#include<time.h>
#include <stdlib.h> 

int main(){

    long chars_1 = 0x6867666564636261;
    long chars_2 = 0x706f6e6d6c6b6a69;
    long chars_3 = 0x313060232a402d2f;
    long chars_4 = 0x3938373635343332;
    char password[15];
    srand((uint)time((time_t *)0x0));
    for (int i = 0; i < 15; i = i + 1) {
        int random_value = rand();
        password[i] = *(char *)((long)&chars_1 + (long)(random_value % 26));
    }
    printf("Password: %s\n", password);
}
```