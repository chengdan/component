#组件的通信

父-->子 （通过属性）

1.1 在子组件中声明props，是个数组，在数组中声明用来接收数据的属性的名称（必写）
1.2 在父组件中使用子组件的时候，通过在子组件标签中添加 之前声明好的属性，给属性赋值类向子组件传递数据
1.3 在子组件内，就可以像使用正常数据一样使用通过props声明从来的数据
这个数据传递过来之后，是单向绑定的，父组件中发生改变，子组件会相应的改变，但是子组件中不允许改变这个数据，就算能改变，父组件中也不会受到影响！
```javascript
Vue.component('mycomp',{
        template :'<div>{{msgfromfather}}</div>',
        data(){
            return{
                msg :'子组件里面的内容'
            }
        },
        props:['msgfromfather']
    })

    let vm = new Vue({
        el:'#app',
        data:{
            msg :'父组件里面的内容'
        }
    })
```


子-->父 （通过事件） 本质： 其实就间接的在子组件中调用父组件的方法

1.1 在父组件中声明方法
1.2 在父组件使用子组件的时候，通过事件绑定，将这个方法绑定给子组件 @
1.3 在子组件中，如果想要和父组件通信，只需要触发之前注册好的事件即可(this.$emit)
注意： $emit在触发事件的时候，是可以传递参数的，这个参数可以直接在父组件中的函数的形参中接收，但是要注意，只能接收一个参数，所以如果数据较多，则利用对象传递
```html
<div id="app">
    <span>{{msg}}</span>
    <mycomp @sendmsg="getMsgMySon"></mycomp>
</div>
```
```javascript
   Vue.component('mycomp',{
        template : '<div>{{msg}}<button @click="sendMsgToDad">点我发消息给爸爸</button></div>',
        data(){
            return{
                msg :'我是子组件内容'
            }
        },
        methods :{
            sendMsgToDad(){
                this.$emit('sendmsg','爸爸，我是儿子')
            }
        }
    })
    let vm = new Vue({
        el:'#app',
        data:{
            msg :'我是父组件'
        },
        methods : {
          getMsgMySon(msg){
              console.log(msg);
          }
        }
    })
```
兄弟组件

 通过一个全局的vue对象来通信 (是vuex一个简单实现！)

 原理： on emit

 1.1 在兄弟组件a中声明方法，

 1.2 在全局的vue对象中，注册事件，绑定兄弟组件a中的方法 $on

 1.3 如果兄弟组件b需要和兄弟组件a通信，那么只需要在兄弟组件b中触发全局vue对象中的事件 $emit
```javascript
var messageHub = new Vue();

    Vue.component('bigbrother',{
        template :'<h1>哥哥组件</h1>',
        created(){
            messageHub.$on('listenmsg',this.getMsg);
        },
        methods:{
            getMsg(msg){
                console.log(msg);
            }
        }
    });

    Vue.component('youngbrother',{
        template : '<div><h1>弟弟组件</h1><button @click="btn">发信息给哥哥组件</button></div>',
        methods :{
            btn(){
                messageHub.$emit('listenmsg','爸爸喊你回家吃饭了')
            }
        }
    });
    
    let vm = new Vue({
            el:'#app',
            data:{
    
            }
        })
```