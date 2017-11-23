---
title: yii 常见问题处理
date: 2017-11-23 09:01:47
tags:
---

## yii 常见问题处理

### post 请求接口时报404
这是由于YII处理post请求，是有一个CSRF验证

#### 解决方法一：
``` php
// 全局解决 
// 修改config/web.php
$config = [
    'components' => [
        'request' => [
            // !!! insert a secret key in the following (if it is empty) - this is required by cookie validation
            'cookieValidationKey' => 'sfsdfsdfdsfewfwefef',
            'enableCookieValidation' => false,      
            'enableCsrfValidation' => false,        // 这里设置false
        ],
    ],
];
```

#### 解决方法二：
``` php
// 在需要post的控制器中加配置

public $enableCsrfValidation = false;
```
