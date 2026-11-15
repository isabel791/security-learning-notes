# Pass-10 双写绕过

## 一、关卡说明

服务端对黑名单中的后缀进行了字符串替换删除，但没有递归处理，可用双写绕过。

## 二、源码分析

```php
$deny_ext = array("php","php5","php4","php3","php2","html","htm","phtml",...);
$file_name = str_ireplace($deny_ext, "", $file_name);
```

`str_ireplace()` 会把匹配到的字符串 **替换为空** 。

当文件名为 `shell.pphphp` 时：

- 替换过程扫描到中间的 `php`，删除后变成 `shell.php`
- 但 `str_ireplace`  **只扫描一遍** ，不会再处理删除后新拼接出来的内容

实际执行过程：`shell.pphphp` → 删除第一个 `php`（在 `pphphp` 中第二个 p 后）→ 结果是 `shell.php`

## 三、绕过步骤

1. 抓包拦截上传请求
2. 文件名改为 `shell.pphphp`
3. 放行请求
4. 服务端替换后路径为 `upload/shell.php`
5. 访问该路径，代码执行成功

如果不生效，调整双写位置：`shell.phpphp`、`shell.pphphp` 等

## 四、验证

访问 `http://靶场地址/upload/shell.php`，PHP 正常执行，蚁剑连接成功。

## 五、防御建议

1. 使用白名单而非黑名单
2. 若必须过滤，应循环处理直到字符串不再变化：

   ```php
   do {
       $old = $file_name;
       $file_name = str_ireplace($deny_ext, "", $file_name);
   } while ($old !== $file_name);
   ```

3. 服务端重命名文件，从根源解决
