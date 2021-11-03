#### uniapp添加全局的组件

* 在main.js中引入组件

  > ```js
  > import service from './components/Service.vue';
  > ```

* 定义组件

  > ```js
  > let serviceVue = Vue.component('service', service)
  > ```

* 向文档中添加元素

  > ```js
  > document.body.appendChild(new serviceVue().$mount().$el)
  > ```

#### 引入crisp

```js
window.$crisp=[];
window.CRISP_WEBSITE_ID=data.crisp_website_id;
let d=document;
let s=d.createElement("script");
s.src="https://client.crisp.chat/l.js";
s.async=1;
let timer1 = setInterval(function(){
	(function(){
		if(d.getElementsByTagName("head").length > 0){
			clearInterval(timer1);
			if(d.getElementsByClassName('cc-unoo').length == 0){
				d.getElementsByTagName("head")[0].appendChild(s);
				let timer = setInterval(function(){
					let cc = d.getElementsByClassName('cc-unoo');
					if(cc.length == 1){ //下面时设置位置
                        cc[0].setAttribute("style", "bottom:90px !important;right:18px !important;");
                        cc[0].addEventListener('click', function(){
                            cc[0].setAttribute("style", "bottom:90px !important;right:18px !important;");
                        })
                        //这里是设置消息提示的位置,通过ID来添加css样式
                        d.getElementsByClassName('cc-1bue')[0].id = "cc-1bue2";
                        clearInterval(timer)
                    }
                },10)
            }
        }
    })();
}, 20)
```

