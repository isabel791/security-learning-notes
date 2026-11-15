# Pass-19 条件竞争 + 目录穿越

## 一、关卡说明

最后一关，限制上传目录为指定目录，并通过 `basename()` 过滤，但过滤不彻底，配合条件竞争和路径穿越通关。

## 二、源码分析

```php
$ext = end(explode('.', $_FILES['upload_file']['name']));
if (!in_array($ext, $white_list)) { die('只允许上传图片'); }
$path = $_POST['upload_dir'] . '/' . basename($_FILES['upload_file']['name']);
move_uploaded_file($tmp_file, $path);
```

问题：

- `basename()` 只去除 **路径** 部分，但可以配合 `../` 绕过
- `upload_dir` 参数用户可控

## 三、绕过步骤

1. 上传一张 `shell.jpg`，抓包
2. 修改 `upload_dir` 参数为 `../upload` 或使用路径穿越
3. 文件名处保持 `shell.jpg`，但可通过修改 `filename` 字段做穿越
4. 由于只校验后缀不校验内容，可用图片马写马
5. 抢占访问执行，写入永久后门

实际通关可参考：把 `save_path` 或 `upload_dir` 改为 `../`，让文件落到可执行目录。

## 四、验证

确认目标目录出现可执行的 PHP 文件，蚁剑连接成功。

## 五、防御建议

1. 上传目录固定为白名单中的绝对路径，不接受用户输入
2. 使用 `realpath()` 校验最终路径在允许目录内
3. 上传目录禁止脚本执行
4. 服务端随机重命名 + 白名单后缀
