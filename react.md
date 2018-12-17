# React Docs
## MAIN CONCEPTS
### Rendering Elements
+ react中元素：
   首先react中元素是一个纯对象，必须经过React Dom 渲染成Dom ，所以对于原生的Dom Api ，react 元素都不适用。一般来说，react中的元素是通过JSX语法给出，其实react中可以不需要JSX语法，直接用React.createElement(tag,props,children);并且react元素是一个immutable，即只读，不可以修改其属性和孩子。
   ```
    const element=<h1>react</h1>;
    console.log(element); //输出一个对象
    element.props.children='jsx'; //报错，不能修改一个只读对象的属性
   ```
### Props and Component
* react中组件，最简单的方式就是一个大写函数名，接受一个参数props，并且返回react元素。
  注意，props也是只读，不能修改的。组件还可以用class的形式；
```
  ReactDOM.render(<Welcome content='name'/>,document.getElementById('root'));
  //首先调用Welcome，并且将{content：'name'}传入进去，然后返回react元素，然后将react元素渲染成Dom；
```
* 'pure' function:不改变输入，相同的输入总是返回相同的输出；
* react的原则：
>All React components must act like pure functions with respect to their props.
### State and Lifecycle
> Thanks to the setState() call, React knows the state has changed, and calls the render() method again to learn what should be on the scree
### Handling Events
react元素和Dom上的事件处理之间差异：
* 首先react元素上事件是用驼峰形式及onClick，而dom上是小写，即onclick
* 使用jsx事件处理传入的是函数，而html标签传入的是字符串，即处理字符串里面的脚本
* html标签中event handler可以返回false来阻止默认行为，而jsx中必须要调用event.preventDefault()
```
  <a href='http://baidu.com' onclick='console.log(11);return false'>点击</a>
```
```
  class ActionLink extends Component{
    constructor(props){
      super(props);

    }
    render(){
      return(
        <a href={this.props.url} onClick={(e)=>this.clickHandler(e)}>
          {this.props.name}
        </a>
      )
  }
  clickHandler(e){
    console.log('react');
    e.preventDefault();
}
}

//注意这里onClick里面是一个函数声明或者一个函数引用，点击事件发生时会调用函数，并且自动自动往函数里面传递一个event；这样用箭头函数虽然不会导致this的失去绑定，但是每一次渲染元素的时候都会，创造一个新的箭头函数，也就是说，每一次对象都不一样，{}==={}//false
所以如果把回调函数当做props的一个属性传给子组件的时候，会出现一些问题，所以最好还是在constructor里面用上this.clickHandler=this.clickHandler.bind(this);
//或者onClick={this.clickHandler.bind(this)}不能写成this.clickHandler,因为this.clickHandler是那个函数的引用，当点击事件发生时，去调用这个函数的时候，函数里面的this失去了绑定。
```
当使用react的时候，通常没有必要在一个react元素渲染成Dom之后，在其上添加addEventListener,而是在react元素初始化的时候就提供一个listener；
向回调函数里面传入参数：
```
  <a onClick={(e)=>this.clickHandler(id,e)}></a>
  <a onClick={this.clickHandler.bind(this,id)}></a>
  //在以上连个情况下，事件event都作为第二个参数传递，但是箭头函数中必须要明确指出，但是bind不需要；
```

