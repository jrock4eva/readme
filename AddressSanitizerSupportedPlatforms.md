


# Platforms
## x86/x86\_64 Linux
Just works, this is the primary platforms for the developers.

Stack unwinding: frame pointers.
Default shadow mapping:
```
Shadow = (Mem >> 3) | 0x20000000         # 32 bit
Shadow = (Mem >> 3) | 0x0000100000000000 # 64 bit
```
Alternative shadow mapping, requires -pie
```
Shadow = Mem >> 3         # 32 and 64 bit
```
Function interception: `dlsym`


## x86/x86\_64 Darwin (OSX)
Just works, but the machinery used to intercept functions (mach\_override) may soon be replaced with another one (function interposition)

Stack unwinding: frame pointers.
Shadow mapping:
```
Shadow = (Mem >> 3) | 0x20000000         # 32 bit
Shadow = (Mem >> 3) | 0x0000100000000000 # 64 bit
```


## ARM Android
Works (TODO(eugenis))

Stack unwinding mechanism: `_Unwind_Backtrace`.

## ARM iOS
Doing baby steps, help is welcome.

## x86 Windows
C-only code mostly works,<br>
Simple C++ code also works, complex C++ code using multiple/virtual inheritance doesn't work.<br>
See issue 56 (on Google Code) for the details.<br>
Help is welcome!<br>
<br>
Stack unwinding mechanism: <code>CaptureStackBackTrace</code>

<h2>PowerPC32/PowerPC64 Linux</h2>
We've been notified that small tests work.<br>
TODO: describe the differences (e.g. the shadow mapping, offset, page size, etc)<br>
<br>
Stack unwinding mechanism: <code>_Unwind_Backtrace</code>.<br>
<br>
<h2>SPARC Linux</h2>
The run-time library is known to build on SPARC Linux.<br>
<br>
<h1>Compilers</h1>
<h2>Clang/LLVM</h2>
Just works, this is the primary compiler for the developers.<br>
<br>
<h2>GCC</h2>
The first version is in trunk, will appear in 4.8 release.<br>
The implementation is (almost?) complete, but we don't have enough experience with it yet.<br>
<br>
In <a href='AddressSanitizer'>AddressSanitizer</a> mode GCC defines <code>__SANITIZE_ADDRESS__</code> macro, but does not support <code>__has_feature(address_sanitizer)</code>