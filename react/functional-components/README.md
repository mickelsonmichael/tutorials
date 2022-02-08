# Functional Components

If you haven't already, go back and read about class components first. It builds a lot of concept on how React expects components to be structured.

Functional components, for better or worse, are the way of the future. They allow you to define components using a function rather than a class. Let's look at a really simple example of a class component, and then convert it to a functional.

```jsx
class MyLabel : Component {
  render() {
    return (
      <label htmlFor={props.htmlFor}>{props.text}</label>
    )
  }
}

// Rendered like
<MyLabel htmlFor="my-input" text="My Input" />
```

This is great, but we can re-write it even simpler, cutting out a lot of the "fat" of the code.

```jsx
function MyLabel(props) {
  return (
    <label htmlFor={props.htmlFor}>{props.text}</label>
  )
}

// Rendered just like a class component
<MyLabel htmlFor="my-input" text="My Input" />
```

This is also great, but writing `function` in JavaScript is a little unconventional these days.
These days, [arrow functions](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions), which in most languages are called lambda functions, are much more popular and idiomatic.
They're very simple to write, but may take newcomers to the language a little time to understand.
Here's the same component above, written in the arrow syntax instead.

```jsx
const MyLabel = (props) => (
  <label htmlFor={props.htmlFor}>{props.text}</label>
);

// Rendered like both of the above
<MyLabel htmlFor="my-input" text="My Input" />

// OR using a `return` statement instead of an implicit return
const MyLabel = (props) => {
  return (
    <label htmlFor={props.htmlFor}>{props.text}</label>
  );
}
```

Both are equally valid, and at the end of the day **it's most important that you follow the convention of the code-base**.
If your workplace uses all `function` definitions, then use those; if they use all arrow functions, then use those.
Consistency is one of the key tenants of clean code.

## State and Hooks

This title is a bit of a misnomer. Functional components don't _technically_ have a state. What may seem like state is actually closer to a database than anything.

