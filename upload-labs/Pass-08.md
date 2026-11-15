# Pass-08 ::$DATA 绕过

## 一、关卡说明

利用 Windows 文件系统的 NTFS 数据流特性（Alternate Data Stream），在文件名后追加 `::$DATA` 绕过黑名单。

## 二、原理说明

Windows NTFS 支持 **多数据流** ，`文件名::$DATA` 表示访问该文件的默认数据流。

关键行为：

- 系统把 `shell.php::$DATA` 保存时，实际落盘的文件名是 `shell.php`
- 而 PHP 在判断扩展名时看到的是 `shell.php::$DATA`

因此：

- `strrchr('shell.php::$DATA', '.')` 返回 `".php::$DATA"`
- `in_array('.php::$DATA', $deny_ext)` 为 false，黑名单被绕过
- Windows 实际保存为 `shell.php`，正常被解析

## 三、绕过步骤

1. 抓包拦截上传请求
2. 将文件名修改为：

   ```
   filename="shell.php::$DATA"
   ```

3. 放行请求
4. 返回的路径可能是 `upload/shell.php::$DATA`，去掉 `::$DATA` 后访问
5. 访问 `upload/shell.php`，代码执行成功

## 四、验证

访问 `http://靶场地址/upload/shell.php`，PHP 正常执行。

## 五、防御建议

1. 过滤文件名中的 `::` 和 `$` 特殊字符
2. 白名单校验扩展名，不依赖黑名单
3. 服务端重命名文件，彻底切断用户对文件名的控制