### Conditional Rendering
在react中，可以创建不同的封装了所需行为的组件，并且可以根据应用的状态有选择的渲染其中一部分组件；
```
  //犯了一个错误
  在组件函数Greeting内部
  function Greeting（）{
    return isLoggedIn? <UserGreeting /> : <GuestGreeting />;
    //错写成isLoggedIn? return <UserGreeting />: return <GuestGreeting />;
    //三目运算符？：中间必须有值，所以当用return时候没有值，会报错；
}

```
```
  react中很智障的报错：
  function Greeting(props){
    return <p>props.name<p>//没有</p>
}
  ReactDOM.render(<Greeting name='react'/>,document.getElementById('root'));//在此行报错Unterminated JSX contents
  //如果不修改组件Greeting中的错误，
  //只是把ReactDOM.render(<div></div>,document.getElementById('root'))去调试，依然
  //会出现错误，必须将Greeting注释或者修改好
```
* 可以用&&来代替if，但是要注意，前一项为false的情况，只返回false；
* 用条件表达式来代替if else （condition ? true : false）
* 当想让一个组件渲染之后，不显示，可以返回一个null作为它的输出，但是这并不影响其生命周期函数;
### List and Key
在react中也是用map将一个数组转变成一个react元素列表，可以先建立元素集合，然后在JSX中用大括号把它们括起来；
```
    const members=[1,2,3,4];
    const listItems=members.map((item)=><li>{item}</li>);
    ReactDOM.render(<ul>{listItems}</ul>,ducoment.getElementById('root'));
```
当创建一个元素列表时，key是一个很重要的属性。key帮助react辨别哪一个列表元素发生改变，天骄，或者移除等，使用key来给每一个元素一个明确的稳定身份。挑选key的最好方式是使用一个能够唯一区别列表元素和其兄弟元素的字符串，通常情况下可以使用IDs。退而求其次，可以使用index作为key。（当元素的顺序可能发生改变，事实上并不推荐使用元素的index作为key值，这会对性能产生负面影响，可能会对组件state产生问题。如果没有对key属性赋明确值，react会默认index作为key值。[index作为key的影响](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318)tips:首先看看列表元素已经存在的属性看看能不能有作为id的；）
* key值仅仅需要同一个列表的兄弟元素之间唯一，而不是全局唯一
* key值仅仅用来react去辨别一个元素，而不作为props传入。
* 直接在JSX中嵌入map，用{}
### Forms
HTML中的form元素在react中与其它dom元素表现有些差异，因为form元素本身保存着内在state；
```
 <form action="">
    <label >Name:
        <input type="text" name="name">
    </label>
    <input type="submit" value='Submit'>
 </form>
```
这个form具有html表格默认的行为，当用户点击提交按钮，提交表格时，就是转到一个新的页面。但是在react中，大多情况下，我们通常采用一个函数去处理表格提交事件，访问到用户输入表格的数据。要达到这个目的，标准做法是采用一个'controlled components'技术。
在html中表格元素如input,textarea,select拥有自己的状态，然后基于用户的输入，可以自动更新自己的状态.
* 在html中select选择列表，通过<option selected value='grape'>Grape</option>来控制选择的项。而在react中通过在根select标签中设置value属性来控制。阻止默认行为用e.preventDafult();
```
  class FlavorFrom extends Component{
  constructor(props){
    super(props);
    this.state={
      value:'apple'
    };
    this.handleChange=this.handleChange.bind(this);
    this.handleSubmit=this.handleSubmit.bind(this);
  }
  render(){
    return (
      <form onSubmit={this.handleSubmit}>
      <label>
            请选择你最喜欢的水果：
          <select value={this.state.value} onChange={this.handleChange} >
            <option value='grape'>Grape</option>
            <option value='mango'>Mango</option>
            <option value='apple'>Apple</option>
          </select>
      </label>
      <input type='submit' value='Submit'/>

      </form>   
    )
  }
  handleChange(e){
    const value=e.target.value;
    this.setState((state)=>({
      value

    }))
  }
  handleSubmit(e){
    alert("your favorite fruit is "+this.state.value);
    e.preventDefault();

  }
}
```
  **react中控制input、textarea、select很相似，都是在其添加一个value属性value={this.state}，然后根据onChange事件用this.setState更新state；选择列表如果是选择多个话，可以给select添加一个multiple={true} value={[]},value属性添加一个列表**
  #### 文件输入标签 <input text='file'/>
  在html中文件输入标签，允许用户选择一个或者多个文件从设备存储中，上传到服务器或者是js通过File API去控制。
  因为其值是只读型，所以在react中其是不可空组件；
  #### 多个控制输入元素
  当需要处理多个控制input元素时，需要在每一个元素添加一个name属性，然后让处理函数根据e.target.name来选择处理；
  ```
  class Reservation extends Component{
  constructor(props){
    super(props);
    this.state={
      isGoing:true,
      numberOfGuests:2
    };
    this.handleInputChange=this.handleInputChange.bind(this);
  }
  handleInputChange(e){
    const target=e.target;
    const value=target.type==='checkbox'?target.checked:target.value;
    const name=target.name;
    //这里注意，当用一个变量作为一个对象的属性的时候，必须用[变量名]；
    //var partialState = {};
    //partialState[name] = value;
    //this.setState(partialState);
    this.setState({
      [name]:value
    })
  }
  render(){
    return (
      <form>
        <label>
          Is going:
          <input 
            name='isGoing'
            type='checkbox'
            checked={this.state.isGoing}
            onChange={this.handleInputChange}
            />
        </label>
        <br/>
        <label>
          Number of guests:
          <input
            name='numberOfGuests'
            type='number'
            value={this.state.numberOfGuests}
            onChange={this.handleInputChange}
            />
        </label>
      </form>
    )
  }


}
  ```
当已经给input设定了一个特定value值，但是input仍然是可编辑的，说明无意中将值设定成undefined或者是null；
```
ReactDOM.render(<input value="hi" />, mountNode);

setTimeout(function() {
  ReactDOM.render(<input value={null} />, mountNode);
}, 1000);

```
### Lifting State Up
经常会遇到几个组件需要根据同一个state data的变化，做出相应的回应。这时候我们应该把它们共享的state data提升到离他们最近的共同祖先上。自顶而下，从父组件保存共有状态data，然后去改变相应需求。
### Composition vs Inheritance
像SiderBar、Dialog这类组件，可能提前不知道自己的children，我们可以直接将props.children直接作为子元素，直接输出。
```
  function FancyBorder(props){
    return (
      <div className={'FancyBorder-'+props.color}>
        {props.children}
      </div>
      )
  }
  function WelcomeDialog(){
    return (
      <FancyBorder color='blue'>
        <h1>Welcome</h1>
        <p>
          Thank you for visiting our spacecraft!
        </p>
      </FancyBorder>
      )
  }
```
<FancyBoder>JSX标签里面的任何东西都会作为children属性，传递给FancyBoder组件。所以FancyBorder渲染{props.children}作为输出。
**组件也可以作为属性值，传入进去**
```
function SplitPane(props) {
  return (
    <div className="SplitPane">
      <div className="SplitPane-left">
        {props.left}
      </div>
      <div className="SplitPane-right">
        {props.right}
      </div>
    </div>
  );
}

function App() {
  return (
    <SplitPane
      left={
        <Contacts />
      }
      right={
        <Chat />
      } />
  );
}
```
