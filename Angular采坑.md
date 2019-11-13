1、项目依赖  尽量使用原始npm仓库安装，在windows下，尽量使用管理员cmd来操作
2、需要使用ngModel进行数据绑定的时候 需要在app.model的ngModule中引入FormControl

```javascript
   imports: [
      BrowserModule,
      AppRoutingModule,
      `FormsModule`
   ],
```

3、（）类似v-bind

