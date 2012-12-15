## 介绍
ahtml 是一个服务端脚本语言。ahtml 以 AAuto 作为母语言，即可以实现在 html 中嵌入 AAuto 代码，嵌入规则很简单，与 php 类似：

```html+php
<?
var tab = { "Hello"; "world" };
?>
...
<body>
<?
for (index, content in tab) {
  response.write("<p>第", index, "条: ", content, "</p>", '\r\n');
}
?>
</body>
...
```

如果你希望代码读起来更加优美，也可以这样写：

```html+php
...
<body>
<? for index, content in tab begin ?>
	<p>第<?=index?>条: <?=content?></p>
<? end ?>
</body>
...
```

## 运行环境

### 操作系统需求
ahtml 需要运行在 Windows 操作系统下，且 Windows 平台必须高于 Windows 2000。

### 其他需求
由于 ahtml 是一个 FastCGI 应用，而且目前 ahtml 仅支持通过命名管道 (Named pipe) 的方式同 Web 服务器（FastCGI 宿主）实现数据交互，您需要一个支持 FastCGI 的 Web 服务器软件（如 IIS）。

## 配置

### Windows 2003 Server + IIS 6

#### 下载源码并发布为 exe 文件
下载或检出最新的工程源码文件，用 AAuto Quicker 将工程发布为 exe 文件。

#### 下载并安装 FastCGI for IIS6
IIS6 默认不支持 FastCGI，需要下载扩展。

下载地址: ``http://www.iis.net/downloads/microsoft/fastcgi-for-iis``

#### 配置 FastCGI
在命令提示符下运行：<br />
``cscript fcgiconfig.js -add -section:"ahtml" -extension:ahtml -path:"{所在文件夹}/ahtmlcgi.exe"``

如果服务器不支持或禁用了 ``cscript``，请用记事本程序打开并编辑 ``%WINDIR%\system32\inetsrv\fcigext.ini``，在其末尾添加：
```ini
[Types]
ahtml=ahtml
[ahtml]
ExePath={所在文件夹}/ahtmlcgi.exe
```
（其中的 ``{所在文件夹}`` 应替换为第一步发布的 exe 所在的文件夹路径）

#### 配置 IIS 使之与 FastCGI 协同工作
1. 右击“网站”（或指定站点）选择“属性”，单击“主目录”选项，点击“配置”按钮。
2. 单击”添加“按钮，单击”浏览“，选中 ``%WINDIR%\system32\inetsrv\fcgiext.dll``。
3. 在扩展名里输入 ``.ahtml``，动作选择“限制为”，输入 ``GET，HEAD，POST``。
4. 确认“脚本引擎”和“检查文件是否存在”是否选择，如没选中，请选中。
5. 确认无误后，单击“确定”。

### Windows Server 2008 (或 Windows 7) + IIS7 及以上
#### 下载源码并发布为 exe 文件
下载或检出最新的工程源码文件，用 AAuto Quicker 将工程发布为 exe 文件。

#### 启用 FastCGI 组件
##### Windows Server 2008 或 Server 2008 R2 UI
1. 单击“开始”，指向“管理工具”，然后单击“服务器管理器”。
2. 在“角色摘要”中，单击“添加角色”。
3. 使用“添加角色向导”添加“Web 服务器 (IIS)”角色。
4. 有关如何安装默认 IIS 7 Web 服务器的详细说明，请参阅 部署静态内容服务器 (IIS 7)。
5. 在“选择角色服务”页上，请记下默认情况下安装的预选角色服务，然后选择以下附加的角色服务：<br />``CGI``
6. 在“要安装的功能摘要”页上，确认所做的选择，然后单击“安装”。
7. 在“安装结果”页上，确认已经成功安装了 Web 服务器 (IIS) 角色和所需角色服务，然后单击“关闭”。

#### 重启 IIS
在命令提示符中输入并运行：``iisreset``。

##### Windows Vista 或 Windows 7 UI
1. 单击“开始”，然后单击“控制面板”。
2. 在“控制面板”中，单击“程序”，然后单击“打开/关闭 Windows 功能”。
3. 在“Windows 功能”对话框中，单击“Internet 信息服务”以安装默认的功能，然后在“应用程序开发功能”部分中选择以下附加功能：<br />``CGI``
4. 单击“确定”以关闭“Windows 功能”对话框。

#### 下载并安装 Administration Pack
用于 FastCGI 配置。IIS 7.5 及以上无需操作此步骤。

