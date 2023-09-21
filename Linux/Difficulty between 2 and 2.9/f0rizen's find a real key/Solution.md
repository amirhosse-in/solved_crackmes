# Solution to [f0rizen's find a real key](https://crackmes.one/crackme/629e1e5833c5d4251e72375f)

First of all, I tried the `strings` command to check all the strings in the file, and I found `sup3r_s3cr3t_k3y_1337`, but it doesn't work. Then, I used Ghidra to disassemble the program and performed some code cleanup, which led to the following result:

```c
int main(int argc,char **argv)

{
  int iVar1;
  size_t sVar2;
  long in_FS_OFFSET;
  int j;
  int i;
  int arr [24];
  byte shifted_string [24];
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  if (argc == 1) {
    puts("Usage: ./crackme FLAG");
    iVar1 = 1;
  }
  else {
    sVar2 = strlen(argv[1]);
    if (sVar2 == 21) {
      for (j = 0; j < 21; j = j + 1) {
        shifted_string[j] = "sup3r_s3cr3t_k3y_1337"[j] - 0x22;
      }
      arr[0] = 55;
      arr[1] = 63;
      arr[2] = 47;
      arr[3] = 118;
      arr[4] = 43;
      arr[5] = 98;
      arr[6] = 40;
      arr[7] = 33;
      arr[8] = 52;
      arr[9] = 15;
      arr[10] = 119;
      arr[11] = 98;
      arr[12] = 72;
      arr[13] = 39;
      arr[14] = 117;
      arr[15] = 8;
      arr[16] = 86;
      arr[17] = 106;
      arr[18] = 104;
      arr[19] = 78;
      arr[20] = 104;
      for (i = 0; i < 21; i = i + 1) {
        if ((int)(char)(shifted_string[i] ^ argv[1][i]) != arr[i]) {
          puts("Wrong flag");
          iVar1 = 1;
          goto END_OF_PROGRAM;
        }
      }
      printf("You found a flag! %s\n",argv[1]);
      iVar1 = 0;
    }
    else {
      puts("Wrong flag");
      iVar1 = 1;
    }
  }
END_OF_PROGRAM:
  if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return iVar1;
}
```

Then I wrote this code to generate a flag, and the generated flag was very beautiful :)

```c
#include <stdio.h>

int find_char(int diff, int a){
    for(int i = 21; i < 256; i++){
        if((i ^ a) == diff){
            printf("%d ^ %d = %d\n", i, a, diff);
            return i;
        }
    }
    printf("FAILED!");
    return -1;
}

int main(){
    int diffs[] = {55, 63, 47, 118, 43, 98, 40, 33, 52, 15, 119, 98, 72, 39, 117, 8, 86, 106, 104, 78, 104}; 

    int shifted_string[21];
    for(int i = 0; i < 21; i++){
        shifted_string[i] = "sup3r_s3cr3t_k3y_1337"[i] - 34;
    }

    int result[21];
    for(int i = 0; i < 21; i++){
        result[i] = find_char(diffs[i], shifted_string[i]);
    }
        
    printf("\"");
    // Print as char
    for(int i = 0; i < 21; i++){
        printf("%c", result[i]);
    }
    printf("\"\n");
}
```

The generated flag was `flag{_y0u_f0und_key_}`.