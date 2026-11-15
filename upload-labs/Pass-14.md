# Pass-14 getimagesize 检测绕过

## 一、关卡说明

服务端使用 `getimagesize()` 函数检测是否为合法图片，比单纯检查文件头更严格。

## 二、原理说明

```php
if (getimagesize($upload_file) === false) {
    die('不是图片');
}
```

`getimagesize()` 会解析图片结构，返回宽高等信息。文件头伪造够不上，必须让文件 **真的是一个能解析的图片** 。

攻击思路：在 **真实图片文件中** 嵌入 PHP 代码，即「图片马」。因为是完整图片，`getimagesize()` 能正常返回。

## 三、绕过步骤

 **制作图片马** 

```bash
copy /b 1.png + shell.php shell.png
```

 **上传** 

1. 上传 `shell.png`，`getimagesize()` 通过
2. 但 `.png` 默认不被解析，需要配合其他手段触发：
   - 结合文件包含漏洞
   - 结合 `.htaccess` 让 `.png` 解析为 PHP

## 四、验证

通过文件包含或 `.htaccess` 触发后，访问该文件，PHP 代码执行。

## 五、防御建议

1. `getimagesize()` 可被图片马绕过，需配合 **图片二次渲染** （GD 库重新生成图片）
2. 上传目录关闭脚本执行权限
3. 对上传图片重新编码，剥离附加数据
