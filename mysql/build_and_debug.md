# MySQL ビルドとデバッグ

## Requirements

- OSX 11.6.8
- gcc
  ```
  > gcc --version
  Configured with: --prefix=/Applications/Xcode.app/Contents/Developer/usr --with-gxx-include-dir=/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/4.2.1
  Apple clang version 13.0.0 (clang-1300.0.29.30)
  Target: x86_64-apple-darwin20.6.0
  Thread model: posix
  InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin
  ```

## Dependencies

```
$ brew install cmake make openssl bison pkg-config
```

## Clone

```
$ git clone --depth 1  https://github.com/mysql/mysql-server.git
```

## Build

- buildディレクトリ作成

```
$ mkdir build && cd build
```

- cmake

```
$ cmake .. \
  -DWITH_DEBUG=ON \
  -DDOWNLOAD_BOOST=ON \
  -DWITH_BOOST=../boost
```

- make

```
$ make -j 4 || make
```

bin/mysqldとbin/mysqlが生成されてればOK.

## Server起動

### 初期化

パスワードが出るので記録します

```
bin/mysqld --initialize --basedir=$(pwd) --datadir=$(pwd)/data
```

### my.cnf

build/my.cnfを作成

```
[mysqld]
basedir=/Users/naoto/Documents/workspace/src/github.com/mysql/mysql-server/build
datadir=/Users/naoto/Documents/workspace/src/github.com/mysql/mysql-server/build/data
```

### 起動

```
$ bin/mysqld --defaults-file=./my.cnf
```

## Client接続

### 接続

記録したパスワードを使用します

```
$ bin/mysql -u root -p
```

### password再設定

```
$ ALTER USER root@localhost IDENTIFIED BY 'MySQL8.0';
```

## Debug

### launch.json

C/C++拡張のlldbを追加して`program`と`args`をそれぞれ修正します.

```diff
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(lldb) 起動",
            "type": "cppdbg",
            "request": "launch",
+           "program": "${workspaceFolder}/build/bin/mysqld",
+           "args": ["--defaults-file=${workspaceFolder}/build/my.cnf"],
            "stopAtEntry": false,
            "cwd": "${fileDirname}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "lldb"
        }
    ]
}
```

### デバッグ

あとはブレイクポイント貼って、デバッガ動かせば変数の中身を確認できる

## References

- https://masayuki14.hatenablog.com/entry/2020/07/30/MySQL8.0%E3%81%AE%E3%83%87%E3%83%90%E3%83%83%E3%82%B0%E3%83%93%E3%83%AB%E3%83%89%E3%82%92%E3%82%8F%E3%82%8A%E3%81%A8%E8%A9%B3%E3%81%97%E3%81%8F%E6%9B%B8%E3%81%84%E3%81%A6%E3%81%BF%E3%82%8B
- https://labs.gree.jp/blog/2021/05/21194/
