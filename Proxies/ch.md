# Proxies
## 目录
- [Proxy](#Proxy)
- [Proxy.revocable](#Proxy.revocable)
## Proxy
Proxy 译为代理，它可以代理对象属性被访问时的行为。
``` javaScript
var target = {}
var handler = {} 

var proxy = new Proxy(target, handler)
```
我们通过构造函数 Proxy 生成一个对象的代理，Proxy 接受两个参数，第一个参数是 **被代理的对象**。第二个参数是 **被代理的行为**。

上面的代码我们的 handler 只是设置了一个空对象，这时 proxy 只是简单的返回操作结果。
``` javaScript
var target = {}
var handler = {} 

var proxy = new Proxy(target, handler)

proxy.foo = 1
console.log(target.foo)
// 1

console.log(proxy.bar)
// undefined
```
一般来说，我们可以代理对象的 **set** 和 **get**。
``` javaScript
var target = {}

var handler = {
    set(target, key, value) {
        console.log('I try to set property', key)
        return true
    },
    get(target, key) {
        console.log('I got', key)
    }
}

var proxy = new Proxy(target, handler)

proxy.a = 1
// I try to set property a
console.log(proxy.a)
// I got a
// 1
```
代理 **set** 的时候，**return true** 表示设置成功。 **return false** 表示设置失败，此时在严格模式中，会抛出错误，正常模式中则是默认设置失败，不会抛出错误。

值得注意的是，被代理的行为只能是通过 **代理对象** 访问才有效，直接在 target 对象上读取属性代理行为是无效的，所以有时候我们需要隐藏 target 对象。
``` javaScript
function getProxy (){
    var target = {}
    var handler = {
        set (target, key, value){
            console.log('set')
            return true
        },
        get (target, key){
            console.log('get')
            return target[key]
        }
    }
    return new Proxy(target, handler)
}

var proxy = getProxy()
```
上述代码通过一个函数生成 proxy 成功隐藏了 target 对象。
## Proxy.revocable
Proxy.revocable 与 Proxy 很相似，它返回两个对象 **proxy** 与 **revoke**。

其中 **proxy** 就是上述的代理实例，**revoke** 是一个方法，它可以撤销所有代理。
``` javaScript
var target = {}
var handler = {}
var {proxy, revoke} = Proxy.revocable(target, handler)

proxy.a = 'b'
console.log(proxy.a)
//  'b'

revoke()
revoke()
revoke()
console.log(proxy.a)
//  TypeError: illegal operation attempted on a revoked proxy
```