# fileupload基于jquery的一个文件上传插件
1、这个插件的HTML使用很简单，直接用input的file类型就可以了，如下代码：

`<input id="fileupload" type="file" name="files" multiple>`

2、如果需要上传的进度条显示，则HTML代码如下所示：
`<div id="progress">
     <div class="bar" style="width: 0%;"></div></div>`
     
3、需要引入一下js文件，如下：
```javascript
<script src="js/jquery.min.js"></script>
<script src="js/fileUpload/vendor/jquery.ui.widget.js"></script>
<script src="js/fileUpload/jquery.iframe-transport.js"></script>
<script src="js/fileUpload/jquery.fileupload.js"></script>
```                  

4、js中初始化代码如下所示：
```javascript
$(function () {
        $('#fileupload').fileupload({
            forceIframeTransport: true,
            redirect: location.href.replace( /\/[^\/]*$/, '/cors/result.html?%s' ),
            url:'/upload/image.json',
            formData:{param1:"p1",param2:"p2"},
            acceptFileTypes: /(\.|\/)(gif|jpe?g|png)$/i,
            dataType: 'json',
            done: function (e, data) {
                //上传成功回调
                //done方法就是上传完毕的回调函数，其他回调函数可以自行查看api
                //注意result要和jquery的ajax的data参数区分，这个对象包含了整个请求信息
                //返回的数据在result.result中，假设我们服务器返回了一个json对象
                //console.log(JSON.stringify(result.result));
                var error_code = data.result.error_code;
                if(error_code == 1){
                    modalWarn(data.result.error_msg);
                }else{
                    var imgUrl = data.result.data.img_url;
                    $.each(data.files, function (index, file) {
                        $('#iptTxt').val(file.name).attr('imgurl',imgUrl);
                    });
                }
            },
            fail: function(e, data){
                console.log("上传失败："+data.errorThrown);
            },
            // 加载进度条
            progressall: function (e, data) {
                var progressBox =  $('#progress');
                var progress = parseInt(data.loaded / data.total * 100, 10);
                if(progress < 100){
                    progressBox.show().css(
                        'width',
                        progress + '%'
                    );
                }else{
                    progressBox.hide();
                }
            }
        });
    });
```
     
 >对属性进行理解 <br/>
    >1、forceIframeTransport: 指的是ie9下支持上传 <br/>
    >2、redirect:指的是使iframe跨域访问通过定向选择 <br/>
    >3、url: 指的是文件上传的地址 <br/>
    >4、formData: 指的是额外添加的参数 <br/>
    >5、acceptFileTypes: 指的是上传文件的类型 <br/>
    >6、dataType: 指的是请求的格式
  
    