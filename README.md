
参考 [Building and Distributing Applications](https://github.com).


假设源码由两个文件组成，`A.ss` 和 `B.ss`，其中 `A.ss` 依赖 `B.ss`。下面我们将其编译为可供分发的二进制文件。


### 将源码转为 object file


在 Chez Scheme 的 REPL 中（下同）输入



```


|  | ;; REPL |
| --- | --- |
|  | (compile-library "B.ss")  ; by default it compiles to "B.so" |
|  | (compile-program "A.ss")  ; by default it compiles to "A.so" |


```

注：`so` 后缀是 Scheme object 的缩写，而非 Linux 系统下的 shared object 文件，尽管两者用途类似，但文件格式不同。


### （可选）合并 object file



```


|  | ;; REPL |
| --- | --- |
|  | (concatenate-object-files "out.so" "B.so" "A.so") |


```

### 用 `scheme-start` 包装程序入口


在 `A.ss` 中，用 `scheme-start` 包装程序入口，其中 `fns` 为命令行传入参数



```


|  | ;; A.ss |
| --- | --- |
|  | (scheme-start |
|  | (lambda fns |
|  | #;(initialize-application) |
|  | #;(start-application fns))) |


```

### 将 object file 转为 boot file



```


|  | ;; REPL |
| --- | --- |
|  | (make-boot-file "out.boot" '("scheme") "out.so") |
|  | ; (make-boot-file "out.boot" '("scheme") "B.so" "A.so") |


```

### 将 Chez Scheme 可执行文件复制并命名为 boot file 的名称



```


|  | ## bash |
| --- | --- |
|  | cp /usr/bin/scheme ./out |


```

### 可以运行了！



```


|  | ## bash |
| --- | --- |
|  | ./out -b out.boot |
|  | # scheme -b out.boot |


```

理论上 `SCHEMEHEAPDIRS="%x:" ./out` 也是[可以](https://github.com):[milou加速器](https://xinminxuehui.org)的，这时应该会自动识别出 boot file 的文件名，但是我没有成功...


