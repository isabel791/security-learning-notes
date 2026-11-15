# Pass-07 点号绕过

## 一、关卡说明

与 Pass-06 思路一致，利用 Windows 自动去除文件名末尾点号的特性绕过黑名单。

## 二、源码分析

```php
$file_ext = strrchr($file_name, '.');
if (!in_array($file_ext, $deny_ext)) { ... }
```

服务端截取最后一个 `.` 之后的内容作为扩展名。

当文件名是 `shell.php.` 时：

- `strrchr('shell.php.', '.')` 返回 `"."`
- `in_array('.', $deny_ext)` 为 false，校验通过
- Windows 保存时去掉末尾的 `.`，落盘为 `shell.php`

## 三、绕过步骤

1. 抓包上传 `shell.php`
2. Burp 中把文件名改为 `shell.php.`（末尾加一个点）
3. 放行
4. 服务端校验扩展名为 `.`，通过黑名单检查
5. Windows 自动去除末尾点号，实际保存为 `shell.php`
6. 访问执行成功

如果加一个点不生效，可尝试多个点：`shell.php.....`

## 四、验证

访问 `http://靶场地址/upload/shell.php`，PHP 代码正常执行，蚁剑可连接。

## 五、防御建议

1. 使用 `trim($file_name, '. ')` 同时去除末尾的点和空格
2. 白名单校验扩展名
3. 服务端重命名文件，使用固定后缀
