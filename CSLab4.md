# Program 4: Buffer Overflow Illustration Using GCC

## Step 1: Open the Linux Terminal

Start your Kali Linux or Ubuntu virtual machine and open Terminal.

## Step 2: Create the C Program

Create a file named `buffer.c`:

``` bash
nano buffer.c
```

Enter the program exactly as given in the lab manual:

``` c
#include <stdio.h>
#include <string.h>

int main()
{
   char buff[15];
   int pass = 0;

  printf("\nEnter the password:\n");

  fgets(buff, sizeof(buff), stdin);
buff[strcspn(buff, "\n")] = '\0'; // Remove newline

  if(strcmp(buff, "thegeekstuff"))
  {
     printf("\nWrong Password\n");
  }
  else
  {
     printf("\nCorrect Password\n");
     pass = 1;
  }

  if(pass)
  {
      printf("\nRoot privileges given to the user\n");
  }

  return 0;

}
```

Save the file: 1. Press **Ctrl + O**. 2. Press **Enter**. 3. Press
**Ctrl + X**.

## Step 3: Compile the Program

Use the compilation command from the lab manual:

``` bash
gcc -g -fno-stack-protector -z execstack -o buffer buffer.c
```

-   `-g` --- Includes debugging symbols.
-   `-fno-stack-protector` --- Disables stack protection.
-   `-z execstack` --- Makes the stack executable for the demonstration.

## Step 4: Run the Program

``` bash
./buffer
```

### Run 1: Correct Password

Enter:

``` text
thegeekstuff
```

Expected output:

``` text
Enter the password:

thegeekstuff

Correct Password

Root privileges given to the user
```

### Run 2: Incorrect Password

Run the program again:

``` bash
./buffer
```

Enter:

``` text
hello
```

Expected output:

``` text
Wrong Password
```

No root privileges are granted.

### Run 3: Attempt Buffer Overflow

Run the program again:

``` bash
./buffer
```

Enter a long string, for example:

``` text
Hhhhhhhhhhhhhhhhhhhhhhhhhhhhhhhh
```

Depending on your compiler and system, one of the following may happen:

-   Program crashes (Segmentation Fault)
-   Unexpected output
-   Variable values change

The program may behave abnormally.

## Conclusion

This experiment illustrates password checking and the concept of a
buffer overflow. The result of entering a long string depends on the
compiler and system.
