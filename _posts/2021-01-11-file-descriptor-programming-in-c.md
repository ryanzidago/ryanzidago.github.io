Today I learned that a file descriptor is basically an integer representing a file. According to the Wikipedia page on file descriptors:
> In Unix and related computer operating systems, a file descriptor [...] is an abstract indicator (handle) used to access a file or other input/output resource, such as a pipe or network socket. [...] A file descriptor is a non-negative integer, generally represented in the C programming language as the type int (negative values being reserved to indicate "no value" or an error condition).

After watching this [conference](https://youtu.be/Ftg8fjY_YWU?t=311) on file descriptors, unix sockets and POSIX, I learned that some file descriptors are reserved for specific purposes:
- 0 is for standard input
- 1 is for standard output
- 2 is for standard error output

Which is nice, because I was reading the [CS 241 coursebook](https://github.com/illinois-cs241/coursebook) where the reader is tasked with writing a program that uses `write()` to print out `"Hi! My name is <name>"` (like a Hello, World! but in a system call style). I started the program by writing it like so:
```c
#include <unistd.h>
#include <string.h>

int main(int argc, char const *argv[]) {
  int stdout_file_descriptor = 1;
  char greeting[] = "Hi! My name is ";

  strcat(greeting, argv[1]);
  write(stdout_file_descriptor,  greeting, strlen(greeting));
}
```

The [unistd.h](https://pubs.opengroup.org/onlinepubs/9699919799/basedefs/unistd.h.html) header defines the `write()` function that we need to use to make a system call. The [string.h](https://pubs.opengroup.org/onlinepubs/9699919799/basedefs/string.h.html) has the `strcat()` function that we use to concatenate two strings.

However, the program above resulted in a `segmentation fault` error every time I executed it without any arguments:

```
./hello_world.c
[1]    391226 segmentation fault (core dumped)  ./hello_world.c
```

Read this [StackOverflow answer](https://stackoverflow.com/a/19641654/11012584) for more information. Basically, this happen because I tried to access memory that I do not have access to. If I run the program without any arguments, then the `argv[1]` does to contain any value. To prevent this error from happening, I can just double check that at least two arguments are provided:
```c
#include <unistd.h>
#include <string.h>

int main(int argc, char const *argv[]) {
  int stdout_file_descriptor = 1;
  char greeting[] = "Hi! My name is ";
  char error_msg[] = "You need to supply one argument!\n";

  if (argc == 2) {
    strcat(greeting, argv[1]);
    write(stdout_file_descriptor,  greeting, strlen(greeting));
  } else {
    write(stdout_file_descriptor, error_msg, strlen(error_msg));
  }
}
```

Now running it without any arguments print out a nice error message to the terminal:
```c
./hello_world.c
You need to supply one argument!
```

Adding a name will properly display it to stdin:
```c
Hi! My name is Ryan%
```

This [blog post](https://hacksland.net/c-programming-file-descriptors) was very helpful in understand the basics of file descriptor programming in C.