Let's talk about Hooks first.
I would highly recommend that you read the [official React documentation for Hooks](https://reactjs.org/docs/hooks-intro.html), because they understand the intricacies of hooks far greater than I ever could.
However, I'll do my best to explain them here for those in need of a TLDR.

Hooks are functions, that provide stateful component functionality to functional components.
It's easiest if we jump into the most common and simple example, the `useState` hook.
The naming  should be a huge tip about what it does.
To explore, we'll re-implement the class component from the previous section, which has a simple button and counter that can be incremented.

The `useState` hook returns an array. The first element is the current state, and the second element is a function that can be used to set or update that state.
You can define the default state by passing it as the first parameter to the function.

```jsx
const result = useState("hello"); // use a string state

console.log(result[0]); // logs "hello"

result[1]("goodbye"); // sets the state to goodbye

console.log(result[0]); // logs "goodbye"
```

The above is an extremely contrived example, you would almost never set state in this way and immediately, it's to demonstrate the return values.
This kind of state setting has _huge_ implications for component re-renders, but that's a more advanced topic you can read about in the React docs.
You'll also notice that it's super weird to be calling the function with the syntax `result[1]("goodbye")`, and that's why most developers utilize [destructuring](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) instead.

```jsx
const [message, setMessage] = useState("hello");

console.log(message); // logs "hello"

setMessage("goodbye"); // sets the state to goodbye

console.log(message); // logs "goodbye"
```

This example is much cleaner and far easier to understand.
Note that we were able to define the two items with whatever names we want.
The first line could be written like the following.

```jsx
const result = useState("hello");

const message = result[0];
const setMessage = result[1];
```

But that's three lines instead of one, so just get used to using the destructuring assignment.
Let's finally implement our counter before.
If you'll recall, it looked something like this.

```jsx
class Counter extends React.Component {
  constructor(props) {
    super(props);

    this.increment = this.increment.bind(this);

    this.state = {
      count: 0,
    };
  }

  increment() {
    this.setState((p) => ({
      ...p,
      count: p.count + 1,
    }));
  }

  render() {
    return (
      <div>
        {this.state.count}
        <button onClick={this.increment}>{this.props.buttonText}</button>
      </div>
    );
  }
}
```

27 lines. But let's do it as a functional component and see what the equivalent code looks like.

```jsx
const Counter = (props) => {
  const [count, setCount] = useState(0);
  const increment = () => setCount((prevCount) => prevCount + 1);

  return (
    <div>
      {count}
      <button onClick={increment}>{props.buttonText}</button>
    </div>
  );
};
```

Functionally does the same thing, with only 11 lines.
Almost 60% smaller, and this is just a contrived example; the savings in a real-world application can be even greater.
There are a few more things we should discuss, however.

### Function composition

For starters, let's talk about the second element in the array returned from `useState`, the `setCount` function in the example above.
Much like the `this.setState` function in a class component, this function can either take in an exact value, `setCount(5)`, or a function
that uses the previous value as the first parameter, `setCount(prev => prev+1)`.
In general, you write these update statements in arrow syntax per convention, but it is valid to write them in any number of ways with the `function` syntax.
In the example above, I didn't want to place the entire logic of incrementing the the count inside the button's `onClick` property, so I used a concept known as "function composition" to create a function from another function.
`const increment = () => setCount(prev => prev + 1);` defines a function that, when called, calls the `setCount` function with a pre-determined input.
This is a very useful strategy for cleaning up your code and making it more readable.
Here's another example you may actually encounter if you end up developing React applications in the future.

```jsx
// define whether a dialog box is open or not
const [isOpen, setIsOpen] = useState(false);

// define a function that sets the isOpen property to true
const openDialog = () => setIsOpen(true);
```

### Other types of hooks

[There are a number of additional hooks provided by React](https://reactjs.org/tutorial/tutorial.html) that you should go through.
While `useState` will be your mainstay for most functions, there are a **ton** of use-cases for other hooks and it will help to know what's out there.

#### Creating Custom Hooks

In the dialog box [example above](#function-composition), we used a simple `useState` call to determine whether a dialog box was open or closed.
That's great, but we may have to repeat this logic multiple times because we have more than one type of dialog box.
That's where custom hooks come in.

Custom hooks are essentially just a function that contains calls to the core React hooks, that you then utilize as a wrapper around those hooks.
Similar to [function composition](#function-composition). So in our dialog example, we could create a hook like the following

```jsx
const useIsOpen = (defaultValue = false) => {
  const [isOpen, setIsOpen] = useState(false);
  
  const open = () => setIsOpen(true);
  const close = () => setIsOpen(false);
  const toggle = () => setIsOpen(prev => !prev);
  
  return {
    isOpen,
    open,
    close,
    toggle
  }
```

Notice we provide an object that contains several additional helper methods, `close`, `open`, and `toggle`.
These are all things that might be useful when working with a dialog box and help reduce the amount of times you may need to write `setIsOpen(true)`.

You can then pick and choose which functions you want by using destructuring assignment like above.

```jsx
const MyModal = () => {
  const { isOpen, toggle } = useIsOpen();

  return (
    <div>
      <div hidden={!isOpen}>I'm a modal</div>
      <button onClick={toggle}>Toggle the modal</button>
    </div>
  );
}
```

This is much cleaner and more reusable code than the alternative.
And this is just a simple example, you can write extremely complex and useful hooks that you can reuse in multiple applications.

Check out <https://usehooks.com/> for some more examples of extremely handy custom hooks.
Note that some of these hooks even build on one another, so custom hooks can wrap around custom hooks and make even more useful functions!

### The Law of Hooks

When developing functional components, you'll inevitably come across a particular error message.
Everyone has, and everyone will at some point.
There are actually [several laws](https://reactjs.org/docs/hooks-rules.html), but most are easy enough to avoid breaking.
This one, however, is a common desire most developers have.

> Hooks cannot be called conditionally.

What does this mean? That this:

```jsx
if (something) {
  const [stuff, setStuff] = useState({});
}
```

Is invalid. You _must_ always call every hook each time you run a component.
No hook can be optionally called given some condition.
Do yourself a favor and remember this rule, because it's going to hit you later.
When it inevitably does, just remember this explanation, and hopefully you'll save some time and just re-structure your component.

### How are hooks considered "stateless"

That's a bit of a topic. The short answer is that hooks are more like a database lookup.
When you call a hook, React stores the value of that hook, for that particular instance, in a secret database to maintain that state.
Calling a hook simply prompts React to lookup the value or create it if it doesn't exist.
The component itself isn't involved with that at all, which is why it gets to keep the moniker of "stateless" even though most would argue hooks add the state.

## Conclusion

This was a highly simplified review of what functional components are.
I would **very highly recommend** looking at the offical React documentation at <https://reactjs.org/docs>.
I realize most documentation is quite daunting, but the React team has done an excellent job of making their docs approachable.
I have only given you a surface-level introduction, enough to get you through a course on web development.
