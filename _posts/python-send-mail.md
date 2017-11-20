---
title: Python发送邮件
date: 2017-11-17 19:00:00
categories: '程序媛|Coding'
tags: 
  - Python
  - html
---
日常工作中有些临时报表或者报警需要通过邮件形式进行。
<!--more-->

# 导入必要代码库


```python
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
from email.mime.image import MIMEImage
from email.mime.application import MIMEApplication
from os.path import basename
from premailer import transform
```

# 定义函数
主要功能包括发送普通邮件、抄送邮件和密送邮件。  
邮件正文中可添加文字（标题、段落）、表格和图片。  
可添加邮件附件。  
具体限制根据不同的邮箱服务器而有所不同，最好在正式邮件发送前多进行测试。  

调用函数时需要的参数信息：
```python
send_mail(
    from_addr='<发送的邮箱地址>', 
    password='<发送邮箱的密码或授权码>', 
    smtp_server='<发送邮箱的服务器>', 
    smtp_port=<发送邮箱的服务器端口>,
    to_addr=['<收件人邮箱的地址>'], 
    subject='<邮件标题>',
    html_text='<邮件内容（复杂的可先生成并存在其他变量内）>',
    styles = '<邮件内容的html的格式配置>', 
    cc_addr = ['<被抄送人邮箱的地址>'], 
    bcc_addr = ['<被密送人邮箱的地址>'],  
    alternative_plain_text = '', 
    local_images = {'<图片cid>': '<图片文件名>'}, 
    attachments = ['<附件文件名>']
)
```

函数定义如下：
```python
def send_mail(
    from_addr, 
    password, 
    smtp_server, 
    to_addr, 
    subject, 
    html_text, 
    smtp_port=0, 
    styles = '', 
    cc_addr = None, 
    bcc_addr = None,  
    local_images = None, 
    attachments = None
):
    
    cc_addr = cc_addr or []
    bcc_addr = bcc_addr or []
    local_images = local_images or {}
    attachments = attachments or []    
    to_all_addr = to_addr+cc_addr+bcc_addr

    # 将styles和html_text渲染在一起
    html_text = transform(styles+html_text)

    # Create the root message and fill in the from, to, and subject headers
    msgRoot = MIMEMultipart('related')
    msgRoot['Subject'] = subject
    
    msgText = MIMEText(html_text, 'html')
    msgRoot.attach(msgText)

    msgRoot.add_header('From',from_addr)
    msgRoot.add_header('To', ",".join(to_addr))
    msgRoot.add_header('Cc', ",".join(cc_addr))
    msgRoot.add_header('Bcc', ",".join(bcc_addr))
	
    # We reference the image in the IMG SRC attribute by the ID we give it below
    # This example assumes the image is in the current directory
    for cid, path in local_images.items():
        fp = open(path, 'rb')
        msgImage = MIMEImage(fp.read())
        fp.close()

        # Define the image's ID as referenced above
        msgImage.add_header('Content-ID', '<{cid}>'.format(cid = cid))
        msgRoot.attach(msgImage)

    for path in attachments or []:
        with open(path, "rb") as file:
            part = MIMEApplication(
                file.read(),
                Name=basename(path)
            )
            part['Content-Disposition'] = 'attachment; filename="{}"'.format(basename(path)) 
            msgRoot.attach(part)    

    # Send the email (this example assumes SMTP authentication is required)
    import smtplib
    smtp = smtplib.SMTP()
    smtp.connect(smtp_server, smtp_port)
    # smtp = smtplib.SMTP_SSL(smtp_server, smtp_port)
    
    smtp.login(from_addr, password)
    smtp.sendmail(from_addr, to_all_addr, msgRoot.as_string())
    smtp.quit()
```


不同邮件系统所需的设置会有所不同，例如：  
* 要用腾讯企业邮箱作为发送邮箱时，使用以下两句代码：
```Python
smtp = smtplib.SMTP()
smtp.connect(smtp_server, smtp_port)
```
把`smtp_server`填充成`smtp.exmail.qq.com`，`smtp_port`保持默认的`0`，`password`也填写邮箱的登录密码即可。


* 而要使用QQ邮箱作为发送邮箱时，就需要把上面两句代码改写为
```Python
smtp = smtplib.SMTP_SSL(smtp_server, smtp_port)
```
且需要提前在QQ邮箱中`设置`->`账户`中开启SMTP服务，并生成“授权码”，并在`password`处填写授权码。
此外，还需要把`smtp_server`填充为`‘smtp.qq.com'`，`smtp_port`填写为`465`。

设置部分截图如下：

![QQ邮箱设置](image_003.png)

# 函数调用 & html styles 应用


`.py`文件与`muban`文件夹平级，以下`.html`模板文件都存在`muban`文件夹下。  
一般运行代码时可选择相对路径引用，如下所示。不过碰到在docker中运行时只能用绝对路径的情况。


