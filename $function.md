# Vue注册可以全局调用的方法，通过this.$XXX()调用

1. `plugins/uploadFile/index.js`

   ```javascript
   import Vue from 'vue';
   import UploadTool from '@/components/uploadTool';
   let uploadFileInstance = null;
   let init = () => {
     let uploadFileConstructor = Vue.extend(UploadTool);
     // 构造函数可以传参，data，method
     uploadFileInstance = new uploadFileConstructor({});
     uploadFileInstance.$mount();
     document.body.appendChild(uploadFileInstance.$el);
   }
   let caller = (options) => {
     if(!uploadFileInstance) {
       init(options);
     }
     // uploadTool.vue 中使用getParams接收调用时传入的参数。 type: image等
     uploadFileInstance.getParams(options);
   }
   export default {
     install(Vue) {
       Vue.prototype.$uploadTool = caller;
     }
   }
   ```

2. `/components/uploadTool.vue`

   ```javascript
   <template>
     <div>
     	<input type="file" ref="input" hidden multiple @change="onFileChange($event)" accept="*" />
     </div>
   </template>
   <script>
   	export default {
   		name: "UploadTool",
       components: {},
       componentName: "UploadTool",
       data() {
         return {
           callBack: null, // Function. Return data when upload success
         }
       },
       methods: {
         getParams(options) {
           this.callBack = options.callBack;
           this.chooseFile();
         },
         chooseFile() {
           this.$refs.input.click();
         },
         onFileChange(e) {
           if(e.target.files.length) {
             // you can deal with files here, like filter type, size...
             this.uploadFileToOss();
           }
         },
         uploadFileToOss() {
           // upload file to Oss here
           // when success, get file data from oss
           this.callBack(data);
         }
       }
   	}      
   </script>
   ```

   

3. XXX.vue 中使用

   ```js
   // js
   startChooseFile() {
     this.$uploadTool({
       msg: "upload", // uploadTool can get "upload" from options.msg
       callBack: (data) => {
         // get file info here
         // ...
       }
     })
   }
   ```

   

#### 这样一个简单的全局方法就注册完毕了，想要使用的时候可以直接 `this.$uploadTool()` 调用，不必在页面 `import` ，然后声明组件，适合常用的但是功能简单而且主要时候JS操作逻辑的功能。

