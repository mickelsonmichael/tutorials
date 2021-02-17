# Class Components

The most basic feature of React is a component. Written in JavaScript (or JSX), a component tells React how to render a particular concept onto a page; that could be a button or a table, but really it is anything you may want to appear on screen.

To create a class component, simply create a `class` and inherit from the `Component` class in React. The only requirement for a component beyond that is that it contain a `render` method that returns a valid `ReactNode`. For our purposes, that will be some simple JSX (you can think of JSX as fancy HTML, but there's a lot more to it; read up about it in the official React docs if you want to learn more).

```jsx
class MyComponent extends React.Component {
    // must have at least a render method
    render() {
        return <p>Hello, world!</p>; // return some html/jsx
    }
}
```

With that we have a perfectly valid React component. You can render that component in several ways, including using the `ReactDOM.render` method, but usually you will render it using JSX.

```jsx
// most react sites have an `App` component as the root element
class App extends React.Component {
    render() {
        return <MyComponent />; // you can write your component in JSX as if it was an HTML tag
    }
}
```

But there's a lot more to class components, namely [properties](#properties) and [state](#state)!

## Properties

Think of properties as parameters (because they pretty much are). They are parameters that are passed into the constructor of your component (you created a class, classes have constructors, how else did you think a component was build?). You can define any number of parameters of any type. For example, let's create a `Label` component; this `Label` component should allow a user of the label to pass in some text that will be rendered inside of some HTML with some fancy styles.

The base `Component` class in React has a few properties and functions defined (which is why we inherit from it in the first place), including a property called `props`. Because you are inheriting from the class, you inherit all of the properties and methods as well and can access them using the `this` keyword. When a component is constructed, all of the properties are combined into an object and passed into the constructor for your component, which is then passed to the constructor for the `Component` base class itself, which does the actual assignment.

> Note: Do not try to assign props yourself; leave this to the base class.

```jsx
// not the full component class, just a skeleton of it
class Component {
    props; // property named props

    // properties come in as one object
    constructor(passedInProps) {
        // assigned to the `props` property
        this.props = passedInProps;
    }
}
```

Now that the `props` property has been assigned, we can access it from our class using `this.props`. To get to the actual values themselves, you will have to specify _which_ property you want. If I passed in `<Label text="something" />`, then I would access it using `this.props.text`. We can bring this all together to make our label in the code below.

But first, you need to quickly learn about the `{}` syntax within JSX. You can insert JavaScript code into your JSX by simply wrapping it in `{}`. The **return value** of any expression inside the braces is rendered into the final product. `{"hey"}` will render `hey` and `{ condition ? "yes" : "no" }` will render `yes` if condition is true and `no` if it's false. We can use that to render out text inside of our label. This is a very powerful syntax and is how you can conditionally render elements on the page, but that's a discussion for another section.

```jsx
class Label extends React.Component {
    render() {
        return (
            <label className="fancy">
                {this.props.text}
            </label>
        );
    }
}

// our main app component can now render the Label
class App extends React.Component {
    render() {
        return (
            <div>
                <Label text="What's your name" />
                <input type="text" />
            </div>
        );
    }
}
```

> Notice how I used `className` instead of `class` to provide the CSS classes to the label. This is because `class` is a [protected keyword](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Lexical_grammar#keywords) in JavaScript, and therefore can't be used outside of defining an actual `class`. React gets around this conflict by simply appending `Name` to the end. Anything passed into `className` will be passed through to the HTML's `class` attribute.

A final note about `props`: They shouldn't change within the component. I said earlier that you shouldn't try to do the assignment to `props` yourself; that's because that's not the use-case for props. They are [immutable](https://www.telerik.com/blogs/immutability-in-javascript) and cannot and should not be changed; changing a property fundamentally changes the output the same way changing an argument to a function changes the function's output. `add(2,2)` is not the same as `add(2,3)`, and those rules apply for components as well (while it's possible they return the same result if you do your math wrong, they are still fundamentally different).

If you want to change anything in your component, you should be changing [state](#state).

## State

Class components are also known as _stateful_ components in React lingo, because they (obviously) have a state. That means that React is managing some internal set of values that can be changed to reflect new information within the Component (e.g. user click, fetching data, etc.).

Much like `props`, the base `Component` class has a property called `state`. In a component where you do not define an initial state, this is simply initialized to `null`.

```js
class Component {
    props;
    state;

    constructor(props){
        this.props = props; // same as before
        this.state = null;
    }
}
```

If you want some state, then you'll need to assign it in the constructor yourself (unlike `props` which you shouldn't touch). For the example in this section, let's do the class `Counter` component; a simple component with a button and a number where clicking the button makes the number go up.

To keep track of our number, we'll need to define a value on our state. As I said before, this is done in the constructor. State can be _technically_ be anything, but ReactDOM is going to yell at you in the browser console if it isn't an object, so it's best to stick with objects.

To set state, you need to create a constructor; that means that the props _won't_ be automatically passed into the base class. We'll have to do that manually now by accepting the `props` as a parameter to our constructor (i.e. `constructor(props)`) and then passing those up to the `Component` class where it can be set (i.e. `super(props)`).

```jsx
class Counter extends React.Component {
    // constructor that accepts our props
    constructor(props) {
        // pass those props to the parent by calling the parent constructor using the `super` keyword
        super(props); 

        // set state to some initial value
        this.state = {
            count: 0
        };
    }

    // don't forget the render method!
    render() {
        return (
            <div>
                {this.state.count}
            </div>
        )
    }
}
```

But this component has no button; it will display `0` forever with no way to change it.

This next step is really important, so **pay special attention**. Your gut may tell you that you set the state like `this.state = this.state + 1`. **This is incorrect** and it's actually really important that you know that. The `Component` base class has a `setState` method defined, and that's the way you should be setting state. Simply because the `setState` does a lot in the background to ensure the final product matches your component. So just do yourself a favor and don't try it.

There are a few ways to set state, but the best way is to use a callback within the `setState` function. `setState` will expect you to pass it a function accepting a parameter `state` which is the current state of the application and then return the updated `state` based on some calculations.

```js
increment(previousState) {
    return {
        ...previousState,
        count: previousState.count + 1
    }
}

this.setState(increment); // pass it the function (but don't call it ourselves by putting `()` accidentally)
```

> The three dots is called the [spread syntax](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax) and is an _extremely_ helpful feature of modern JavaScript. Learn it and save yourself a lot of pain.

Obviously this is a little bulky (7 lines just to change one value), but a lot of the time people shorten it down using an arrow function inline

```js
this.setState(prev => ({
    ...prev,
    count: prev.count + 1
}));
```

Which can save you quite a number of lines in the long run.

Another important point to note is that I did `count: prev.count + 1` and **not** `count: this.state.count + 1`. The previous state is there for a reason; use it any time your new state depends on your previous state in any way! `setState` is called asynchronously and React cannot guarantee that `this.state` is still the value you expect; it's safer to avoid this by using the `prev` parameter instead ([See the official React docs for a bit more](https://reactjs.org/docs/state-and-lifecycle.html#state-updates-may-be-asynchronous)).

The `setState` has another overload that accepts a single object as a parameter (e.g. `this.setState({})`). This will set the state to whatever object you pass in with a caveat; this object is merged with the existing object. Let's say for example we have to properties on our state, `age` and `name`. We want to update the `age` but don't really want to update `name`, so we can instead just call the `setState` and pass it an object like `this.setState({ age: 42 })`. This object will be merged with the existing state, leaving the `name` value untouched. This sounds really cool, and honestly it is, but I generally avoid it as a rule of thumb. It is a shallow merge, which can lead to issues if you have complex state objects, so I find it's better to be more verbose to err on the side of caution. Still confused? [Read the React docs for a more coherent explanation](https://reactjs.org/docs/state-and-lifecycle.html#state-updates-are-merged).

One last thing before we finish our component; `HTML` component have a lot of built in properties that are converted from the DOM. Properties like `placeholder`, `name`, and `id` as you'd expect, but also event listeners like `onChange` and `onClick`. You can pass functions into these properties in order to attach a listener to these events. It honestly is the most pleasant part about working with React since it strips out the need to `addEventListener` or `$("").on("event", function() {})`.

So let's wrap it all together; take your time to understand every line of the example below because I'm combining a lot of concepts here.

```jsx
class Counter extends React.Component {
    constructor(props) {
        super(props);

        this.state = {
            count: 0
        };
    }

    render() {
        return (
            <div>
                {this.state.count}
                <button 
                    onClick={() => this.setState(prev => ({
                        ...prev,
                        count: prev.count + 1
                    }))}
                >
                    Increment me!
                </button>
            </div>
        )
    }
}
```

You may think the above code is ugly (and it is). Wouldn't it be better if our `onClick` handler was a separate function? Why yes it would be! But in order to do that we need to introduce one final concept, [binding this](#binding-this).

## Binding this

Our counter example is really verbose in the `onClick` method, so you may be tempted to do this instead

```jsx
class Counter extends React.Component {
    constructor(props) {
        super(props);

        this.state = {
            count: 0
        };
    }

    increment() {
        this.setState(p => ({
            ...p,
            count: p.count + 1
        }));
    }

    render() {
        return (
            <div>
                {this.state.count}
                <button  onClick={this.increment}>
                    Increment me!
                </button>
            </div>
        )
    }
}
```

Go ahead and try this out, but I'm afraid I've got some bad news for you: It won't work. You will get an error in your console, `Uncaught TypeError: this is undefined`, and your counter won't budge. What gives? Well that's a bit of a topic, really. It has to do with how `this` is bound within a function. By default, `this` within a class method is bound to class itself.But when you pass the function off to the `onClick` method, it loses that binding. Make sense? Maybe not. So here's a quick example:

```js
class Shouter {
    constructor(what) {
        this.what = what.toUpperCase(); // give it something to shout
    }

    shout() {
        console.log(this.what); // shout it to the rooftops
    }
}

var me = new Shouter("hello, world"); // create a new shouter
me.shout(); // tell it to shout, and it should work great!

var doShout = me.shout; // but pass the reference away from the instance
doShout(); // and you've broken it
```

I recommend running this example somewhere - in a file, in Codepen, in JSBin, in JSFiddle, wherever you want, just run it; it helps clear things up a bit. But to sum up, when you pass a reference to a function (i.e. `onClick={increment}`) you lose your `this` binding.

How do you fix it? Simple, use the `bind` method to permanently bind `this` in the constructor.

```js
class Shouter {
    constructor(what) {
        this.what = what.toUpperCase();
        this.shout = this.shout.bind(this); // bind it to the current this
    }

    shout() {
        console.log(this.what);
    }
}
```

Essentially what the fourth line does is say "Hey, you know that `shout` method? Replace it with a copy that uses this thing as the internal `this`." In our example, the "thing" is the current `this` which includes our `setState` method.

Finally, put it all together to clean up our counter like so:

```jsx
class Counter extends React.Component {
    constructor(props) {
        super(props);

        this.increment = this.increment.bind(this); // do the binding!!!

        this.state = {
            count: 0
        };
    }

    increment() {
        this.setState(p => ({
            ...p,
            count: p.count + 1
        }));
    }

    render() {
        return (
            <div>
                {this.state.count}
                <button  onClick={this.increment}>
                    Increment me!
                </button>
            </div>
        )
    }
}
```

And all should be right in the world.

This section may be confusing still, and that's OK. It's a convoluted concept that only JavaScript could create because JavaScript is the wild west of languages. If you really want, do some additional research online about the topic and hopefully find some people who are more eloquent and smarter than me to explain it properly.

For React purposes though, you can just say "this is undefined? Forgot to bind" and add the binding call in the constructor.

## Lifecycle Methods

Sometimes in React you will want to do something when the component loads. You may think to go for the constructor, but when the constructor is called, the component isn't live on the page yet, it isn't "mounted" in React terms. This is a concept that relates to the Virtual DOM being aligned with the Browser DOM, React concepts that I will hopefully discuss in another tutorial. There are also times when you may want to do something when the component is updated, or even when the component is about to be removed (unmounted). For these use-cases, React has added in several lifecycle methods that we can hook into!

These methods are features of the `Component` base class; the component base defines them, and it's up to us as the developer to give them a body. Once we've done that, the methods will be automatically called at the proper time!

### `componentDidMount()`

Often times you will want to do something when the component appears on the user's screen. This could be working with a third-party library, or it could be an opportunity to do some calculations based on the size of your elements in the DOM or to start a timer/fetch routine. Whatever it is, you can do it in this method:

```jsx
class AlertOnLoad extends React.Component {
    componentDidMount() {
        alert("You've loaded this component into the DOM!");
    }

    render() {
        // some render method
    }
}
```

### `componentDidUpdate()`

This method is called any time the component is updated after the original mount (and **only** on updates, this method is not called for the initial render). It could be used to update state (based on some conditions) or any other modifications that may need to be made if the component's state changes and you need to react to it (no pun intended).

While you can use `setState` within this method, you **must** do so inside a conditional block.

```jsx
class Counter extends React.Component {
    constructor(props) {
        super(props);
        this.state = { count: 0, done: false };
    }

    componentDidUpdate() {
        if (this.state.count > 10) {
            this.setState({ done: true });
        }
    }

    render() {
        // some render method
    }
}
```

### `componentWillUnmount()`

The final lifecycle method we'll look into is the `componentWillUnmount` method. This is called when a component is going to be removed from the DOM, either because the user navigated away from the page or the component is being removed due to some condition. Most use-cases will be for canceling some subscription or timer to prevent memory leaks. The below code is based on the [time example from the Official React Docs](https://reactjs.org/docs/state-and-lifecycle.html) and if you have any confusion, head over there for more insight.

```jsx
class Fetcher extends React.Component {
    constructor(props) {
        this.state = {
            timer: new Date() // start timer now
        };
    }

    componentDidMount() {
        this.timer = setInterval(() => {
            this.setState({ timer: new Date() }) // update the timer
        });
    }

    componentWillUnmount() {
        clearInterval(this.timer); // stop the timer
    }

    render() {
        // some render method
    }
}
```

## Instance properties (not state or input properties)

It's also possible to define properties on a component beyond the state and input properties. In cases where you don't want your component to update if a value changes, then you can simply define an instance variable instead of a property on state.

You can freely update the properties inside of any methods of the component, but keep in mind that updates to these properties will **not** result in an update for the component. Only state changes and prop changes will do that.

You may use these properties to define `refs` or state that doesn't affect display.

```jsx
class Instance extends React.Component {
    constructor(props) {
        super(props);

        this.renderCount = 0;

        this.setState({
            count: 0
        })
    }

    onComponentDidUpdate() {
        this.renderCount += 1;
        // this will not cause another update for the component
        // the render count will not always match the state.count
        // it will simply keep track of how many times the component
        // has updated
    }

    increment() {
        this.setState(prev => ({
            count: prev.count + 1 
        }));
    }

    decrement() {
        this.setState(prev => ({
            count: prev.count - 1
        }));
    }

    render() {
        return (
            <>
                <button onClick={this.decrement}>-</button>
                {this.state.count}
                <button onClick={this.increment}>+</button>
        )
    }
}
```

## Conclusion

This wraps up class components for now. There are additional lifecycle methods to explore, but the ones listed above are by far the most popular.

The `index.html` file in this section creates a simple `Counter` component that - in a very contrived manner - wraps all these concepts into a small package. You should understand what each of the parts is doing; ideally no line should be confusing to you.
