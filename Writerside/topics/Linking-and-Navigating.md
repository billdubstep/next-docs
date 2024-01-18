# Linking and Navigating

在Next.js中有三种导航路由的方法:  
- 使用[`<Link>组件`](#link-component)
- 使用[`useRouter钩子`](#userouter-hook)
- 使用本机[历史API](#using-the-native-history-api)  

本页将介绍如何使用这些选项，并深入了解导航的工作原理。

## `<Link>` Component

`<Link>`是一个内置组件，它扩展了HTML `<a>`标签，在路由之间提供预取和客户端导航。
这是Next.js中导航路由的主要和推荐的方式。  
你可以通过从`next/link`中导入它来使用它，并给组件传递一个`href` prop:

```Typescript
// app/page.tsx
import Link from 'next/link'
 
export default function Page() {
  return <Link href="/dashboard">Dashboard</Link>
}
```

还有其他可选的道具可以传递给`<Link>`。有关更多信息，请参阅[API参考](Link.md)。

### Examples

#### Linking to Dynamic Segments

当链接到[动态段](Defining-Routes.md)时，您可以使用
[模板字符串](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Template_literals)
和插值来生成链接列表。例如，要生成一个博客文章列表:

```Typescript
// app/blog/PostList.js
import Link from 'next/link'
 
export default function PostList({ posts }) {
  return (
    <ul>
      {posts.map((post) => (
        <li key={post.id}>
          <Link href={`/blog/${post.slug}`}>{post.title}</Link>
        </li>
      ))}
    </ul>
  )
}
```

#### Checking Active Links

可以使用[`usePathname()`](usePathname.md)来确定链接是否处于活动状态。例如，要向活动链接添加一个类，
你可以检查当前路径名(`pathname`)是否与链接的`href`匹配:

<tabs>

<tab title="TypeScript">

```Typescript
// app/components/links.tsx
'use client'
 
import { usePathname } from 'next/navigation'
import Link from 'next/link'
 
export function Links() {
  const pathname = usePathname()
 
  return (
    <nav>
      <ul>
        <li>
          <Link className={`link ${pathname === '/' ? 'active' : ''}`} href="/">
            Home
          </Link>
        </li>
        <li>
          <Link
            className={`link ${pathname === '/about' ? 'active' : ''}`}
            href="/about"
          >
            About
          </Link>
        </li>
      </ul>
    </nav>
  )
}
```

</tab>

<tab title="JavaScript">

```Javascript
// app/components/links.js
'use client'

import { usePathname } from 'next/navigation'
import Link from 'next/link'
 
export function Links() {
  const pathname = usePathname()
 
  return (
    <nav>
      <ul>
        <li>
          <Link className={`link ${pathname === '/' ? 'active' : ''}`} href="/">
            Home
          </Link>
        </li>
        <li>
          <Link
            className={`link ${pathname === '/about' ? 'active' : ''}`}
            href="/about"
          >
            About
          </Link>
        </li>
      </ul>
    </nav>
  )
}
```

</tab>

</tabs>


## `useRouter()` Hook

## Using the native History API

### 2. Prefetching


