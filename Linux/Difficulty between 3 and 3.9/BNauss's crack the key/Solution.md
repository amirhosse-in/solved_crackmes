# Solution to [BNauss's crack the key](https://crackmes.one/crackme/65ed01ee7b0f7ceced2c5afb)

I used Ghidra to find out what the code does. Here is the `main` function with a few changes:

```c
int main(void)

{
  size_t sVar1;
  char char_array [11];
  size_t read_length;
  undefined8 local_60;
  undefined4 local_58;
  undefined4 local_50;
  undefined4 uStack_4c;
  undefined4 local_48;
  byte byte_array [9];
  size_t flag_length;
  int local_2c;
  char *flag;
  int local_1c;
  FILE *flag_file;
  int local_10;
  int i;
  
  local_10 = 0;
  strcpy(char_array,secret_flag);
  flag_file = fopen(char_array,"r");
  if (flag_file == (FILE *)0x0) {
    perror("Error opening file");
    local_10 = -1;
  }
  else {
    fseek(flag_file,0,2);
    flag_length = ftell(flag_file);
    rewind(flag_file);
    flag = (char *)malloc(flag_length + 1);
    if (flag == (char *)0x0) {
      perror("Error allocating memory");
      fclose(flag_file);
      local_10 = -1;
    }
    else {
      read_length = fread(flag,1,flag_length,flag_file);
      if (read_length == flag_length) {
        fclose(flag_file);
        flag[flag_length] = '\0';
        sVar1 = strlen(flag);
        if (sVar1 == 8) {
          local_50 = 8;
          uStack_4c = 32;
          local_48 = 48;
          local_1c = 0;
          local_2c = 1;
          strcpy((char *)byte_array,flag);
          for (i = 0; i < 8; i = i + 1) {
            local_1c = local_1c + 1;
            fkey[i] = fkey[i] ^ byte_array[i];
            byte_array[i] = byte_array[i] ^ key[i + 1];
            if (byte_array[i] != *(byte *)((long)&skey + (long)i)) {
              local_2c = 0;
              break;
            }
          }
          if (local_2c == 1) {
            local_60 = CONCAT44(uStack_4c,local_50);
            local_58 = local_48;
            giveFlag(local_1c,local_60,local_48);
          }
        }
        free(flag);
        local_10 = 0;
      }
      else {
        perror("Error reading file");
        fclose(flag_file);
        local_10 = -1;
      }
    }
  }
  return local_10;
}
```

I used the code below to perform XOR operations :D

```python
key = [0x00, 0x05, 0x54, 0x10, 0x36, 0xF6, 0xF1, 0x03, 0x17]
skey = [0x76, 0x21, 0x60, 0x53, 0x84, 0x9c, 0x62, 0x79]
byte_array = []

for i in range(8):
    byte_array.append(skey[i] ^ key[i+1])

key = ''.join([chr(x) for x in byte_array])

with open("secret_flag", "w") as f:
    f.write(key)
```