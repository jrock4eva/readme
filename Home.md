Welcome!

This repository is an umbrella for the three repositories previously hosted on code.google.com:
* http://code.google.com/p/address-sanitizer/
* http://code.google.com/p/thread-sanitizer/
* http://code.google.com/p/memory-sanitizer/

Since those projects started, most of the code has been moved to the LLVM Compiler Infrastructure (http://llvm.org). Here reside some pieces of infrastructure and documentation.

Below are the links to the documentation for each project.

### AddressSanitizer
AddressSanitizer (ASan) is a fast memory error detector. 
It finds use-after-free and {heap,stack,global}-buffer overflow bugs in C/C++ programs. 
Learn more:

* [AddressSanitizer](AddressSanitizer) -- how to use the tool.
* [LeakSanitizer](AddressSanitizerLeakSanitizer) -- leak detector
* [AddressSanitizer page at clang.llvm.org](http://clang.llvm.org/docs/AddressSanitizer.html)
* [AddressSanitizerAlgorithm](AddressSanitizerAlgorithm) -- how it works.
* [AddressSanitizerHowToContribute](AddressSanitizerHowToContribute) -- if you want to help.
* [Read if you are working on Chromium](https://sites.google.com/a/chromium.org/dev/developers/testing/addresssanitizer)
* Watch the presentation from the [LLVM Developer's meeting](http://llvm.org/devmtg/2011-11/) (Nov 18, 2011): [Video](http://www.youtube.com/watch?v=CPnRS1nv3_s), [slides](http://llvm.org/devmtg/2011-11/Serebryany_FindingRacesMemoryErrors.pdf).
* [Our paper accepted to USENIX ATC 2012](http://research.google.com/pubs/pub37752.html)
  * See also: our [slides for USENIX ATC 2012](https://docs.google.com/presentation/d/19OSgb1N9Ezef39Blb-5lkzycq7-tMtAvy825FofyrmY/edit#slide=id.p14)

Your comments are welcome at address-sanitizer@googlegroups.com or [in Google+](https://plus.google.com/117014197169958493500)

### ThreadSanitizer
ThreadSanitizer is a fast data race detector for C/C++ and Go.

Check out:

 * [C/C++ User Manual](ThreadSanitizerCppManual)
 * [Go User Manual](ThreadSanitizerGoManual)
 * [High-level algorithm overview](ThreadSanitizerAlgorithm)
 * [Instructions for developers](ThreadSanitizerDevelopment)

Send comments/questions to thread-sanitizer@googlegroups.com

### MemorySanitizer

A fast LLVM-based tool that detects the use of uninitialized memory.

 * [MemorySanitizer](MemorySanitizer) - more info
 * LLVM Developer's meeting (April 29-30, 2013): [slides](http://llvm.org/devmtg/2013-04/stepanov-slides.pdf), [video](http://llvm.org/devmtg/2013-04/videos/stepanov-hires.mov)