---
title: yii导出pdf
date: 2017-11-30 14:13:33
tags: yii pdf 
---

## yii导出pdf

### 插件网站
http://demos.krajee.com/mpdf

### 安装 
``` php
$ php composer.phar require kartik-v/yii2-mpdf "*"
```
或添加:
``` php
"kartik-v/yii2-mpdf": "*"
```
到composer.json更新
``` php
php composer.phar update
```

### 使用
#### controller
``` php
use kartik\mpdf\Pdf;

public function actionExport()
{
    $model = Article::findOne(10);

    $pdf = new Pdf([
        'mode' => Pdf::MODE_ASIAN,      // 中文
        /** content 页面内容 渲染一个页面视图 **/
        'content' => $this->renderPartial('_base-info-table',[
            'model' => $model,
        ]),
        'methods' => [
            'SetHeader' => ['Generated By: 作者 || Generated On: ' . date("r")],
            'SetFooter' => ['|Page {PAGENO}|'],
        ]
    ]);

    return $pdf->render();
}
```

### 中文乱码
在mPDF的库文件中找到config.php
如：vendor/mpdf/mpdf/config.php
修改成下面的参数
``` php
$this->autoScriptToLang = true;
$this->autoLangToFont = true;
```
