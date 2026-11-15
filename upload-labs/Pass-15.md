# Pass-15 exif_imagetype 检测绕过

## 一、关卡说明

服务端使用 `exif_imagetype()` 检查文件类型，该函数读取文件头并返回对应的常量。

## 二、原理说明

```php
if (exif_imagetype($upload_file) === false) {
    die('不是图片');
}
```

`exif_imagetype()` 只读取很短的头部字节来判断类型（比 `getimagesize()` 更宽松），本质上依然可以通过伪造文件头绕过。

## 三、绕过步骤

1. 按 Pass-13 的方法制作图片马：

   ```bash
   copy /b 1.jpg + shell.php shell.jpg
   ```

2. 上传 `shell.jpg`，`exif_imagetype()` 识别为 `IMAGETYPE_JPEG`，校验通过
3. 同样需要配合文件包含或 `.htaccess` 触发执行

## 四、验证

通过包含方式执行该图片，代码运行成功。

## 五、防御建议

1. `exif_imagetype()` 只检查文件头，防护强度低，必须配合二次渲染
2. 上传目录禁止解析脚本
3. 使用 `finfo` 结合白名单多重校验
