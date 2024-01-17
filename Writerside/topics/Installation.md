# Installation

## Automatic Installation

使用`create-next-app`可以自动配置完所有事情，创建一个next项目


```Bash
npx create-next-app@latest
```


安装时的提示信息如下


```Bash
What is your project named? my-app
Would you like to use TypeScript? No / Yes
Would you like to use ESLint? No / Yes
Would you like to use Tailwind CSS? No / Yes
Would you like to use `src/` directory? No / Yes
Would you like to use App Router? (recommended) No / Yes
Would you like to customize the default import alias (@/*)? No / Yes
What import alias would you like configured? @/*
```


在提示之后，create-next-app将创建一个带有项目名称的文件夹，并安装所需的依赖项。

 > Good to know:  
 > Next.js现在默认带有TypeScript、ESLint和Tailwind CSS配置。  
 > 您可以选择在项目的根目录中使用src目录来将应用程序的代码与配置文件分开。


需要注意的是nodejs的版本，过低的版本会导致项目无法运行，在nodejs官网下载最新的LTS版本安装即可


