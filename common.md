# Common supplementary materials for all projects

## Visual Studio Code调试

安装Microsoft的`C/C++`。在项目根目录中新建目录`.vscode`。在其中添加如下文件：

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

由于`build-debug`没有在项目根目录下的`.gitignore`里，所以需要在`build-debug`下面新建一个`.gitignore`，其内容设置为`*`，这样这个目录就会被git忽略，而且不会污染根目录下的`.gitignore`。

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
    // 具体是哪个program根据project决定
    "program": "${workspaceFolder}/build-debug/test/test_btree",
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

其中`args`后面是命令行参数列表。比如如果想要运行某个特定的测试，可以这样：`"args": ["--gtest_filter=BPlusTreeTest.RandInsertGetDelete1e6"],`

然后按`F5`即可开始调试。

## Hints

* 如果使用我们提供的服务器进行开发的话，需要在服务器上用`ssh-keygen`生成key，然后将公钥上传到GitHub上，才能正常使用git协议访问`git@github.com:xxx/xxxx.git`。

* 如果出现难以定位的内存访问报错（如segmentation fault, bad alloc)，可以使用[Address Sanitizer](https://github.com/google/sanitizers/wiki/AddressSanitizer)来帮助自己定位错误。 Asan也可以确保自己程序运行过程中没有内存泄露等问题。在wing中可以通过添加cmake选项`-DCMAKE_BUILD_TYPE=debug -DASAN=on`来启用asan。只在debug模式中生效。

* Wing使用exception进行错误处理，因此在正常运行中也可能会throw exception，例如可能会`throw ParserException("Invalid expression.")`。在调试时如果IDE给all exceptions打了断点则可能会在这种正常exception处停下来，这并不意味着程序出了BUG，直接跳过即可。

* Autolab在正常使用时可能会出现500 internal error。如果遇到了请不用慌张，在handin history查看评测结果即可。

* 最终得分在deadline+late submission过后才会显示在gradebook上。

* autolab上due和last day to handin的区别：due之后仍然可以提交，但是会消耗late days，所有lab总共5天late days，用完late days后的提交每晚一天分数会-10%（按syllabus中的描述）。所以due之前已经满分的同学注意不要再次提交以免误用late days。
