# Common supplementary materials for all projects

## Configure Visual Studio Code to use C++20

If you are using Microsoft's C/C++ extension, you need to configure it to use C++20. The easiest way is to find `C_Cpp.default.cppStandard` in settings and change it to `c++20`.

## Debugging with Visual Studio Code

Install Microsoft's extension `C/C++`. Create the directory `.vscode` under the root directory of the project. Add files listed below in `.vscode`:

### `tasks.json`

```json
{
 "version": "2.0.0",
 "tasks": [
  {
   "label": "build debug",
   "type": "shell",
   "group": {
    "kind": "build",
    "isDefault": true
   },
   "linux": {
    "command": "mkdir -p build-debug && cd build-debug && cmake .. -DCMAKE_BUILD_TYPE=Debug && make -j8"
   },
   "presentation": {
    "clear": true
   }
  }
 ]
}
```

`build-debug` is not in the `.gitignore` under the root directory of the project, therefore you need to create `.gitignore` under `build-debug` and write `*` in it, which means that all content in that directory (`build-debug` in this case) will be ignored by git.

### `launch.json`

```json
{
 "version": "0.2.0",
 "configurations": [
  {
   "name": "Debug",
   "type": "cppdbg",
   "request": "launch",
   "linux": {
    // For project 1, program is "test_lsm"
    "program": "${workspaceFolder}/build-debug/test/test_lsm",
    "MIMode": "gdb"
   },
   "args": [],
   "stopAtEntry": false,
   "cwd": "${workspaceRoot}",
   "environment": [],
   "externalConsole": false,
   "preLaunchTask": "build debug",
   "setupCommands": [
    {
     "text": "-enable-pretty-printing",
     "description": "Enable pretty-printing for gdb",
     "ignoreFailures": true
    }
   ]
  },
 ]
}
```

`args` is the argument list. If you want to run a specific test, you can modify it like this: `"args": ["--gtest_filter=LSMTest.BlockTest"],`

Then press `F5` to start debugging.

## Address Sanitizer

If you encounter memory errors such as `segmentation fault` and `bad alloc`, you may try [Address Sanitizer](https://github.com/google/sanitizers/wiki/AddressSanitizer) (Asan) to help yourself to locate the cause of the error. Asan can also detect some other memory problems such as memory leaks. You may enable Asan by CMake options `-DCMAKE_BUILD_TYPE=debug -DASAN=on`. Note that Asan only takes effect in debug mode. Programs with Asan enabled are slower, therefore you may need to increase the timeout value in tests temporarily.

## Hints

* If you use our public server, you should `ssh-keygen` on our server to generate a key pair and upload the public key to GitHub, so that you can access GitHub using git protocal like `git@github.com:xxx/xxxx.git`

* We have already installed gtest and pre-commit on our public server.

* Submission after the due date will consume late days. If you already get full grades, don't submit after the due date, otherwise you will waste your late days.

* `make: no rule to make target 'submit'` If you encounter this error when `make submit`, a possible reason is that you cloned the repo before I implemented `make submit` in CMakeLists.txt. `git fetch public && git merge public/main && cmake ..` can fix the issue in this case.