下载地址：``http://www.iis.net/downloads/microsoft/administration-pack``

#### 配置 IIS 使之与 FastCGI 协同工作
1. 打开 IIS 管理器，选中根节点（或指定站点），在右侧“功能视图”中双击进入“处理程序映射”。
2. 在右侧的“操作”面板中单击“添加模块映射”链接，将弹出“添加模块映射”对话框。
3. 在“请求路径”中输入 ``*.ahtml``，在“模块”下拉框中选中“FastCgiModule”。
4. 单击“可执行文件”右侧的“...”按钮，查找并选中刚才生成的 ``ahtmlcgi.exe`` 文件，在“名称”中填写 ``ahtml``。
5. 单击“请求限制...”按钮，在弹出的“请求限制”对话框中的“映射”选项卡中选中“仅当请求映射至以下内容时才调用处理程序”，并选择“文件”。
6. 单击“确定”按钮关闭“请求限制”对话框。
7. 单击“确定”按钮关闭“添加模块映射”对话框。

以上操作确认无误后，请重启 IIS。

#### 重启 IIS
使用管理员权限运行命令提示符，在命令提示符中输入并运行：``iisreset``。

## 语法
### 基本语法
ahtml 以 AAuto 作为母语言，因此 ahtml 支持 AAuto 所有的语法特性（包括基本流程语句、库引用机制等）。

### 标准库
除 AAuto 内核库之外，ahtml 会自动引入一些标准库。另外，并不是所有的 AAuto 标准库都可以在 ahtml 中被引入。下面的表格展示了自动引入的标准库以及可引入的标准库。

<table>
	<tr>
		<th>库名称</th>
		<th>是否自动引入</th>
	</tr>
	<tr>
		<td>console</td>
		<td>是</td>
	</tr>
	<tr>
		<td>com</td>
		<td>是</td>
	</tr>
	<tr>
		<td>util</td>
		<td>是</td>
	</tr>
	<tr>
		<td>fsys</td>
		<td>否</td>
	</tr>
</table>

### 用户库
除标准库之外，用户可自定义用户库。ahtml 文件同目录下可以新建一个名为 ``lib`` 的文件夹，其中的库文件即为“用户库”，可在 ahtml 文件中被引入（用法同 AAuto 的用户库相同）。

### 扩展对象
ahtml 作为服务端语言，同时作为 FastCGI 应用，需要处理来自 Web 服务器的客户端请求并作出响应。

简单地说，ahtml 的扩展对象有两个：

<table>
	<tr>
		<td>类型</td>
		<td>对象名</td>
	</tr>
	<tr>
		<td>请求对象</td>
		<td>request</td>
	</tr>
	<tr>
		<td>响应对象</td>
		<td>response</td>
	</tr>
</table>

#### 请求 (request) 对象
请求对象是用来获取来自 Web 服务器的客户端请求信息的（如 Web 服务端、站点信息，Query String、POST Data、附加协议头等）。

就目前而言，request 对象有四个键值对成员：
<table>
	<tr>
		<td>成员名</td>
		<td>用途</td>
	</tr>
	<tr>
		<td>server</td>
		<td>来源于服务器的信息，相当于 PHP 中的 ``$_SERVER`` 数组</td>
	</tr>
	<tr>
		<td>post</td>
		<td>客户端的 POST Data，相当于 PHP 中的 ``$_POST`` 数组</td>
	</tr>
	<tr>
		<td>get</td>
		<td>客户端的 Query String，相当于 PHP 中的 ``$_GET`` 数组</td>
	</tr>
	<tr>
		<td>cookie</td>
		<td>客户端的 COOKIE，相当于 PHP 中的 ``$_COOKIE`` 数组</td>
	</tr>
</table>

其操作方法如同 AAuto 中的普通键值对一样（需要注意的是，``request.cookie`` 比较特殊，不支持直接遍历。

范例：
```html+php
<!doctype html>
<html>
<head>
<meta charset="UTF-8">
<title>request.server 测试</title>
</head>
<body>
<table>
	<tr>
		<th>键名</th>
		<th>值</th>
	</tr>
	<? for key, value in request.server begin ?>
	<tr>
		<td><?=key?></td>
		<td><?=value?></td>
	</tr>
	<? end ?>
</table>
</body>
</html>
```

需要注意的是，实际应用时应在输出前转义 html 字符，避免 XSS 攻击。
