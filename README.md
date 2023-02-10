# react-router-chinese-tutorial
react-router 中文教程，基于 react-router 6.8.1 编写的教程

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
  errorElement: <ErrorPage />
}])
```
5. 再次访问 localhost:5137/home,会看到显示的是自定义的错误页面，并且在控制台打印出了错误信息
![image](https://user-images.githubusercontent.com/48917726/218024095-6848926c-412e-4527-8f48-1a874d622e96.png)

