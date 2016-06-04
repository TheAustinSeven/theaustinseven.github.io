---
layout: post
title: (Mis)Adventures With Rust On The GPU
date:   2016-6-03 20:42:07 -0700
category: Projects
tags: [Rust, Projects, OpenCL, GPU]
---
One of my projects this Summer is adding easy GPU support for the Rust Programming Language. I've been figuring out different possibilities, and the most promising of them all was a project put out by the Khronos Group called the [LLVM/SPIR-V Bi-Directional Translator](https://github.com/KhronosGroup/SPIRV-LLVM).

To provide some background, SPIR-V is a language that the Khronos Group developed that should compile and run on the graphics card with OpenCL. Since Rust is an LLVM language, you should be able to compile rust to LLVM bitcode, and then compile that code into SPIR-V. Then once you have SPIR-V, you can run that on the GPU with the `clCreateProgramWithBinary` method from OpenCL.

So began my week long battle with the LLVM compiler infrastructure. My first attempt to build the LLVM/SPIR-V Bi-Directional Translator, I assumed that I would need the most recent version of LLVM. Since the translator is built as a tool in a fork from LLVM 3.6.1 and the most recent stable version of LLVM was 3.8.1, I figured I would need to update the version. Since the Khronos Group had added some code all over LLVM. I started by trying to combine the two with a `git rebase`. The rebase ran for 14401 commits! When all was said and done. I started the build and it made it to about 30% complete when it started to complain.

After about a day of fighting with the compiler, I decided to try dragging and dropping relevant files from the translator directory into LLVM. This method made it about as far before it also decided it wasn't going to be able to finish. I also fought with this version before finally deciding to build directly from the translator repo. It took several hours, and required some quick fixes, but finally I actually had an instance of the program. I quickly ran the program on my compiled hello world program. This is when things really went wrong.

```
llvm-spirv: /vagrant/llvm/lib/SPIRV/SPIRVWriter.cpp:1084: bool SPIRV::LLVMToSPIRV::transBuiltinSet(): Assertion `(Kind == SourceLanguageOpenCL_C || Kind == SourceLanguageOpenCL_CPP ) && "not supported"' failed.
Stack dump:
0.      Program arguments: ./llvm-spirv /vagrant/test.bc
1.      Running pass 'Unnamed pass: implement Pass::getPassName()' on module '/vagrant/test.bc'.
0  llvm-spirv      0x00000000007592c2
1  llvm-spirv      0x000000000075955d
2  llvm-spirv      0x00000000007581c7
3  libpthread.so.0 0x00007fe67db20340
4  libc.so.6       0x00007fe67cf56cc9 gsignal + 57
5  libc.so.6       0x00007fe67cf5a0d8 abort + 328
6  libc.so.6       0x00007fe67cf4fb86
7  libc.so.6       0x00007fe67cf4fc32
8  llvm-spirv      0x0000000000553a09
9  llvm-spirv      0x0000000000554f7e
10 llvm-spirv      0x000000000054d5e1
11 llvm-spirv      0x00000000006a6ce4
12 llvm-spirv      0x00000000006a738a
13 llvm-spirv      0x00000000006a75a9
14 llvm-spirv      0x000000000055738b
15 llvm-spirv      0x0000000000406ddb
16 llvm-spirv      0x0000000000407b86
17 libc.so.6       0x00007fe67cf41ec5 __libc_start_main + 245
18 llvm-spirv      0x0000000000405ff9
Aborted (core dumped)
```

So apparently the SPIR-V compiler takes LLVM as long as that LLVM is compiled as OpenCL C or C++. This was pretty disappointing as that makes it less of an LLVM to SPIR-V translator and more of an OpenCL C to SPIR-V translator.

So that was disappointing. However, I am not done yet. My next step is to work out an efficient pathway using the PTX and AMDGPU targets that already exist in LLVM. I will try to post updates in a week or two.
