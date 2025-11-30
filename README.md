# 天气预报在线查询工具

一个功能完整的纯前端天气查询应用，支持查看实时天气和未来7天预报。

## 功能特点

- **智能定位**：首次访问自动根据IP地址定位并显示当前位置天气
- **智能缓存**：天气数据本地缓存5分钟，避免频繁API请求，提升响应速度
- **实时天气查询**：显示当前温度、体感温度、湿度、风速等详细信息
- **7天天气预报**：查看未来一周的天气趋势
- **热门城市快速查询**：一键查询12个热门城市天气
- **中英文支持**：支持中文和英文城市名称搜索
- **响应式设计**：完美适配PC端和移动端
- **美观界面**：现代化Facebook风格设计，简洁专业

## 使用说明

### 第一步：获取API密钥

1. 访问 [WeatherAPI.com](https://www.weatherapi.com/signup.aspx)
2. 免费注册账号
3. 登录后在个人中心获取API密钥

### 第二步：配置API密钥

打开 `app.js` 文件，找到第4行：

```javascript
const API_KEY = 'YOUR_API_KEY_HERE';
```

将 `YOUR_API_KEY_HERE` 替换为你获取的API密钥：

```javascript
const API_KEY = '你的API密钥';
```

### 第三步：启动应用

1. **方式一：直接打开**
   - 双击 `index.html` 文件即可在浏览器中打开

2. **方式二：本地服务器**（推荐）
   ```bash
   # 如果安装了Python 3
   python -m http.server 8000

   # 如果安装了Node.js
   npx http-server
   ```
   然后在浏览器访问 `http://localhost:8000`

### 第四步：查询天气

1. **自动定位**（首次访问）：
   - 打开页面后会自动根据您的IP地址定位
   - 自动显示当前位置的天气信息
   - 仅在浏览器会话期间首次访问时执行

2. **搜索框查询**：
   - 在搜索框输入城市名称（如：北京、Beijing、Shanghai）
   - 点击"查询天气"按钮或按回车键

3. **热门城市快速查询**：
   - 点击搜索框下方的热门城市链接即可快速查询


## 生产环境部署

### 使用 Nginx 部署

#### 1. 安装 Nginx

**Ubuntu/Debian:**
```bash
sudo apt update
sudo apt install nginx
```

**CentOS/RHEL:**
```bash
sudo yum install nginx
```

**macOS:**
```bash
brew install nginx
```

#### 2. 配置 Nginx

创建站点配置文件：

```bash
sudo vim /etc/nginx/conf.d/weather.conf
```

添加以下配置：

```nginx
server {
    listen 80;
    server_name your-domain.com;  # 替换为你的域名或IP

    root /var/www/weather;  # 项目文件路径
    index index.html;

    # 启用 gzip 压缩
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml text/javascript;
    gzip_min_length 1000;

    location / {
        try_files $uri $uri/ /index.html;
    }

    # 静态资源缓存
    location ~* \.(css|js|jpg|jpeg|png|gif|ico|svg)$ {
        expires 7d;
        add_header Cache-Control "public, immutable";
    }

    # 安全头部
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
}
```



#### 5. 配置 HTTPS（可选）

使用 Let's Encrypt 免费 SSL 证书：

```bash
# 安装 Certbot
sudo apt install certbot python3-certbot-nginx

# 获取证书并自动配置
sudo certbot --nginx -d your-domain.com

# 自动续期（已默认配置）
sudo certbot renew --dry-run
```

配置会自动更新为：

```nginx
server {
    listen 80;
    server_name your-domain.com;
    return 301 https://$server_name$request_uri;  # 重定向到 HTTPS
}

server {
    listen 443 ssl http2;
    server_name your-domain.com;

    ssl_certificate /etc/letsencrypt/live/your-domain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/your-domain.com/privkey.pem;

    # SSL 优化配置
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;

    root /var/www/weather;
    index index.html;

    # gzip 压缩
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml text/javascript;

    location / {
        try_files $uri $uri/ /index.html;
    }

    # 静态资源缓存
    location ~* \.(css|js|jpg|jpeg|png|gif|ico|svg)$ {
        expires 7d;
        add_header Cache-Control "public, immutable";
    }

    # 安全头部
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
}
```

#### 6. 验证部署

访问你的域名或服务器IP：
```
http://your-domain.com
或
https://your-domain.com
```

#### 7. 常用 Nginx 命令

```bash
# 启动 Nginx
sudo systemctl start nginx

# 停止 Nginx
sudo systemctl stop nginx

# 重启 Nginx
sudo systemctl restart nginx

# 重载配置（不中断服务）
sudo systemctl reload nginx

# 查看状态
sudo systemctl status nginx

# 测试配置文件
sudo nginx -t

# 查看错误日志
sudo tail -f /var/log/nginx/error.log

# 查看访问日志
sudo tail -f /var/log/nginx/access.log
```



## 技术栈

- **HTML5**：结构语言
- **CSS3**：样式设计（支持渐变、动画、响应式）
- **JavaScript (ES6+)**：业务逻辑（使用Async/Await、Fetch API）
- **LocalStorage**：本地数据缓存
- **WeatherAPI.com**：天气数据接口

## 缓存机制

为了提升用户体验和减少API请求次数，应用采用了智能缓存策略：

### 工作原理

1. **首次查询**：请求API获取天气数据，并保存到 localStorage
2. **5分钟内再次查询**：直接从本地缓存读取，无需请求API
3. **超过5分钟**：缓存过期，重新请求API并更新缓存
4. **自动清理**：页面加载时自动清除所有过期缓存

### 技术细节

- **存储方式**：使用 localStorage 持久化存储
- **缓存键名**：`weather_cache_城市名`（小写）
- **有效期**：5分钟（300秒）
- **数据结构**：
  ```javascript
  {
    data: {...},      // 完整的天气数据
    timestamp: 1234567890  // 保存时间戳
  }
  ```



## 文件结构

```
web_weather/
├── index.html      # 主HTML文件
├── style.css       # 样式文件
├── app.js          # JavaScript逻辑
└── README.md       # 使用说明
```




## 常见问题

### 1. 提示"请先在 app.js 中配置您的 API 密钥"

**解决方法**：按照上述步骤获取并配置API密钥

### 2. 提示"未找到该城市"

**解决方法**：
- 检查城市名称拼写是否正确
- 尝试使用英文城市名称
- 尝试添加国家名称，如"Beijing, China"

### 3. 提示"网络连接失败"

**解决方法**：
- 检查网络连接
- 确认防火墙没有阻止访问
- 使用本地服务器而非直接打开文件

### 4. CORS跨域错误

**解决方法**：使用本地服务器运行（参见"第三步：启动应用"）

## API限制

WeatherAPI.com免费版限制：
- 每月100万次请求
- 每天最多10天预报（本项目使用7天）
- 支持历史天气查询
- 完全够个人使用

## 注意事项

1. **不要暴露API密钥**：如果将代码上传到GitHub等公开平台，请删除或隐藏API密钥
2. **遵守API使用条款**：不要频繁请求，建议添加缓存机制
3. **HTTPS访问**：如果部署到服务器，建议使用HTTPS


 

  
## 许可证

本项目采用 MIT 许可证，可自由使用和修改。

 

---

**享受查询天气的乐趣吧！** 🌤️
