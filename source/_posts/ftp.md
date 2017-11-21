---
title: ftp 连接上传下载
date: 2017-11-21 11:35:14
tags: ftp upload download
---

## ftp 连接上传下载

``` bash
#-*- coding: UTF-8 -*-
#
# ftp 连接上传下载
#
#

import ftplib
import os
import os.path
import shutil
import datetime

def ftp_up(filename):
    ftp=ftplib.FTP() 
    ftp.set_debuglevel(2)#打开调试级别2，显示详细信息;0为关闭调试信息 
    ftp.connect('192.168.0.1','21')#连接 
    ftp.login('user','passwd')#登录，如果匿名登录则用空串代替即可 
    print ftp.getwelcome()#显示ftp服务器欢迎信息
    try:
        ftp.mkd('pdf')  
    except:  
        print('dir has existed %s' % 'pdf')

    ftp.cwd('pdf') #选择操作目录 
    bufsize = 1024#设置缓冲块大小 
    file_handler = open(filename,'rb')#以读模式在本地打开文件 
    ftp.storbinary('STOR %s' % os.path.basename(filename),file_handler,bufsize)#上传文件 
    ftp.set_debuglevel(0) 
    file_handler.close() 
    ftp.quit() 
    print "ftp up OK" 
 
def ftp_down(host,port,user,passw,rootDir,locaDir): 
    ftp=ftplib.FTP() 
    ftp.set_debuglevel(2) 
    ftp.connect(host,port) 
    ftp.login(user,passw) 
    print ftp.getwelcome()#显示ftp服务器欢迎信息 
    ftp.cwd(rootDir) #选择操作目录 
    files = ftp.nlst()
    for name in files:   
        bufsize = 1024 
        file_handler = open(locaDir + name,'wb').write #以写模式在本地打开文件 
        ftp.retrbinary('RETR %s' % os.path.basename(name),file_handler,bufsize)#接收服务器上文件并写入本地文件 
    ftp.set_debuglevel(0) 
    ftp.quit() 
    print "ftp down OK"

def copyFiles(sourceDir,  targetDir): 
    if sourceDir.find(".svn") > 0: 
        return 
    for file in os.listdir(sourceDir): 
        sourceFile = os.path.join(sourceDir,  file) 
        targetFile = os.path.join(targetDir,  file) 
        if os.path.isfile(sourceFile): 
            if not os.path.exists(targetDir):  
                os.makedirs(targetDir)  
            if not os.path.exists(targetFile) or(os.path.exists(targetFile) and (os.path.getsize(targetFile) != os.path.getsize(sourceFile))):  
                    open(targetFile, "wb").write(open(sourceFile, "rb").read()) 
        if os.path.isdir(sourceFile): 
            First_Directory = False 
            copyFiles(sourceFile, targetFile)
    print 'copy them to '+targetDir


#下载
ftp_down('192.168.0.1','21','user',"passwd",'happ','toftp/')

#上传
filesPath = '/home/data/company_annual_pdf'
for root,dirs,files in os.walk(filesPath):
    for f in files:
        ftp_up(os.path.join(root,f))

```