```python
path = "muban/"

from jinja2 import Template, Environment, FileSystemLoader
templateLoader = FileSystemLoader(searchpath=path)
env = Environment(loader=templateLoader)
```

## 插入文字

* 如果不需要任何格式修饰，可以直接往`html_text`输入长的`Python` `string`。

* 如果邮件的正文中有部分固定文字的格式，还有一部分内容可以由使用函数的用户自定义变量的值来进行填充，可以使用以下方式。

在`header.html`中写入如下内容：
```html
<h4>DEAR ALL～，此为{{date_str}}自动发送的邮件，请勿直接回复，如有疑问，请与{{author}}联系，谢谢！ </h4>
```
然后运行以下代码，就可把`date_str`和`author`的值填充进邮件正文中了。


```python
start_template = env.get_template('header.html')
date_str = datetime.strftime(datetime.now(), '%Y-%m-%d %H:%M:%S')
start_statement = start_template.render(date_str=date_str,author='XXX')

html_text = start_statement
```

## 插入表格

把以下表格styles代码存入文件`table_template.html`中。

```html
{% macro table(data) %}
    <table>
         {% for row in data%}
		{% if (row[0]!='序号') and (row[0]%2==0) %}
		<tr class='odd'>
		{% elif (row[0]!='序号') and (row[0]%2!=0) %}
		<tr class ='even'>
		{% else %}
		<tr class ='tableHeader'>
		{% endif %}
                {% for col in row%}
                	<td>{{col}}</td>
                {% endfor %}
            	</tr>
        {% endfor %}
    </table>
{% endmacro %}
```

然后在`main_body_template.html`中输入以下代码：
```
{% from "table_template.html" import table with context %}
{{table(data)}}
```

将需要发送在邮件内容中的`pandas.DataFrame`存在`input_df`变量中，则应用以上模板的方法如下：


```python
from jinja2 import Template, Environment, FileSystemLoader
templateLoader = FileSystemLoader(searchpath='./')
env = Environment(loader=templateLoader)
```


```python
table_template = env.get_template('main_body_template.html')
header = list(input_df.columns)
header.insert(0, '序号')
email_list = []
email_list.append(header)
email_list.extend(input_df.to_records())
html_text = table_template.render(data=email_list)
```
再在以上函数中引入如下styles：

```python
styles = '''
<style>
    table {
        background:#87ceeb;
        color: #333; /* Lighten up font color */
        font-family: Helvetica, Arial, sans-serif; /* Nicer font */
        width: 640px;
        border-collapse:
        collapse; border-spacing: 0;
    }

   td, th { border: 1px solid #CCC; height: 30px; } /* Make cells a bit taller */

   th {
        background: #F3F3F3; /* Light grey background */
        font-weight: bold; /* Make sure they're bold */
    }
   td {
        /*background: #FAFAFA;  Lighter grey background */
        text-align: center; /* Center our text */
    }
   .odd>td { background: #FEFEFE;}
   .even>td { background: #F1F1F1;}

</style>

'''
```

应用在如下`pd.DataFrame`上，可得到下面的显示效果（不同邮箱系统可能会存在差异）。


```python
input_df = pd.DataFrame(
    {
        '产品': list('ABCD'),
        '销量': [100, 200, 300, 200] 
    }
)
input_df
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>产品</th>
      <th>销量</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>A</td>
      <td>100</td>
    </tr>
    <tr>
      <th>1</th>
      <td>B</td>
      <td>200</td>
    </tr>
    <tr>
      <th>2</th>
      <td>C</td>
      <td>300</td>
    </tr>
    <tr>
      <th>3</th>
      <td>D</td>
      <td>200</td>
    </tr>
  </tbody>
</table>
</div>


![表格显示效果](image_002.png)

## 插入图片

将生成好的图片保存在代码同级或以下的路径中，然后将`cid`和图片文件名一一对应，如下所示。  

```Python
local_images = {
    '01': 'pic01.png',
    ...
    '10': 'pic10.png'
}
```


**注意：若图片数量大于等于10张，需要将`cid`编号左边填充`0`，否则，`cid`排序不正确会导致图片显示顺序出错。**  
如果有10张图片，而`cid`直接从`1`到`10`，则`cid`为`10`的图片会显示在`1`的后面、`2`的前面。

## 添加附件

将附件文件存放在代码路径或以下，后将文件路径填写在`attachments`下。

# 定时任务设置


```python
from apscheduler.schedulers.background import BlockingScheduler
def aps_task():
    send_mail(...)
    
if __name__=='__main__':
    print('scheduler starts')
    scheduler  = BlockingScheduler()
    scheduler.add_job(func=aps_task, trigger='cron',minute='*/30')
    scheduler.start()
```
