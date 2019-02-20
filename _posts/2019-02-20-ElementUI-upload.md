---
layout: post
title: "利用Element-UI的upload组件实现文件的上传以及样式变化"
subtitle: ""
date: 2019-01-14 17:00:00
author: "wantingtr"
tags:
    - 前端
    - ElementUI
---

最近在做项目的时候遇到在浏览器中上传视频的需求，使用Element-UI的upload组件。具体效果如下
![img](/img/post/upload.gif)
总的来说是很简单的，但一定要仔细对照官方文档，并且因为我使用了typescript，其中也遇到了一个坑。


### 对照了一下官方文档，所需要注意的部分：
1. 只能上传一个视频并且禁止多选 `:limit="1"`，`accept="video/*"`， `:multiple="false"`，
2. 当重复上传时弹出错误弹窗 `:on-exceed="exceed"`
3. 当有文件被选中时，样式会如图发生变化
4. 同时实现选择上传与拖拽上传
5. 手动上传，即用户选中上传按钮之后才进行上传`:auto-upload="false"`
6. 上传中后台返回进度，并利用`<el-progress>`显示实时进度（因为目前还没有后台数据所以这部分还是静态的）

具体代码如下：
```html
<div class="upload" v-show="!uploadTrue">
    <el-upload 
        ref="upload"
        drag
        action="https://jsonplaceholder.typicode.com/posts/"
        accept="video/*"
        :multiple="false"
        :limit="1"
        :on-remove="remove"
        :on-change="handleChange"
        :file-list="fileList"
        :on-exceed="exceed"
        :auto-upload="false"
        :on-progress="uploading">
        <img src="../../../static/imgs/upload_img_file.png" alt="" class="uploadImg">
        <p class="text1">拖拽文件至此区域</p>
        <p class="text2" id="text2">或</p>
        <p class="text3" id="text3">浏览本地文件</p>
    </el-upload>
</div>
```

```js
methods: {
    uploading() {
        //上传中
        console.log("uploading")
        if (this.uploadProgress == 100) {
            //上传完成
            this.uploadStatus = true
        }
    },
    handleChange() {
        //添加文件（样式变化）
        var el:any = document.getElementById("text2")
        var ele:any = document.getElementById("text3")
        if(this.fileList != null) {
            el.style.display = "none"
            ele.style.display = "none"
        }
    },
    remove() {
        //删除视频的钩子
        var el:any = document.getElementById("text2")
        var ele:any = document.getElementById("text3")
        el.style.display = "inline-block"
        ele.style.display = "inline-block"
    },
    exceed() {
        //重复选择文件,弹出错误弹窗
        this.$alert("最多仅支持上传一个视频文件")
    },
    uploadVideo() {
        //上传文件
        let element:any = this.$refs.upload
        element.submit();
        //
        //开始上传
        //服务端返回进度uploadProgress
        this.uploadTrue = true
    }
}
```

### 使用typescript中遇到的坑
根据官方文档，当使用手动上传时在`<el-upload> 中` 使用 `ref="upload"`，并在上传的函数中使用`this.$refs.upload.submit()`。但是，一直报错  
```
  Property 'submit' does not exist on type 'Vue | Element | Vue[] | Element[]'.
  Property 'submit' does not exist on type 'Vue'.
```
上网查了很久，发现是在typescript中无法识别vue中的$refs并且ts使用强类型，必须先使用any来进行声明
```js
    let element:any = this.$refs.upload
    element.submit();
```