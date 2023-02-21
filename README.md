# react-router-chinese-tutorial

react-router 中文教程，基于 react-router 6.8.1

## 开始

1. 搭建一个 vite + react 的项目

`npm create vite@latest name-of-your-project -- --template react`

2. 打开 name-of-your-project 文件夹，引入 react-router 依赖

`npm install react-router-dom localforage match-sorter sort-by`

3. 运行项目

`npm run dev`

4. 项目搭建运行成功，访问 localhost:5137

![image](https://user-images.githubusercontent.com/48917726/217995568-e66484a0-b3d9-46ab-bdf1-54579c25138c.png)

## 创建一个 router

1. 在 src/main.jsx 中引入 react-router

```javascript
import { createBrowserRouter, RouterProvider } from 'react-router-dom'
```

2. 创建一个 router，这里的 `element` 可以是 jsx ，也可以是 react 组件

```javascript
const router = createBrowserRouter([{
  path: '/',
  element: <div>hello world</div>
}])
```

3. 渲染 router
```javascript
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>
```
4. 访问 localhost:5137

![image](https://user-images.githubusercontent.com/48917726/217997109-57f19903-015d-47ff-83c5-abc512795ddb.png)

5. react-router 也提供了通过 jsx 创建路由的方法，这两种写法只有风格上的区别，用你喜欢就好了

```javascript
import { createBrowserRouter, RouterProvider, createRoutesFromElements, Route } from 'react-router-dom'

const router = createBrowserRouter(
  createRoutesFromElements(
    <Route path='/' element={<div>hello world</div>}></Route>
  )
)
```

## 错误处理

默认情况下当你的页面在渲染、加载数据或改变数据时抛出异常，react-router 都会捕获这个异常并渲染一个错误页面。比如说访问 localhost:5137/home，这个路由在 `router` 里匹配不到，所以会显示一个默认的 404 页面

![image](https://user-images.githubusercontent.com/48917726/218019340-24d61f46-a36d-4cb6-9354-d6ffedaced93.png)

### 自定义错误页面

1. 在 src 目录下新建一个 error-page.jsx

2. 引入路由错误 hook
 
```javascript
import { useRouteError } from "react-router-dom";
```

3. 导出组件

```javascript
export default function ErrorPage() {
  const error = useRouteError();
  console.error(error);

  return (
    <div id="error-page">
      <h1>Oops!</h1>
      <p>Sorry, an unexpected error has occurred.</p>
      <p>
        <i>{error.statusText || error.message}</i>
      </p>
    </div>
  );
}
```

3. 在 main.jsx 中引入组件

```javascript
import ErrorPage from "./error-page";
```

4. 在 react-router 中使用

```javascript
const router = createBrowserRouter([{
  path: '/',
  element: <App />,
  // 在这里
  errorElement: <ErrorPage />
}])
```

5. 再次访问 localhost:5137/home,会看到显示的是自定义的错误页面，并且在控制台打印出了错误信息

![image](https://user-images.githubusercontent.com/48917726/218024095-6848926c-412e-4527-8f48-1a874d622e96.png)

## 动态路由

假设有一个 userInfo 页面，需要访问 user-info/userId （userId 是不确定的）来显示它，那么我们可以这么写

```javascript
{
  // 冒号具有特殊含义
  // 冒号后边的片段叫动态段（dynamic segment）
  // 动态段将匹配任意动态值
  path: "user-info/:userId",
  element: <UserInfo />,
}
```

现在不管是访问 user-info/1，还是 user-info/2，他们都会导航到 userInfo 页面

### 获取URL参数

动态段匹配到的动态值叫做 URL参数 或 参数，这些参数通过与动态段匹配的键传递给加载器（loader）。

1. 在 userInfo 页面编写并导出 loader

```javascript
export function loader({ params }) {
    const { userId } = params;

    return { userId };
}
```

2. 在路由中使用它

```javascript
import UserInfo, { loader } from "./routes/userInfo";

const router = createBrowserRouter([{
  path: 'user-info/:userId',
  element: <UserInfo />,
  loader,
}])
```

3. 在 userInfo 页面调用 useLoaderData hook

```javascript
import { useLoaderData } from "react-router-dom";

export default function UserInfo() {
  const { userId } = useLoaderData()

  return <p>{userId}</p>
}
```

## 嵌套路由

假设有一个 box 页面，主体是不确定的，头部底部是确定的元素，那么我们就需要用到嵌套路由

1. 首先在 box 页面引入 `<outlet>`

```javascript
import { Outlet } from "react-router-dom";
```

2. 将 `<Outlet />` 放到你需要渲染的位置

```javascript
export default function Box() {
    return <>
        <header>头部</header>
        <main>
            <Outlet />
        </main>
        <footer>底部</footer>
    </>
}
```

3. 把需要嵌套的路由放到目标路由的 children 中，现在访问 /home 或者 /other，Home 或 Other 组件都会渲染到 Box 组件的 `<main>` 元素中

```javascript
{
  path: '/',
  element: <Box />,
  children: [{
    path: "home",
    element: <Home />,
  },{
    path: "other",
    element: <Other />,
  }]
}
```

### 索引路由

按照上面的路由，当你访问 '/' 时，因为没有匹配的子路由 `<Outlet>` 部分会是一片空白，为此你可以设置一个索引路由（index route）来当作默认子路由

```javascript
{
  path: '/',
  element: <Box />,
  children: [{
    // 注意是 index，而不是 path
    // 它会告诉路由，当用户访问父路由的确切地址时，匹配并渲染 <Index> 组件
    index: true,
    element: <Index />
  },{
    path: "home",
    element: <Home />,
  },{
    path: "other",
    element: <Other />,
  }]
}
```

## 客户端路由

如果你用过 vue-router，那么你就会知道，在 vue-router 中导航到一个路由需要用到 `<router-link>`，那么在 react-router 中有没有类似的组件呢？答案是肯定的，在 react-router 中这个组件是 `<link>`，和 `<outlet>` 一样，如果你要使用 `<link>`，那么你得先引入它

 ```javascript
 import { Link } from "react-router-dom";
 ```
 
 用法和 `<router-link>` 一样
 
 ```javascript
 <Link to="/home">home</Link>
 ```
 
 `<a>` 标签虽然同样也可以完成路由的导航，但是同时也会发送一个完整的文档请求，而客户端路由则没有这个问题
 
 ![image](https://user-images.githubusercontent.com/48917726/218052552-be1a31ac-0945-476c-b392-b90b79e53620.png)
 
 ## 编程式导航
 
 useNavigate hook 返回一个用于函数式导航的函数
 
 ```javascript
 const navigate = useNavigate()
 
 // 可以传一个整数
 // 表示在历史堆栈中前进或后退多少步
 navigate(-1)
 
 // 可以传一个 To 值，类似于 <Link to>
 // 第二个参数可以传一个 option，包括 {replace,state,relative}
 navigate('/home',{replace: true})
 ```
 
 ## 加载数据
 
1. 编写一个 loader,loader 是在进入路由时立即触发的函数，可以返回任意数据，然后通过 useLoaderData 可以获取到 loader 返回的数据
 
 ```javascript
export function loader() {
    return { contacts: [] };
}
```

2. 在路由中使用它

```javascript
import Root, { loader } from "./routes/root";

const router = createBrowserRouter([{
  path: '/',
  element: <Root />,
  // 在这里使用它
  loader: loader,
  // 子路由无法访问祖先路由的 loader
  // 如果你想让子路由访问祖先路由的 loader
  // 那么你也得在子路由里使用想要访问的 loader
  children: [{
    path: "contacts/:contactId",
    element: <Contact />,
  }]
}])
```

3. 在组件中使用它，需要先引用 useLoaderData hook，useLoaderData 会返回 loader return 的数据，并且 react-router 会保持数据和 UI 同步

```javascript
import { useLoaderData, Link } from "react-router-dom";

export default function Root() {
    const { contacts } = useLoaderData();

    return (
        <>
            <nav>
                {contacts.length ? (
                    <ul>
                        {contacts.map((contact) => (
                            <li key={contact.id}>
                                <Link to={`contacts/${contact.id}`}>
                                    {contact.first || contact.last ? (
                                        <>
                                            {contact.first} {contact.last}
                                        </>
                                    ) : (
                                        <i>No Name</i>
                                    )}{" "}
                                    {contact.favorite && <span>★</span>}
                                </Link>
                            </li>
                        ))}
                    </ul>
                ) : (
                    <p>
                        <i>No contacts</i>
                    </p>
                )}
            </nav>
        </>
    );
}
```

4. 也可以在 loader 里返回一个 web Response，用来导航到其他路由页面

```javascript
import { redirect } from "react-router-dom";

export function loader() {
    // 返回一个 web Response，用于重定向到 home 页面
    return redirect('/home');
}
```

## 活动链接样式

如果一个 `<Link>` 匹配的路由和当前页面路由匹配的话，我们称之为活动链接（active link）

现在我们要给活动链接设置特殊样式，如果用 `<Link>` 组件来做的话，就需要手写一些逻辑判断

而 react-router 内置了一个 `<NavLink>`，专门用于处理这种情况

```javascript
import {NavLink} from "react-router-dom";

export default function TabBar() {
  const tabs = [
    {
      name: "首页",
      path: "/home"
    },
    {
      name: "个人中心",
      path: "/personal-center"
    }
  ]

  return tabs.map(item=>(<NavLink
    key={item.path}
    to={item.path}
    // react-router 通过 className 函数返回 isActive 和 isPending 状态
    // 当用户将要进入当前路由，数据还在加载（等待 loader return）时，isPending 为true
    // 当用户已经进入当前路由，isActive 为true
    className={({ isActive, isPending }) => isActive ? 'active' : isPending ? 'pending' : ''}
  >
    {item.name}
  </NavLink>))
}
```

## Form 和 action

react-router 有一个自带的 `<Form>` 组件，其行为和 html 原生表单 `<form>` 类似———如果没有 js 逻辑，在点击提交按钮时，表单组件会创建一个 FormData 发送给服务器

```javascript
import { Form } from "react-router-dom";

export default function MyForm() {
  return <Form method="post">
    // 这里可以输入一个名为 test 的字段
    <input type="text" name="test"/>
    <button type="submit">submit</button>
  </Form>
}
```

而 react-router 会拦截这个请求，如果 method 是 get 则会将请求转发到 loader 中，如果是 post 则转发到 action 中

首先在 `<Form>` 所在页面定义并导出一个 action

```javascript
import { Form, redirect } from "react-router-dom";

// action 必须返回值
// 返回值可以是一个 web Response，也可以是其他任何东西
// 可以通过 useActionData 获取 action 的返回值
// request 里包含了请求相关的信息
// request.formData() 返回一个 Promise
// params 是 URL参数
export async function action({ request, params }) {
    const formData = await request.formData()
    // 这是刚刚输入的 test 字段
    console.log(formData.get('test'))
    
    // 可以用 Object.fromEntries 来将 formData 转成一个普通对象
    const temp = Object.fromEntries(formData)
    console.log(temp.test)

    // 这里返回一个 web Response，用于重定向到 userInfo 页面
    return redirect(`/userInfo/${params.userId}`)
}
```

然后在 router 中引入它

```javascript
import Root, { loader as rootLoader, action as rootAction } from "./routes/root";

[{
  path: '/',
  element: <Root />,
  loader: rootLoader,
  // 在这里
  action: rootAction,
  children: [{
    path: "userInfo/:userId",
    element: <UserInfo />,
  }]
}]
```

## 获取导航状态

通过 useNavigation hook 可以获取当前导航信息

通过它我们可以实现全局加载样式统一管理，比如在加载时给页面显示一个进度条什么的

### navigation.state

可能的值有:

1. 'idle' 空闲状态
2. 'submitting' 正在执行当前路由的 action 方法
3. 'loading' 正在执行当前路由的 loader 方法

### navigation.formData

当通过 `<Form method>` 为 POST、PUT、PATCH、DELETE 或用 useSubmit 提交表单时，formData 会是一个 FormData 对象，里边保存了提交的表单数据

当通过 GET 方法提交表单的情况时，formData 将为空，这时表单数据将返回在 navigation.location.search 中

### navigation.location

location 里包含了接下来将要进入的下一个导航的信息
