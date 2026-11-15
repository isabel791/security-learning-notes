# upload-labs 文件上传漏洞通关笔记

## 关卡目录

| 关卡 | 绕过类型 |
|---|---|
| Pass-01 | 前端 JS 校验绕过 |
| Pass-02 | MIME 类型（Content-Type）绕过 |
| Pass-03 | 黑名单特殊后缀绕过（php3/phtml 等） |
| Pass-04 | .htaccess 解析绕过 |
| Pass-05 | 后缀大小写绕过 |
| Pass-06 | 空格绕过 |
| Pass-07 | 点号绕过（php.） |
| Pass-08 | ::$DATA 绕过 |
| Pass-09 | 点空格点绕过 |
| Pass-10 | 双写绕过 |
| Pass-11 | %00 截断（GET） |
| Pass-12 | %00 截断（POST） |
| Pass-13 | 文件头检测绕过（图片马） |
| Pass-14 | getimagesize 检测绕过 |
| Pass-15 | exif_imagetype 检测绕过 |
| Pass-16 | 二次渲染绕过 |
| Pass-17 | 条件竞争 |
| Pass-18 | 条件竞争 + 重命名 |
| Pass-19 | 条件竞争 + 目录穿越 |
