# REACT
### in this caption we look:
1. the basic teory react
2. how start a basic project with react
3. how to funcion basic in react
4. how to impruve a library (as bootstrap) or framework (as tailwind)


## basic teory React 

When we say "what is react?" this's a library that allows us using component for building a our project in smart mode.
(remember that react is library end not is framework as Angular)
React is a most popular library that in the word for developing front-end, his competitor are: "Angular, Vue.js".
this info is available on gemini IA, are valid until today 11/04/2024:

<img src='./assets/img/competitorsReactJs.png' style=' height: 500px'>

i want say that all actions we can do it with react can be done 'normal' js.
react is build by Meta and using in facebook, in 2013 is released how project Open Source.
with react we not manipulated "DOM" but Interacting with "virtual DOM".
are present different methods for "install" react, using CDN or using NodeJs for install a boilerplate of react.
now i say how to using NodeJS for install boilerplate of react.


1. create a folder

2. opend the CMD in the folder (open the folder right click selct "open CMD")

3. past this command in CMD

```
- npm install -g create-react-app

- npx create-react-app appName

- cd .\appName\ 

- npm start   
```

now that you have creat your place for work i can say that React using the "jsx" as syntax, this is more similar to simple js but is a version with steroids, then we will see the differences.

in react are present a particular consept, you can imagine your app as body and this is component by atoms, we can say that atoms are component develop in React.
we can replay the same atom in more part of body.
(we can simply say that the components are classical functions/callback.)

example:

<img src='./assets/img/exampleComponentInReact.png'>

Another propiety essentially is the "proprs" , we can set more value in a function/component/atom with the props, an example:

<img src='./assets/img/exampleProps.png'>

you can import primitive values (string,numeric,array and object)

> [!TIP]<br>
> when you callback a props you can "destructure" the propieties using thi syntax:
> ```
>const Example = function({example1, example2, ...example3}) {
>   return(
>   <h1>example1</h1>
>   <p>example2</p>
>   <p>example3</p>
>   )}
> ```
Is present a special props this's call "children" an example of this props is:

<img src='./assets/img/children.png'>

his function is insert props value that we passed in side the component/atom.

<img src='./assets/img/childrenInSertValue.png'>

the results is:

<img src='./assets/img/childrenResult.png'>

remember when you want start create an element you must insert all element in a div or in a  react fragment, example:

```
const Example = function() {
   return(
   <div>
    <h1>example1</h1>
    <p>example2</p>
    <p>example3</p>
   </div>
)}

OR

long form:

const Example = function() {
   return(
   <React.Fragment>
    <h1>example1</h1>
    <p>example2</p>
    <p>example3</p>
   </React.Fragment>
)}


contract form:

const Example = function() {
   return(
   <>
    <h1>example1</h1>
    <p>example2</p>
    <p>example3</p>
   </>
)}
```

> [!TIP]<br>
>
>if you want get data by server frist that load page you can inser the "request" in head as your application can be loaded with the data inside.  
>example:
>
><img src='./assets/img/scriptInHead.png'>
>
>p.s.
>remember that this has necesry need onload function 

