# Pass-02 MIME 类型（Content-Type）绕过

## 一、关卡说明

前端校验已移除，改为服务端校验文件的 MIME 类型，只允许 `image/jpeg`、`image/png`、`image/gif`。

## 二、源码分析
服务端通过 `$_FILES['upload_file']['type']` 判断文件类型：
php```
if (($_FILES['upload_file']['type'] == 'image/jpeg') ||
    ($_FILES['upload_file']['type'] == 'image/png') ||
    ($_FILES['upload_file']['type'] == 'image/gif')) {
    // 允许上传
}```

问题所在：`type` 字段的值来自 HTTP 请求头中的 `Content-Type`， **完全由客户端提交** ，服务端并没有真正检测文件内容。这是典型的"信任了不可信输入"。

## 三、绕过步骤

1. 将 webshell 文件改名为 `shell.php`
2. Burp Suite 开启代理拦截
3. 上传文件，在 Burp 中拦截请求包
4. 找到：
   Content-Type: application/x-php

5. 修改为：
    ```
   Content-Type: image/jpeg
    ```
6. 放行请求，文件上传成功，路径为 `upload/shell.php`
7. 访问该路径或使用蚁剑连接，验证代码可执行

## 四、验证

蚁剑添加 URL `http://靶场地址/upload/shell.php`，连接成功，可执行命令。

## 五、防御建议

1. MIME 类型校验必须 **基于服务端对文件内容的实际检测** ，例如 PHP 的 `finfo_file()` 或 `mime_content_type()`
2. 绝不信任客户端提交的 `Content-Type`
3. 结合扩展名白名单与文件头校验形成多层防护

