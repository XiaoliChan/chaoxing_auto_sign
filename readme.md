## 功能描述

创建Autosign对象

参数为手机号码和密码，也支持学号登录，学号登录需要手动获取fid值(操作在下方)

支持普通签到，手势签到，二维码签到，位置签到，拍照签到

### 学号登录,获取schoolid
关于学号登录方式，有一个额外参数`schoolid`

http://passport2.chaoxing.com/login

![schoolid][5]


## 更新日志

3.21
- 加入activeid缓存，会将所有签到成功的activeid记录在activeid.txt,避免重复触发签到

3.20 
- 加入server酱，签到消息可推送至微信
- 纠正因证书问题，导致运行过程中，输出大量异常消息
- 优化签到类型的逻辑判断

3.18 新增二维码，拍照，位置签到

3.18 修复登录失败问题

3.17 更新手势签到，无需手动输入验证码

3.15 支持学号方式登录,目前可以通过(手机号码，邮箱，学号登录)

3.10 新增手势签到

## 其他签到脚本推荐


| 项目地址                                                | 开发语言   | 备注                                           |
| ------------------------------------------------------- | ---------- | ---------------------------------------------- |
| https://github.com/Wzb3422/auto-sign-chaoxing           | TypeScript | 超星学习通自动签到，梦中刷网课       |
| https://github.com/Huangyan0804/AutoCheckin             | Python     | 学习通自动签到，支持手势，二维码，位置，拍照等 |
| https://github.com/aihuahua-522/chaoxing-testforAndroid | Java       | 学习通（超星）自动签到               |
| https://github.com/yuban10703/chaoxingsign              | Python     | 超星学习通自动签到                   |


## 接口使用(非长期有效)

```
http://101.89.182.58:9090/sign/
```

请求代码示例：
```python
params = {
    'username': 'xxxxx',
    'password': 'xxxxx',
    'schoolid': '',
}
requests.post('http://101.89.182.58:9090/sign/', params=params)
```

在线接口调试：
http://101.89.182.58:9090/docs#/default/sign_sign__post


| 请求方式 |   参数   |  说明  | 是否必须 |
| :------: | :------: | :----: | :------: |
|          | username |  账号  |    是    |
|   **POST**   | password |  密码  |    是    |
|          | schoolid | 学校ID |    否    |
| | sckey | server酱key | 否 |


请求后，会自动签到所需要签到的课程

**如果是学号登录，fid参数必填**
**接口仅做学习参考，不建议长期使用**

## 扩展
1. 配合fastapi，挂到服务器上，定时请求，全天自动签到
2. 做成QQ或微信机器人插件，可以面向大众用户使用
3. 结合Server酱，可以将签到结果推送给用户微信

## 实现过程

### 1、 登录
```
# 手机号码及邮箱登录URL
# http://i.chaoxing.com/vlogin?passWord=passwordwu&userName=username

# 学号登录URL
# http://passport2.chaoxing.com/api/login?name={}&pwd={}&schoolid={}&verify=0
```
Post请求方式，参数就是账号密码
关于学号登录方式，有一个额外参数`schoolid`

http://passport2.chaoxing.com/login

![schoolid][5]


### 2、 访问课程主页
```
# 课程主页url
# http://mooc1-2.chaoxing.com/visit/interaction
```
访问课程主页，是获取所有课程的classid和courseid，需要这两个参数，才能拼接出该门课程的签到url
![获取classid,courseid][3]

### 3、访问任务页面
```
# 课程任务url
# https://mobilelearn.chaoxing.com/widget/pcpick/stu/index?courseId=209320132&jclassId=18855085
```
从课程主页获取classid courseid，现在就可以用到了，访问该课程任务url
这里的目的是为了获取`activeid`这个任务id
![无图片描述][4]

`onclick="activeDetail(129022258,2,null)"`
前面的数字就是`activeid` 后面的参数就是任务类型序号
```
2 签到
4 抢答
14 问卷
目前只知道这些
```
### 4、签到

拿到所有参数后，就可以签到了，直接get请求一下即可
```
# 签到url
# https://mobilelearn.chaoxing.com/widget/sign/pcStuSignController/preSign?activeId=126433134&classId=19047512&courseId=209403053
```

### 5、自定义上传照片
请保存你需要上传的照片为“123.jpeg”在当前目录下，如果当前目录下没有该文件，则会使用Daniel Wu的照片

  [1]: http://assets.z2blog.com/imgbed/2020/03/06/20200306880794.png
  [2]: https://www.z2blog.com//usr/uploads/2020/03/493803449.png
  [3]: http://assets.z2blog.com/imgbed/2020/03/06/20200306606197.png
  [4]: http://assets.z2blog.com/imgbed/2020/03/06/20200306740615.png
  [5]: https://ae01.alicdn.com/kf/U99b1d82401c14c2192b3ec5e81fbaa05F.png


