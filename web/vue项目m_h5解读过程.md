查看入口函数`main`所在的文件`main.js`

```javascript
import Vue from 'vue';
import { Lazyload } from 'vant';
// 移动端浏览器在派发点击事件时，会出现延迟的问题解决。为了立即响应用户点击事件而使用
import FastClick from 'fastclick';
// 移动端web开发在开发（测试）环境中使用的日志插件
import VConsole from 'vconsole';
// 日期插件，直接查看官网即可
import moment from 'moment';
import App from './App.vue';
import router from './router';
import store from './store';
import 'moment/locale/zh-cn';
// 默认的HTML元素样式上提供了跨浏览器的高度一致性
import '@/style/normalize.css';
// 全局样式修改：1.html的整体样式；2.设置body样式；3.设置其他统一样式
import '@/style/global.less';
// 重置第三方vant的统一样式
import '@/style/reset.less';

// 加载启动页图片
Vue.use(Lazyload, {
    lazyComponent: true,
    loading: require('./...'),
});

// Vue2.0写法，创建Vue对象
new Vue({
	router,
	store,
	render: (h) => h(App)
}).$mount('#app');

// 配置vConsole环境
if (['dev', 'test'].includes(process.env.VUE_APP_ENV) && process.env.NODE_ENV === 'production') {
	const vConsole = new VConsole();
	Vue.use(vConsole);
}

// fastclick的使用
FastClick.attach(document.body);
// 解决input的焦点很难获取的问题
FastClick.prototype.focus = (ele) => {
    ele.focus();
}

moment.locale('zh-cn');
// 关闭moment错误提示
moment.suppressDeprecationWarnings = true;

// 路由发生变化，通过全局前置守卫处理路由内容
/// 留个疑问？？？是否能讲该全局前置守卫放到router.js或者router/index.js中
router.beforeEach((to, from, next) => {
    /**
    to 和 from 都是路由对象，即 在router.js或router/index.js文件中
    const router = new VueRouter({
    	mode: 'history',
    	base: process.env.BASE_URL,
    	routes,
    });
    routes数组中的其中一个元素。
    */
    // 清除挂载在windows上的定时器
	clearTimeout(window.keye_1);
    clearTimeout(window.keye_2);
    // 修改title
    /**
    在路由对象中添加路由元信息meta
    meta: {
		keepAlive: true,
	}
	其中keepAlive用来标记是否保活当前路由对象。
	保活方式是在App.vue中为<router-view>外层嵌入<keep-alive>，详细介绍参考vue的
    */
    if (to.meta.title) {
        document.title = to.meta.title;
    }
    // 页面重置顶部
    if (from.meta.keepAlive) {
        const dom = document.documentElement || document.body;
        const scrollTop = dom ? dom.scrollTop : 0;
        from.meta.scrollTop = scrollTop;
    }
    if (!to.meta.keepAlive) {
        const dom = document.documentElement || document.body;
        // 实现一个定时器keye_1，挂载在windows上
        window.keye_1 = window.setTimeout(() => {
            dom.scrollTop = 0;
        }, 0);
    } else {
        const dom = document.documentElement || document.body;
		// 实现一个定时器keye_2，挂载在windows上
        window.keye_2 = setTimeout(() => {
            dom.scrollTop = to.meta.scrollTop;
        }, 0);
    }
    const keepAliveArr = ['home', 'orderList', 'approvalList', 'cardInfo', 'cardDetail', 'selectCountry', 'filghtFillOrder', 'hotelList', 'hotelDetail', 'hotelFillOrder'];
    if (keepAliveArr.includes(to.name)) {
        to.meta.keepAlive = true;
    }
    next();
});

export default {
    store,
    router,
};

```

