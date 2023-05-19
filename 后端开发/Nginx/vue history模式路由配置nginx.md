参考文章 https://www.cnblogs.com/flytree/archive/2021/07/17/15022437.html
配置根目录
```
server {
	listen 8000;
	server_name localhost;
	root "D:/xxxx"
	location / {
		try_files $uri $uri/ /index.html;
	}
}
```

配置到子目录
```
server {
	listen 8000;
	server_name localhost;
	root "D:/xxxx"
	location / {
		try_files $uri $uri/ /index.html;
	}

	location /admin {
		alias 'D:/xxx/admin';
		try_files $uri $uri/ /admin/index.html;
	}
}
```