# Pages and Layouts

>我们建议在继续之前阅读[路由基础](Route.md)和[定义路由](Defining-Routes.md)页面。

Next.js 13中的应用路由器引入了新的文件约定，以方便地创建[页面](#page)、[共享布局](#layouts)和[模板](#templates)。
本页将指导您如何在Next.js应用程序中使用这些特殊文件。

## page
页面是路由**唯一**的UI。你可以通过从`page.js`文件中导出组件来定义页面。使用嵌套文件夹[定义路由](Defining-Routes.md)，
并使用`page.js`文件使该路由可公开访问。  
通过在`app`目录中添加`page.js`文件来创建你的第一个页面: 

![image_6.png](image_6.png)

```Typescript
// app/page.tsx
// app/page.tsx is the UI for the `/` URL
export default function Page() {
    return <h1>Hello, Home page!</h1>
}
```

```Typescript
// appapp/dashboard/page.tsx
// `app/dashboard/page.tsx` is the UI for the `/dashboard` URL
export default function Page() {
    return <h1>Hello, Dashboard Page!</h1>
}
```

>Good to know:  
>- 页面总是路由子树的叶子。  
>- .js、.jsx或.tsx文件扩展名可用于Pages。  
>- 需要一个page.js文件来让一个路由段可以公开访问。  
>- 页面默认是服务器组件，但可以设置为客户端组件。  
>- 页面可以获取数据。有关更多信息，请查看数据抓取部分。

## Layouts
布局是在多个页面之间共享的UI。在导航上，布局保持状态，保持交互，不重新呈现。
布局也可以嵌套([nested](Pages-and-Layouts.md#nesting-layouts))。  
默认情况下，你可以定义一个布局，从`layout.js`文件导出一个React组件。组件应该接受一个子属性，在呈现过程中，
这个子属性将被一个子布局(如果存在的话)或一个子页面填充。

![image_7.png](image_7.png)

```Typescript
export default function DashboardLayout({
  children, // will be a page or nested layout
}: {
  children: React.ReactNode
}) {
  return (
    <section>
      {/* Include shared UI here e.g. a header or sidebar */}
      <nav></nav>
 
      {children}
    </section>
  )
}
```

>Good to know:  
>- 最顶层的布局称为[根布局](Pages-and-Layouts.md#root-layout-required)。
这个所需的布局在应用程序中的所有页面之间共享。根布局必须包含html和body标签。  
>- 任何路由段都可以选择定义自己的[布局](Pages-and-Layouts.md#nesting-layouts)。这些布局将在该段的所有页面上共享。  
>- 默认情况下，路由中的布局是嵌套的。每个父布局使用React `children`属性将子布局封装在它的下面。  
>- 你可以使用路由组来选择共享布局中的特定路由段。  
>- 默认情况下，布局是[服务器组件](Server-Components.md)，但可以设置为[客户端组件](Client-Components.md)。  
>- 布局可以获取数据。有关更多信息，请查看[数据抓取](Data-Fetching.md)部分。  
>- 不能在父布局和子布局之间传递数据。然而，你可以在一个路由中多次获取相同的数据，
React会[自动删除请求](Caching-in-Next-js.md#request-memoization)，而不会影响性能。  
>- 布局不能访问它下面的路由段。要访问所有路由段，你可以在客户端组件中使用
[`useSelectedLayoutSegment`](useSelectedLayoutSegment.md)或[`useSelectedLayoutSegments`](useSelectedLayoutSegments.md)。  
>- `.js`， `.jsx`或`.tsx`文件扩展名可用于布局。
>- `layout.js`和`page.js`文件可以在同一个文件夹中定义。布局将包裹页面。

### Root Layout (Required)

根布局定义在app目录的顶层，适用于所有路由。这种布局使您能够修改从服务器返回的初始HTML。

```Typescript
export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  )
}
```
>Good to know:
>- app目录必须包含一个根目录。  
>- 根布局必须定义`<html>`和`<body>`标签，因为Next.js不会自动创建它们。  
>- 您可以使用[内置的SEO支持](Metadata.md)来管理`<head>`HTML元素，例如`<title>`元素。  
>- 您可以使用[路由组](Route-Groups.md)来创建多个根布局。请看下面的[例子](Route-Groups.md#creating-multiple-root-layouts)。  
>- 默认情况下，根布局是一个服务器组件，**不能**设置为[客户端组件](Client-Components.md)。  

### Nesting Layouts

在文件夹中定义的布局(例如`app/dashboard/layout.js`)应用于特定的路由段(例如`acme.com/dashboard`)，
并在这些段处于活动状态时渲染。默认情况下，文件层次结构中的布局是嵌套的，这意味着它们通过其子属性包装子布局。

![image_8.png](image_8.png)

```Typescript
export default function DashboardLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return <section>{children}</section>
}
```

>Good to know:
>- 只有根布局可以包含`<html>`和`<body>`标签。 

如果你要把上面的两个布局结合起来，根布局(`app/layout.js`)会包裹仪表板布局(`app/dashboard/layout.js`)，
它会包裹在`app/dashboard/*`中的路由段。  
这两个布局将像这样嵌套:

![image_5.png](image_5.png)

你可以使用[路由组](Route-Groups.md)来选择共享布局中的特定路由段。  

## Templates

模板类似于布局，因为它们包装每个子布局或页面。与跨路由持久保存和维护状态的布局不同，模板在导航上为它们的每个子实例创建一个新实例。
这意味着当用户在共享模板的路由之间导航时，组件的新实例被挂载，DOM元素被重新创建，状态**不被**保留，效果被重新同步。

在某些情况下，您可能需要这些特定的行为，而模板将是比布局更合适的选择。例如:
- 依赖于`useEffect`(例如记录页面浏览量)和`useState`(例如每页反馈表单)的特性。
- 更改默认的框架行为。例如，布局中的悬念边界仅在布局第一次加载时显示回退，而在切换页面时则不显示。对于模板，回退显示在每个导航栏上。  

模板可以通过从`template.js`文件中导出一个默认的React组件来定义。组件应该接受一个子属性。

![image_9.png](image_9.png)

```Typescript
export default function Template({ children }: { children: React.ReactNode }) {
  return <div>{children}</div>
}
```

在嵌套方面，`template.js`是在布局和它的子布局之间呈现的。下面是一个简化的输出:

```Typescript
<Layout>
  {/* Note that the template is given a unique key. */}
  <Template key={routeParam}>{children}</Template>
</Layout>
```

## Modifying `<head>`

在app目录中，你可以使用[内置的SEO支持](Metadata.md)来修改`<head>` HTML元素，比如`title`和`meta`。  
`matedata`可以通过在[`layout.js`](layout-js.md)或[`page.js`](page-js.md)文件中
导出[`matedata对象`](generateMetadata.md#the-metadata-object)或
[`generateMetadata`](generateMetadata.md#generatemetadata-function)函数来定义。

```Typescript
import { Metadata } from 'next'
 
export const metadata: Metadata = {
  title: 'Next.js',
}
 
export default function Page() {
  return '...'
}
```

>Good to know: 你不应该手动添加`<head>`标签，如`<title>`和`<meta>`到根布局。
相反，您应该使用[元数据API](generateMetadata.md)，它可以自动处理高级需求，例如流式传输和重复删除`<head>`元素。