[导航守卫链接](https://router.vuejs.org/zh/guide/advanced/navigation-guards.html)

[路由元meta信息链接](https://router.vuejs.org/zh/guide/advanced/meta.html)

查看`main.js`中包含的文件`import App from './App.vue';`

`<router-view v-if="$route.meta.keepAlive" />`取路由对象中的`meta`中的`keepAlive`值，来判定是否显示在标签`keep-alive`中。`meta`的内容在`router`或`index.js/router.js`中添加。

`<style lang="less"></style>`中未添加`scope`，指全局生效

```vue
<template>
    <div id="app">
        <keep-alive>
            <router-view v-if="$route.meta.keepAlive" />
    	</keep-alive>
        <router-view v-if="!$route.meta.keepAlive" />
        <TabBar />
    </div>
</template>

<script>
    import TabBar from '@/components/TabBar.vue';
    
    export default {
        name: 'app',
        components: {
            TabBar,
        },
    };
</script>

<style lang="less">
    #app {
        height: 100%;
    }
</style>
```



查看`main.js`中包含的文件`import router from './router';`

```javascript
import Vue from 'vue';
import VueRouter from 'vue-router';

const Home = () => import('../views/home/index.vue');
const FlightList = () => import('../views/flight/list.vue');

// 使用模块化机制编程，导入Vue和VueRouter，要调用 Vue.use(VueRouter)
Vue.use(VueRouter);

/**
定义route
@path		路由路径，路由访问 this.$router.push('/next/nextRouter') 或 
						    this.$router.push({ path: '/next/nextRouter' })
@name		路由名称，可以通过名称进行路由访问 this.$router.push({ name: 'nextRouter' })

@component	路由组件，其他文件中import过来，或者
			const Home = { template: '<div>Home<div>' }
*/

const routes = [
	{
        // 重定向'/'到'/home'路径下
        path: '/',
        redirect: '/home',
    },
    {
       	path: '/home',
        name: 'home',
        component: Home,
    },
    {
        path: '/flight/list/:index', // index航程序号: 1 单程/去程, 2 返程
        name: 'flightList',
        component: FlightList,
    },
    {
        // 嵌套路由，该路由作为样式展示在代码中
        /**
        	访问 bar 使用 '/foo/bar',
        	children中的子路由可根据需要进行选择，父路由的内容样式保持不变
        */
        path: '/foo',
        component: Foo,
        children: [
            {
                path: 'bar',
                component: Bar,            
            },
            {
                path: 'bal',
                component: Bal,
            }
        ]
    },
    ...
    // 捕获所有路由
   	{	
        // 会匹配所有路径，通常用于客户端 404 错误
        path: '*',
        // 重定向到'/'路径
        redirect: '/',
    }
];

const router = new VueRouter({
    // 设置vue-router的模式
	mode: 'history',
    base: process.env.BASE_URL,
    routes,
});
```

在注入路由器之后，可以在任何组件内通过`this.$router`访问路由器，也可以通过`this.$route`访问当前路由。在`this.$route`中访问参数时，使用`param`访问`url`中的参数，如

```javascript
export default {
    computed: {
		username() {
            return this.$route.params;
        }
    },
}
```

动态路由匹配，使用`:`标记，如上述`router.js`中`FlightList`路由所示

```javascript
    {
        path: '/flight/list/:index', // index航程序号: 1 单程/去程, 2 返程
        name: 'flightList',
        component: FlightList,
    },
```

当匹配到一个路由时，参数值会被设置到 `this.$route.params`，可以在每个组件内使用。在`FlightList`组件中使用：

```vue
<template>
	<div>
        <div>{{ $route.params.index }}</div>
    </div>    
</template>
<script>
    ...
    methods: {
    	func() {
            return this.$route.params.index;
        }  
    },
    ...
</script>
...
```

 `this.$route`还提供其他信息`$route.query` (如果 URL 中有查询参数)、`$route.hash` 等等，具体参考[API文档](https://router.vuejs.org/zh/api/#%E8%B7%AF%E7%94%B1%E5%AF%B9%E8%B1%A1)

HTML5 History 模式

`vue-router`默认是hash模式——使用`URL`的`hash`来模拟一个完整的`URL`，当`URL`改变时，页面不会重新加载。

默认`hash`模式下，访问路由的`url`端口后必须添加`#`，`http://localhost:8080/#/home`。

```javascript
const router = new VueRouter({
	mode: 'history',
    routes: [...],
});
```



