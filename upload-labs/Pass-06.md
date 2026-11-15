# Pass-06 空格绕过

## 一、关卡说明

服务端使用黑名单校验扩展名，但没有对文件名做去空格处理，利用 Windows 特性绕过。

## 二、源码分析

```php
$deny_ext = array(".php",".php5",".php4",".php3",".php2",".html",".phtml",...);
$file_ext = strrchr($file_name, '.');
if (!in_array($file_ext, $deny_ext)) { ... }
```

代码直接用 `strrchr()` 截取最后一个点后面的内容做比较， **没有执行 `trim()` 去除空格** 。

关键特性：Windows 文件系统会自动去除文件名 **末尾** 的空格和点号。

## 三、绕过步骤

1. 抓包上传 `shell.php`
2. 在 Burp 中把 `filename="shell.php"` 改为 `filename="shell.php "`（末尾加一个空格）
3. 放行请求
4. 服务端比较的是 `".php "`（带空格），不在黑名单里，校验通过
5. Windows 保存文件时自动去掉末尾空格，实际落盘为 `shell.php`
6. 访问 `upload/shell.php` 执行成功

 **注意** ：此绕过只在 Windows + Apache 环境下有效，Linux 下空格会被保留，无法利用。

## 四、验证

访问 `http://靶场地址/upload/shell.php`，代码正常执行。

## 五、防御建议

1. 文件名处理时先执行 `trim()` 去除首尾空格和点号
2. 使用白名单校验扩展名
3. 服务器端重命名上传文件，不保留用户提交的原始文件名

