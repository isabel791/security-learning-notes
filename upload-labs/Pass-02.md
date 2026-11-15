# Pass-02 MIME 类型（Content-Type）绕过

## 一、关卡说明
前端校验已被移除，改为服务端校验文件的 MIME 类型，只允许 `image/jpeg`、`image/png`、`image/gif`。

## 二、源码分析
服务端通过 `$_FILES['upload_file']['type']` 判断类型：
```php
if (($_FILES['upload_file']['type'] == 'image/jpeg') ||
    ($_FILES['upload_file']['type'] == 'image/png') ||
    ($_FILES['upload_file']['type'] == 'image/gif')) {
