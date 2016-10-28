---
layout: post
title:  "React-Native生命周期!"
date:   2016-10-28 14:10:53 +0800
description: "React-Native生命周期详解!"
tag: React-Native
---
一.React-Native生命周期

说到生命周期，大家大概也能想到就是创建、销毁、状态改变。RN的组件就是一个状态机。它接收两个输入参数：props和state，返回一个Virtual DOM。和Native一样，RN也为我们提供相应的钩子函数。RN的状态变化取决于props和state。我们先来看一张经典图。

这张图涵盖了一个组件从创建、运行到销毁的整个过程。大家可以看到，初始化的时候会调用5个函数（按先后顺序）。这5个函数在整个组件被创建到销毁的过程中只调用一次。初始化完毕后，当组件的props或者state改变都会触发不同的钩子函数，继而引发组件的重新渲染。现在我们把这过程拆开一点一点来分析。

初始化

我们先来看初始化，在初始化的过程中，会按顺序调用下面5个函数。

getDefaultProps：组件实例创建前调用，多个实例间共享引用。注意：如果父组件传递过来的Props和你在该函数中定义的Props的key一样，将会被覆盖。

getInitalState:组件示例创建的时候调用的第一个函数。主要用于初始化state。注意：为了在使用中不出现空值，建议初始化state的时候尽可能给每一个可能用到的值都赋一个初始值。

componentWillMount：在render前，getInitalState之后调用。仅调用一次，可以用于改变state操作。

render：组件渲染函数，会返回一个Virtual DOM，只允许返回一个最外层容器组件。render函数尽量保持纯净，只渲染组件，不修改状态，不执行副操作（比如计时器）。

componentDidMount:在render渲染之后，React会根据Virtual DOM来生成真实DOM，生成完毕后会调用该函数。在浏览器端（React），我们可以通过this.getDOMNode()来拿到相应的DOM节点。然而我们在RN中并用不到，在RN中主要在该函数中执行网络请求，定时器开启等相关操作

下面我们来演示getDefaultProps初始化Props以及父组件覆盖问题（AppConnect和Provider是和redux相关的代码，大家请跳过这一行）:

比如我们在这里定义了SimpleApp的默认Props为一个key为name,value为wsd的字典（ES6以后废除了getDefaultProps而使用上述方式），然后我们在它的父组件App中传入一个同样key为name的Props，然后我们在SimpleApp中使用this.props.name把props打印出来，如下：

可以看到，原先的wsd被后面传入的kingStart覆盖了。

然后我们来看初始化State的演示(ES6里使用constructor)：

我们初始化一个state为key为sex，value为boy的state对象，然后我们在componentWillMount函数中改变已经初始化的sex和没有声明的age，最后在render中打印：

可以看到我们在render中打印出了state中两个属性的值。在这里我们需要注意的是，如果在componentWillMount中直接修改state的值不会引发render的再次渲染。而如果把修改state的操作放到在render执行完之后的componentDidMount中，是会引发render的再次渲染的。

运行中

初始化完成之后，组件将会进入到运行中状态，运行中状态我们将会遇到如下几个函数：

componentWillReceiveProps(nextProps)：props改变（父容器来更改或是redux），将会调用该函数。新的props将会作为参数传递进来，老的props可以根据this.props来获取。我们可以在该函数中对state作一些处理。注意：在该函数中更新state不会引起二次渲染。

boolean shouldComponentUpdate(object nextProps, object nextState)：该函数传递过来两个参数，新的state和新的props。state和props的改变都会调到该函数。该函数主要对传递过来的nextProps和nextState作判断。如果返回true则重新渲染，如果返回false则不重新渲染。在某些特定条件下，我们可以根据传递过来的props和state来选择更新或者不更新，从而提高效率。

componentWillUpdate(object nextProps, object nextState)：与componentWillMount方法类似，组件上会接收到新的props或者state渲染之前，调用该方法。但是不可以在该方法中更新state和props。

render：跟初始化的时候功能一样。

componentDidUpdate(object prevProps,object prevState):和初始化时期的componentDidMount类似，在render之后，真实DOM生成之后调用该函数。传递过来的是当前的props和state。在该函数中同样可以使用this.getDOMNode()来拿到相应的DOM节点。如果你需要在运行中执行某些副操作，请在该函数中完成。

我们来演示componentWillReceiveProps的调用时机，对于顶层组件，我们添加一个文本及一个点击事件：

按钮点击以后，我们将自身state的name属性改变，并传递给SimpleApp（这里的AppConnect就是SimpleApp），结果如下：

我们可以看到，第一次render，打印的是defaultProps传过来的props。当按钮点击，顶层组件state改变，引发顶层组件重新渲染，父组件传递的name发生改变，componentWillReceiveProps被调用，继而引发二次渲染。在第二次render的时候，打印出来的就是新传递过来的props。

销毁

销毁阶段只有一个函数，很简单

componentWillUnmount：组件DOM中移除的时候调用。在这里进行一些相关的销毁操作，比如定时器，监听等等。

为了加深记忆，我们把初始化和运行中所有的钩子函数写出来，让大家看看最终的运行结果。

我们首先初始化组件，不执行任何操作，打印结果如图所示：

当我们点击按钮，改变组件的props之后，打印结果如下：

我们给自身组件添加了一个点击事件，点击之后改变自身的state，如下：

点击之后，再来看调用结果：

这也印证了我们的结论

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: http://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
