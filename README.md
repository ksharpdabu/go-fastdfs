# 分布式文件服务器go-fastdfs（类fastdfs）在运维管理等方面优于fastdfs，更人性化

- 支持curl命令上传
- 支持浏览器上传
- 支持HTTP下载
- 支持多机自动同步
- 类fastdfs
- 高性能 （使用leveldb作为kv库）
- 高可靠（设计极其简单，使用成熟组件）

# 优点

- 无依赖(单一文件）
- 自动同步
- 失败自动修复
- 按天分目录方便维护
- 支持不同的场景
- 文件自动去重
- 支持目录自定义
- 支持保留原文件名
- 支持自动生成唯一文件名
- 支持浏览器上传
- 支持查看集群文件信息
- 支持集群监控邮件告警
- 支持token下载　token=md5(file_md5+timestamp)
- 运维简单，只有一个角色（不像fastdfs有三个角色Tracker Server,Storage Server,Client），配置自动生成
- 每个节点对等（简化运维）



# 启动服务器（已编译，[下载](https://github.com/sjqzhang/fastdfs/releases)体验）

`./fileserver`



# 配置自动生成  (conf/cfg.json)
```json
{
	"绑定端号": "端口",
	"addr": ":8080",
	"集群": "集群列表例如： http://10.1.xx.2:8080,http://10.1.xx.5:8080,http://10.1.xx.60:8080,注意是字符串数组(ip必须不一样)",
	"peers": ["http://10.1.xx.2:8080","http://10.1.xx.5:8080","http://10.1.xx.60:8080"],
	"组号": "组号",
	"group": "group1",
	"refresh_interval": 120,
	"是否自动重命名": "真假",
	"rename_file": false,
	"是否支持web上传": "真假",
	"enable_web_upload": true,
	"是否支持非日期路径": "真假",
	"enable_custom_path": true,
	"下载域名": "dowonload.web.com",
	"download_domain": "",
	"场景":"场景列表",
	"scenes":[],
	"默认场景":"",
	"default_scene":"default",
	"是否显示目录": "真假",
	"show_dir": true,
	"邮件配置":"",
	"mail":{
		"user":"abc@163.com",
		"password":"abc",
		"host":"smtp.163.com:25"
	},
	"告警接收邮件列表":"",
	"alram_receivers":[],
	"告警接收URL":"",
	"alarm_url":"",
	"下载是否需带token":"真假",
	"download_use_token":false,
	"下载token过期时间":"",
	"download_token_expire":600
}
```


# 命令上传

`curl -F file=@http-index-fs http://10.1.xx.60:8080/upload` 	


# WEB上传（浏览器打开）

`http://127.0.0.1:8080` 	

# 代码上传(选项参阅浏览器上传)

```python
import requests
url = 'http://127.0.0.1:8080/upload'
files = {'file': open('report.xls', 'rb')}
options={'output':'json','path':'','scene':''} #参阅浏览器上传的选项
r = requests.post(url, files=files)
print(t.text)
```


# 有问题请[点击反馈](https://github.com/sjqzhang/go-fastdfs/issues/new)


# Q&A
- 已经使用fastdfs存储的文件可以迁移到go fastdfs下么？
```
答案是可以的，你担心的问题是路径改变,go fastdfs为你考虑了这一点

curl -F file=@data/00/00/_78HAFwyvN2AK6ChAAHg8gw80FQ213.jpg -F path=M00/00/00/ http://127.0.0.1:8080/upload

同理可以用一行命令迁移所有文件

cd fastdfs/data && find -type f |xargs -n 1 -I {} curl -F file=@data/{} -F path=M00/00/00/ http://127.0.0.1:8080/

以上命令可以过迁粗糙
可以写一些简单脚本进行迁移

```

- 还需要安装nginx么？
```
可以不安装，也可以选择安装
go fastdfs 本身就是一个高性能的web文件服务器。
```

- 如何查看集群文件信息？
```
http://10.1.xx.60:8080/stat
```
- 可靠性怎样，能用于生产环境么？
```
本项目已大规模用于生产环境，如担心不能满足
可以在使用前对其各项特性进行压力测试，有任何
问题可以直接提issue
```

- 能不能在一台机器部置多个服务端？
```
不能，在设计之初就已考虑到集群的高可用问题，为了保证集群的真正可用，必须为不同的ip
错误　"peers": ["http://127.0.0.1:8080","http://127.0.0.1:8081","http://127.0.0.1:8082"]
正确　"peers": ["http://10.0.0.3:8080","http://10.0.0.4:8080","http://10.0.0.5:8082"]
```

- 有问题请[点击反馈](https://github.com/sjqzhang/go-fastdfs/issues/new)



