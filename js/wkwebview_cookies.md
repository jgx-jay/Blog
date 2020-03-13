### **背景: 在 iOS9/10 系统中,由于官方原因没办法把同步给 wkwebview cookies, 导致网页内所请求没有携带 cookies**

## **解决方案:**

1. ***首次加载网页时, 在请求 headr 中设置 cookies, 保证首次加载会携带当前域名的 cookies***
2. ***加载后  JS 脚本注入到 WKWebview 中***
3. ***拦截 response set-cookie 手动更新保存以便下一次注入***

## **存在问题：**

### **问题一:**

#### `不支持跨域请求cookies 写入`

**比如说, 如果在 A 页面中访问 B 页面, 我们再 A 页面脚本注入时, 是没有办法跨域名的写入 cookies, 这就导致访问 B 页面的时候, 是没有携带任何 cookies.**

**如果需要类似登录态的值, 可以考虑在 url 后面拼接参数.类似 ?token=123123**

### **问题二:**

#### `由于写 cookies 时机是在页面加载时, 所以当前访问页面的请求时还没有写入`

### **native 端可以在加载请求页面时, 将所需 cookies 写入请求 headr 中, 保证第一次请求是携带 cookie, 后续的 cookie 由脚本注入后提供**

### **问题三:**

#### `wkwebview 没有将 cookies 同步给目前在使用的NSHttpCookieStorage中, 导致后续再注入 cookie 时不会携带后端下发的最新 cookie, 而且会覆盖`

### **经过测试, iOS 10 下 WKWebview 会自动向 NSHttpCookieStorage 同步. iOS 9 由于没有测试机 其情况待测试, 推断和 iOS10 相同**



## **Q&A**

#### **Q: 那拦截所有的请求并放到 headr 里面请求不就好了么, 这样就不用注入写 cookie 的脚本了**

#### **A:**  后端和网页都会修改 cookie, 如果要保证所有请求都携带最新的 cookie, 需要及时给客户端同步 cookie 才行, 现在来看wkwebview还是没有办法主动给NSHttpCookie同步, 只能靠自动同步, 但是时机有可能就错过了