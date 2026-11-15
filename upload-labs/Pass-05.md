# Pass-05 后缀大小写绕过

## 一、关卡说明

黑名单匹配时 **没有对扩展名做大小写统一处理** ，而文件解析层面对大小写不敏感，形成绕过。

## 二、源码分析

```php
$deny_ext = array(".php",".php5",".php4",".php3",".php2",".html",".htm",
                  ".phtml",".pht",".pHp",".pHp5",".pHp4",".pHp3",".pHp2",
                  ".Html",".Htm",".pHtml",".jsp",".jspa",".jspx");

$file_ext = strrchr($file_name, '.');
if (!in_array($file_ext, $deny_ext)) {
    // 允许上传
}
```

关键矛盾：

- `in_array()` 是 **严格区分大小写** 的字符串比较
- 而 Windows 文件系统和部分 Apache 配置在 **解析时大小写不敏感** 

黑名单里虽然写了 `.php` 和部分 `.pHp` 变体，但大小写组合数量太多，不可能全部覆盖。

## 三、绕过步骤

1. 将 webshell 后缀改为黑名单中 **没有的排列方式** ，例如 `.PHP`、`.Php`、`.pHP`
2. 直接上传该文件
3. `in_array('.PHP', $deny_ext)` 返回 false，校验通过
4. 访问 `upload/shell.PHP`，代码被解析执行

## 四、验证

访问 `http://靶场地址/upload/shell.PHP`，PHP 代码正常执行。

判断依据：文件系统能找到该文件、PHP 能解析，说明大小写不同但被当作同一后缀处理。

## 五、防御建议

1. 校验前对扩展名使用 `strtolower()` 统一转小写再比较
2. 使用白名单机制，从根本上避免"列举不全"
3. 上传后的文件由服务端重命名为随机字符串 + 固定后缀，用户不可控
