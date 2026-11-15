# Pass-12 %00 截断（POST 方式）

## 一、关卡说明

与 Pass-11 思路相同，区别在于文件名字段通过 POST 传递，截断位置不同。

## 二、原理说明

Pass-11 中文件名在 GET 参数里，`%00` 会被 URL 解码。

Pass-12 中 `save_path` 通过 POST 隐藏字段提交，需要让 `%00` 作为 **原始字节** 进入字符串，而不是被转义成字面量 `%00` 三个字符。

源码示例：

```php
$save_path = $_POST['save_path'] . '/' . basename($_FILES['upload_file']['name']);
```

## 三、绕过步骤

1. 上传 `shell.php`
2. Burp 拦截请求，找到 `save_path` 参数
3. 把 `save_path` 改为：

   ```
   ../upload/shell.php%00
   ```

4.  **关键操作** ：在 Burp 中选中 `%00` 这两个字符，右键选择  **Convert selection → URL → URL-decode** ，此时 `%00` 会变成不可见字符（显示为一个点或空白）
5. 放行请求
6. 服务端拼接后路径在空字节处截断为 `../upload/shell.php`
7. 文件保存为 `shell.php`，访问执行

## 四、验证

访问 `http://靶场地址/upload/shell.php`，代码执行成功。

如果失败，检查是否需要先在 Burp 中解码再发送，或确认 PHP 版本。

## 五、防御建议

1. 保持 PHP 版本更新（≥5.3.4）
2. 严格过滤文件名中的控制字符（`\x00`-`\x1f`）
3. 白名单 + 服务端重命名
