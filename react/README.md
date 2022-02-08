# React

The examples in this directory will pertain to the [React](https://reactjs.org) library. **They are not meant as a replacement for the tutorials found on the official React website**, merely some supplemental learning opportunities and references.

For simplicity's sake, I've divided each topic into a separate directory, each with their own `index.html` file and `README.md`.

Read through the `README.md` to get a handle on the concepts; that should be enough to get you 90% of the way there from a technical standpoint, but it's important to get your hands on some code, which is where the `index.html` file comes in.

All the code will be located near the bottom of the `index.html` file below the React imports. That will prevent readers from having to install `npm` and running commands that they may or may not be prepared to run. These tutorials are about learning React concepts, not environment concepts like `npm`, `babel`, or `WebPack`.

Ideally, the code would be inside its own file, separate from the `html` file, but modern browsers have a lot of security features in place, namely CORS, that aim to prevent scripts from accessing files on your computer that they shouldn't have access to. Thus the easiest way around that is to place the scripts within the `index.html` file itself; the browser can be reasonably certain that the code inside is safe and not manipulating or reading anything on the user's hard drive.

## How to Run the Code

Simply open the `index.html` file for the topic in any compatible browser to view the results.

## Recommended Reading Order

1. [Class Components](./class-components/README.md)
2. [Functional Components](./functional-components/README.md)
