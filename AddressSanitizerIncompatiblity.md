TODO: this page is incomplete

# malloc

[AddressSanitizer](AddressSanitizer) uses its own memory allocator (`malloc`, `free`, etc).
If your code depends on a particular feature or extension of glibc malloc, tcmalloc or some other malloc, it may not work as you expect.

# Virtual memory
[AddressSanitizer](AddressSanitizer) uses a lot of virtual address space (20T on x86\_64 Linux)
