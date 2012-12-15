## 介绍
ahtml 是一个服务端脚本语言。ahtml 以 AAuto 作为母语言，即可以实现在 html 中嵌入 AAuto 代码，嵌入规则很简单，与 php 类似：

```
<?
var tab = { "Hello"; "world" };
?>
...
<body>
<?
for (i, v in tab) {
  response.write("<p>第", i, "条: ", v, "</p>", '\r\n');
}
?>
</body>
...
```

如果你希望代码读起来更加优美，也可以这样写：

```
...
<body>
<? for i, v in tab begin ?>
	<p>第<?=i?>条: <?=v?></p>
<? end//for; ?>
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
```
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
