
# 一、Webform
`场景`:旧版本中使用，不做详细介绍
`用途`:
`用法`:
# 二、WebPages
- ## _PageStart
`场景`: 文件夹中所有页面需要统一布局文件，或者在运行页面之前检查用户登录等；
`用途`:能够在每个页面运行前运行```_pagestart```里的代码；（内部用RenderBody（）与Renderpage（）等）
`用法`:在需添加布局的页面所在文件夹中添加`_Pagestart.cshtml`即可。
# 三、Mvc5
- ## _ViewStart
`场景`:作为一个在呈现View文件的时候的启动文件，主要用于一些不方便或不能在母版（_Layout.cshtml）中进行的统一操作
 ```

 @{    Layout = "~/Views/Shared/_Layout.cshtml";}
 ```
`用途`:用于MVC 中view文件展现前的页面渲染等。
`用法`:在所需渲染的页面中增加该文件，即可对所有相同文件夹下的view页面生效（包括其子文件夹中的页面）；
若子文件夹中也含_ViewStart文件则该文件生效。
 也可在view 页面中单独引用
 ```

 @{    Layout = "~/Views/Shared/_MyLayout.cshtml";}
 ```
  则_viewstart文件不会生效
- ## MVC controller（ 直接传递指到对应的View ）
`场景`:在MVC中的Controllers文件夹中且命名均以Controller结尾；
`用途`:处理用户输入和相应的控制器类。MVC框架中将URL映射到控制器方法，控制器处理进入的请求、处理输入、保存数据、并把响应发送回客户端；
`用法`:在固定的Controllers文件夹中添加以Controller文件名结尾，以所对应的view文件的名称为前缀。之后在内部添加相应的方法即可；
#四、Webapi controller（通常通过Ajax调用）
##BaseControl`<T>`
 以下方法名称并没有太大限制，关键在   ```[HttpPut]```这些上标表示它的类型。
- ###Put
`场景`:页面需向后台传递用户输入的数据并保存时，如页面中录入数据时；
`用途`:将所传数据保存在对应的数据库中。
`用法`:Post等页面调用方法，传入数据。
 ```
  [HttpPut]

public override AjaxResult Put(string id, [FromBody] string value) => HandleHelper.TryAction(db =>
        {
            var model = db.LoadModalByJson<Dishes>(value, id);
            if (ObjectExtensions.IsNull(DynamicJson.Parse(value).ViewCategory))
                model.ViewCategory = null;
            BeforeAction(db, ControllerEvent.Put, model);
            db.Save(model);
            AfterAction(db, ControllerEvent.Put, model);
            return model;
        });    
 ```
- ### GetList

`场景`:PC端需要采用分页页面展示多条数据时；
`用途`:响应页面中api获取的方法，并根据参数过滤条件等返回数据；
`用法`:页面采用get方法调用对应的api即可，再根据回调函数来对数据进行相应的处理；
代码实例：（若未key，course等其他参数，则可直接调用系统自带的方法，无须重写。其他几个方法同样适用）
 ```c#
  [HttpGet]
        public AjaxResult GetList(int pageIndex, int pageSize, string key, string course)
        {
            return HandleHelper.TryAction(db =>
            {
                var clip = MakeWhere(key);
                if (course.IsNotNull()) clip &= Clip.Where<StudentScore>(x => x.Course.Name.Like(course));
                return db.GetPageList<StudentScore>(pageIndex, pageSize, clip, OrderBy);
            });         }  

  ```


`get方法介绍`
$.get(api路径,参数（可不填）, 回调函数)；
代码实例：
```
    $.get("/api/ExchangeGoods/GetList", function (result) {
                console.log(result.Data);
                if (result.IsSuccess) {
                    $("#GoodsList").Link("#goodsListTmpl", result.Data);
                }             }); 
``` 
- ### Get

`场景`:View页面需要从后台读取单条数据时；
`用途`:根据ID获取单条数据；
`用法`: 同GetList；
-  ###Delete
`场景`:页面根据请求删除某些数据时；
`用途`:在数据库中删除指定数据；
`用法`:用于类似put
代码实例：
 ```
[HttpDelete]
 public AjaxResult Delete([FromBody] string id)
        {
            return HandleHelper.TryAction(db =>
            {
                var model = db.GetModelById<AgencyPipe>(id);
                if (model == null || model.IsNew()) throw new SailCommonException("该数据已被删除");
                var pipe = db.GetModelById<Pipe>(model.PipeId);
                db.DeleteById<AgencyPipe>(id);
                return pipe;
            });         }  

 ```
- ### 其他扩展

`场景`:
`用途`:
`用法`:
