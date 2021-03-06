# Clang 5.0 vs GCC 7.1

This page describes differences in AddressSanitizer between Clang and GCC (both runtime and compiler parts). In general, most differences are caused by different compiler part implementations in Clang and GCC.

The library part is periodically merged from LLVM to GCC and doesn't contain serious incompatibilities (although GCC still needs some local workarounds, see below).

The comparison was performed for relatively fresh versions of mentioned tools:

Clang: clang version 5.0 r304761 (http://llvm.org/viewvc/llvm-project/cfe?view=revision&revision=304761)<br>
LLVM:  LLVM  version 5.0 r304775 (http://llvm.org/viewvc/llvm-project?view=revision&revision=304775)<br>
Compiler-rt: r304744               (http://llvm.org/viewvc/llvm-project/compiler-rt?view=revision&revision=304744)<br>

GCC: gcc version 7.1.1 r247506 (https://gcc.gnu.org/viewcvs/gcc?view=revision&revision=247506)

#### Feature:
std containers overflow detection
* LLVM: yes (std::vector, std::string is under review)
* GCC: google branch (std::vector), but available in GCC 8
* Bugs/ML: <http://llvm.org/viewvc/llvm-project?view=revision&revision=208319> <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<https://gcc.gnu.org/viewcvs/gcc?view=revision&revision=207517>

##### Cons and Pros:
Support new kinds of bugs detection.

<br>

#### Feature:
dynamic alloca overflow detection
* LLVM: yes
* GCC: compiler support missing, but available in GCC 8
* Bugs/ML: 

##### Cons and Pros:
Support new kinds of bugs detection.

<br>

#### Feature:
using private aliases for globals
* LLVM: optional, disabled by default, mixing instrumented code with non-instrumented is not safe in default mode.
* GCC: yes, mixing instrumented code with non-instrumented is safe.
* Bugs/ML: https://gcc.gnu.org/bugzilla/show_bug.cgi?id=63888 <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;https://gcc.gnu.org/bugzilla/show_bug.cgi?id=68016 <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;https://github.com/google/sanitizers/issues/398 <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;https://llvm.org/bugs/show_bug.cgi?id=24486 <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;http://reviews.llvm.org/D15642 <br>

##### Cons and Pros:
GCC flaws:
- false negatives on global variables when copy relocations are involved.

LLVM flaws:
- we need to re-link our binaries against each time we add/remove sanitized library.
- false positives or internal ASan CHECK failures can occur when mixing instrumented code with non-instrumented.
- mixing instrumented code with non-instrumented is not safe in default mode.

<br>

#### Feature:
ODR violation detection
* LLVM: yes, poisoning based and ODR indicator symbols based
* GCC: yes, ODR indicator symbols based
* Bugs/ML: https://gcc.gnu.org/bugzilla/show_bug.cgi?id=63888 <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;https://gcc.gnu.org/viewcvs/gcc?view=revision&revision=243153 <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;https://gcc.gnu.org/viewcvs/gcc?view=revision&revision=241981 <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;http://reviews.llvm.org/D15642 <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;http://reviews.llvm.org/D15644 <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;https://github.com/google/sanitizers/issues/398 <br>
##### Cons and Pros:
GCC flaws:
- GCC needs ODR indicators to make ODR violation detection work.

LLVM flaws:
- false positives or internal ASan CHECK failures can occur when mixing instrumented code with non-instrumented.

<br>

#### Feature:
symbol size changing for global variables
* LLVM: yes
* GCC: no
* Bugs/ML: https://github.com/google/sanitizers/issues/619 <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;https://gcc.gnu.org/bugzilla/show_bug.cgi?id=68016 <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;https://sourceware.org/bugzilla/show_bug.cgi?id=19173

##### Cons and Pros:
In Clang, ASan changes size of global variables by appending redzone size to it. This is an ABI change and may cause runtime errors when/if other shared modules have been linked against non-sanitized version of the library. However, given that Clang should support not only ELF format (e.g. Windows and OS/X use different ones), it's complicated to make sure that target linker won't cut out right redzone for global variables if not include it in symbol itself. GCC ASan generally supports ELF only, so it doesn't have such problems.

<br>

#### Feature:
adaptive global redzone sizes
* LLVM: yes
* GCC: no
* Bugs/ML: 

##### Cons and Pros:
Possible better layout for global variables in Clang.

<br>

#### Feature:
KASan support
* LLVM: limited
* GCC: yes
* Bugs/ML: <https://www.kernel.org/doc/Documentation/kasan.txt>

##### Cons and Pros:

Use Asan for kernel. Linux kernel still cannot be built via clang for most targets (for x86{,_64} you still need apply special patches).

<br>

#### Feature:
asan_experiments support
* LLVM: yes (experimental)
* GCC: no
* Bugs/ML: http://llvm.org/viewvc/llvm-project?view=revision&revision=232501 <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;http://llvm.org/viewvc/llvm-project?view=revision&revision=232502

##### Cons and Pros:

This feature is experimental. The experiments can be used to evaluate potential optimizations that remove instrumentation (assess false negatives). This way we can figure out, what optimization causes false negatives in much easier way.

<br>

#### Feature:
invalid pointer pairs detection
* LLVM: yes (experimental, heap only)
* GCC: no, but will be available in GCC 8.
* Bugs/ML:https://gcc.gnu.org/viewcvs/gcc?view=revision&revision=255404

##### Cons and Pros:

This feature is experimental. Instrument <, <=, >, >=, - with pointer operands. Used to enable detection of invalid comparison operators on pointers.

<br>

#### Feature:
default runtime library linkage
* LLVM: static
* GCC: dynamic
* Bugs/ML: https://github.com/google/sanitizers/wiki/AddressSanitizerAsDso <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;https://github.com/google/sanitizers/issues/147 <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;http://reviews.llvm.org/D3042

##### Cons and Pros:
Dynamic linkage cons:
- Worse performance (ASAN run-time is called via PLT even in the main executable)
- Issue 147 (on Google Code) (can't use -static-libstdc++-Harder deployment (need to carry the DSO around)
- __asan_init is not called from preinit_array and so there is a risk that an instrumented code will get called before __asan_init (may cause SEGV at startup; still unlikely)

Dynamic linkage proc:
- Smaller disk usage and memory footprint when multiple processes are running with ASan.
- Potential ability to bring old ASAN-ified binaries to new systems
- Ability to LD_PRELOAD ASAN-DSO

<br>

#### Feature:
use explicit list of exported symbols
* LLVM: yes
* GCC: no
* Bugs/ML: https://gcc.gnu.org/bugzilla/show_bug.cgi?id=64234 

##### Cons and Pros:

In GCC, statically sanitized executables don't export ASan symbols. In particular, if executable is linked with -static-libasan, it won't export libasan public API (__asan_reportXYZ, etc.), causing dlopens of sanitized shared libraries to fail. Clang uses --dynamic-list link option to export public symbols and avoid such errors.

<br>

#### Feature:
asan_symbolize script
* LLVM: yes
* GCC: no
* Bugs/ML:

##### Cons and Pros:

The script itself is a useful feature, perhaps it should be embedded to GCC source tree as well.

<br>

#### Feature:
ARM ABI for frame pointer extracting
* LLVM: LLVM ABI
* GCC: GCC ABI
* Bugs/ML: https://gcc.gnu.org/bugzilla/show_bug.cgi?id=61771

##### Cons and Pros:

GCC needs a special patch to handle the issue.

<br>

#### Feature:
support ASan blacklist file
* LLVM: yes
* GCC: no
* Bugs/ML: <http://clang.llvm.org/docs/AddressSanitizer.html#suppressing-errors-in-recompiled-code-blacklist><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<https://gcc.gnu.org/ml/gcc-patches/2013-12/msg00649.html>

##### Cons and Pros:

Clang is more flexible in controlling instrumentation.

<br>

#### Feature:
support sanitizer coverage
* LLVM: yes
* GCC: very limited, for Linux kernel only
* Bugs/ML: http://clang.llvm.org/docs/SanitizerCoverage.html <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
https://gcc.gnu.org/ml/gcc-patches/2015-12/msg00299.html

##### Cons and Pros:

In Clang, ASan has good integration with fast and lightweight fuzzing library Libfuzzer (<http://llvm.org/docs/LibFuzzer.html>). In GCC, ASan is integrated with AFL only (http://lcamtuf.coredump.cx/afl/).

<br>

#### Feature:
support old Linux kernels (< 3.0)
* LLVM: no
* GCC: yes
* Bugs/ML:

##### Cons and Pros:

In GCC, ASan works for older Linux kernels. GCC uses local workarounds in libsanitizer to support this feature.

<br>

#### Feature:
symbolizer implementation
* LLVM: LLVM symbolizer
* GCC: Libbacktrace
* Bugs/ML:

##### Cons and Pros:

GCC uses embedded libbacktrace library for symbolization that's statically linked with libasan. LLVM needs a separate llvm-symbolizer binary to print nice reports.

#### Feature:
Support for `no_sanitize` attribute
* LLVM: yes
* GCC: no, but would become available in GCC 8
* Bugs/ML: <https://gcc.gnu.org/bugzilla/show_bug.cgi?id=78204> <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;https://gcc.gnu.org/viewcvs/gcc?view=revision&revision=249158 <br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;https://clang.llvm.org/docs/AttributeReference.html#no-sanitize-clang-no-sanitize <br>

##### Cons and Pros:
In LLVM it's possible to disable custom UBSan checker (e.g. `float-divide-by-zero`). GCC 8+ will support such functionality as well.

<br>

#### Feature:
Instrument function call arguments whose address is taken
* LLVM: yes, but only for small types
* GCC: no, but would become available in GCC 8
* Bugs/ML: <https://gcc.gnu.org/bugzilla/show_bug.cgi?id=81040><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<https://github.com/google/sanitizers/issues/823>

##### Cons and Pros:
LLVM can find more bugs in function arguments

<br>

#### Feature:
Support for dead stripping of globals on Linux
* LLVM: yes
* GCC: no
* Bugs/ML: <https://reviews.llvm.org/rL301587><br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<https://reviews.llvm.org/rL301588>

##### Cons and Pros:
Allows linker to perform dead stripping of globals and their ASan metadata.