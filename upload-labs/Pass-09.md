# Pass-09 点空格点绕过

## 一、关卡说明

Pass-06 和 Pass-07 的补丁版本：代码同时做了 `trim()` 去空格和去点号处理，但处理顺序存在缺陷，可用「点 空格 点」组合绕过。

## 二、源码分析

```php
$file_name = trim($_FILES['upload_file']['name']);
$file_name = deldot($file_name);  // 删除末尾的点
$file_ext = strrchr($file_name, '.');
$file_ext = strtolower($file_ext);
if (!in_array($file_ext, $deny_ext)) { ... }
```

处理流程的漏洞：

- `deldot()` 先删掉末尾的点，但 **没有处理空格** 
- `strrchr()` 再截取最后一个点后面的内容

当文件名是 `shell.php . .`（点+空格+点）时：

1. `deldot()` 删除末尾的点 → `shell.php . `
2. `strrchr()` 找到最后一个 `.` 后是 `" . "`
3. 不在黑名单中，校验通过
4. Windows 保存时去掉末尾空格和点，落盘为 `shell.php`

## 三、绕过步骤

1. 抓包拦截
2. 文件名修改为 `shell.php . .`（php 后加 空格点空格点）
3. 也可以用 Burp 的十六进制编辑器精确插入空格（`%20`）
4. 放行，上传成功
5. 访问 `upload/shell.php` 执行

## 四、验证

直接从返回信息中去掉空格和点号，访问 `upload/shell.php`，代码正常执行。

## 五、防御建议

1. 使用循环处理，反复执行 `trim()` 和去点操作直到文件名稳定
2. 使用正则 `preg_replace('/[\.\s]+$/', '', $file_name)` 一次清除所有末尾空白和点
3. 白名单 + 服务端重命名才是根本方案
