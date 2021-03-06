> # Deploying a React Application

# 部署 React 应用

> Now it's time to get out into the world with your React application. There are many ways to deploy a React application to production, and many competing providers that offer this service. We'll keep it simple here by narrowing it down on one provider, after which you'll be equipped to check out other hosting providers on your own.

现在是时候让你的 React 应用走向世界了！有许多方法可以将 React 应用部署到生产环境中，也有许多提供这种服务的平台。在这里我们就简单的把它集中到一个平台上，之后你可以自己去看一看其他的托管平台。

> ## Build Process

## 构建过程

> So far, everything we've done has been the *development stage* of the application, when the development server handles everything: packaging all files to one application and serving it on localhost on your local machine. As a result, our code isn't available for anyone else.

到目前为止，我们做的所有事情都是在应用的*开发阶段*，这时服务器会处理所有的事情：将所有文件打包成一个应用，并且运行在本地机器的 localhost 上。结果是，我们的代码并不能给别人使用。

> The next step is to take your application to the *production stage* by hosting it on a remote server, called deployment, making it accessible for users of your application. Before an application can go public, it needs to be packaged as one essential application. Redundant code, testing code, and duplications are removed. There is also a process called minification at work which reduces the code size once more.

下一步是让你的应用进入*生产阶段*，将其托管到远程服务器上，这个步骤称为部署，这样可以让其他用户访问到你的应用。在一个程序可以向用户开放之前，它需要被打包成一个基本的应用程序，冗余的代码、测试代码和重复的代码会被删掉。在工作中还有一个叫做 minification 的过程，这个过程会再次减小代码的体积。

> Fortunately, optimizations and packaging, also called bundling, comes with the build tools in create-react-app. First, build your application on the command line:

幸运的是，在 create-react-app 的构建工具中自带了优化和打包的功能。首先在命令行中构建你的应用程序：

{title="Command Line",lang="text"}
~~~~~~~
npm run build
~~~~~~~

> This creates a new *build/* folder in your project with the bundled application. You could take this folder and deploy it on a hosting provider now, but we'll use a local server to mimic this process before engaging in the real thing. First, install an HTTP server on your machine:

这一步将在你的项目中创建一个新的 *build/* 文件夹，其中包含了打包的应用程序。你现在可以把这个文件夹部署在一个托管平台上，但我们在进行实际操作之前将使用一个本地服务器来模拟这个过程。首先，在你的机器上安装一个 HTTP 服务器：

{title="Command Line",lang="text"}
~~~~~~~
npm install -g http-server
~~~~~~~

> Next, serve your application with this local HTTP server:

接下来，用这个本地 HTTP 服务器来管理你的应用程序：

{title="Command Line",lang="text"}
~~~~~~~
http-server build/
~~~~~~~

> The process can also be done on demand with a single command:

这个过程也可以按需进行，只需要一个命令就可以完成。

{title="Command Line",lang="text"}
~~~~~~~
npx http-server build/
~~~~~~~

> After entering one of the commands, a URL is presented that provides access to your optimized, packaged and hosted application. It's sent through a local IP address that can be made available over your local network, meaning we're hosting the application on our local machine.

输入其中一个命令后，会出现一个 URL，通过这个 URL 可以让您可以访问打包优化后的应用。它是通过一个本地 IP 地址发送的，可以通过您的本地网络提供，这意味着我们在本地机器上托管了应用程序。