# Pass-04 .htaccess 解析绕过

## 一、关卡说明

黑名单进一步收紧，几乎封禁了所有 PHP 相关后缀，但仍然允许上传 `.htaccess` 文件。

## 二、原理说明

`.htaccess` 是 Apache 的 **目录级配置文件** ，放在某个目录下即可作用于该目录及子目录。

通过 `.htaccess` 可以让 Apache 把任意后缀的文件当作 PHP 解析：

```
AddType application/x-httpd-php .jpg
```

这行的含义是：把 `.jpg` 后缀的文件交给 PHP 解释器处理。

## 三、绕过步骤

 **第一步：上传 `.htaccess`** 

1. 本地新建文件，命名为 `.htaccess`（Windows 下可先命名为 `.htaccess.` 再改回来）
2. 内容写：

   ```
   SetHandler application/x-httpd-php
   ```

   或：

   ```
   AddType application/x-httpd-php .jpg
   ```

3. 通过页面上传该文件（黑名单未拦截 `.htaccess`）

 **第二步：上传图片马** 

4. 准备一个内容为 PHP 代码、但后缀为 `.jpg` 的 webshell
5. 上传 `shell.jpg`
6. 访问 `upload/shell.jpg`，代码被解析执行

## 四、验证

访问 `http://靶场地址/upload/shell.jpg`，PHP 代码正常执行，蚁剑连接成功。

关键判断依据：`.jpg` 本来应该被当作图片返回，现在却执行了 PHP，说明 `.htaccess` 生效。

## 五、防御建议

1. 黑名单中必须包含 `.htaccess`、`.htpasswd` 等 Apache 配置文件
2. 服务器配置 `AllowOverride None`，禁止上传目录的 `.htaccess` 生效
3. 根本方案：上传目录关闭脚本执行权限 + 文件存储在 Web 根目录之外
