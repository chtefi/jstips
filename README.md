![header](https://raw.githubusercontent.com/loverajoel/jstips/master/resources/jstips-header-blog.gif)

# Introducing Javascript Tips
> New year, new project. **A JS tip per day!**

With great excitement, I introduce short and useful Javascript tips per day that will allow you to improve your code writing. With less than 2 minutes each day, you will be able to read about performance, frameworks, conventions, hacks, interview questions and all the items that future of this awesome language holds for us.

At midday, no matter if it is a weekend or a holiday, a tip will be posted and tweeted.

### Can you help us enrich it?
Please feel free to send us a PR with your own Javascript tip to be published here.
Any improvements or suggestions are more than welcome!
[Click to see the instructions](https://github.com/loverajoel/jstips/blob/master/CONTRIBUTING.md)

### Let’s keep in touch
To get updates, watch the repo and follow the [Twitter account](https://twitter.com/tips_js), only one tweet will be sent per day. It is a deal!
> Don't forget Star the repo, this will help to diffuse the project!

# Tips list

## #1(number) - Alternatives to classic `for` loops

> yyyy/mm/dd(date) by [@chtefid](https://twitter.com/chtefid)

Everybody knows the classic `for` loop :

```javascript
for (var i = 0; i < elements.length; i++) {
  var element = elements[i];
  doSomethingWith(element);
}
```

But often, you don't need `i` anywhere else except to get the element at this index.
You just want to loop over your elements to do something with them.
An alternative is to use the ES5 Array functions, such as `forEach` :

```javascript
elements.forEach(doSomethingWith);
```
Each item from `elements` will be passed as argument one by one to `doSomethingWith`.

Same idea when you want to filter some elements in an array, you can replace this kind of loop: 

```javascript
var validElements = [];
for (var i = 0; i < elements.length; i++) {
  var element = elements[i];
  if (element.isValid) {
    validElements.push(element);
  }
}
```

And use the `filter`'s Array function :

```javascript
var elements = [ { name: 'A', isValid: true }, { name: 'B', isValid: false } ];
var validElements = elements.filter(function(element) { return element.isValid; });
// validElements = [ { name: 'A', isValid: true } ]
```

Another useful function is `map`. When you want to extract a particular field (or severals) from an array of objects, or transform each element, it's the perfect candidate :

```javascript
var people = [ { name: 'John', age: 13 }, { name: 'Henry', age: 37 } ];
var ages = people.map(function(person) { return person.age; });
// ages = [ 13, 37 ]
var uppercaseNames = people.map(function(person) { return person.name.toUpperCase(); });
// uppercaseNames = [ "JOHN", "HENRY" ]
```

Because you are using a callback, the `this` inside does not reference the same `this` as the caller. If you don't use `.bind` to change its context, `this` will be equals to `window`, the global object (that's often lead to some bugs).
But hopefully, those functions take a second parameter that will be passed as the `this` inside the callback :
```javascript
this.max = 20;
people.filter(function(person) { return person.age < this.max; }, this);
```

Last but no least, the callback you pass to `forEach`, `filter`, `map` will get three arguments, not only one :

```javascript
elements.forEach/filter/map(function(item, index, array) {
	// item : current item in the loop
	// index : the current index, the `i` in the classic `for`
	// array : the array you're looping through. It can come in handy when your program is quite dynamic.
});
```
It's not mandatory to always add them to the function parameters, often only `item` is needed.


Notice that `map` and `filter` don't modify your original array, they return a new one (but if you have objects inside, they are not cloned, they have the same reference).

Consider using those functions when it's not perf-critical, they are a bit slower than the classic `for` : https://jsperf.com/for-vs-foreach/444

Check out the [Mozilla documentation](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Objets_globaux/Array) to find out more about those functions.


## #06 - Writing a single method for arrays or single elements

> 2016-01-06 by [@mattfxyz](https://twitter.com/mattfxyz)

Rather than writing separate methods to handle an array and a single element parameter, write your functions so they can handle both. This is similar to how some of jQuery's functions work (`css` will modify everything matched by the selector).

You just have to concat everything into an array first. `Array.concat` will accept an array or a single element.

```javascript
function printUpperCase(words) {
  var elements = [].concat(words); 
  for (var i = 0; i < elements.length; i++) {
    console.log(elements[i].toUpperCase());
  }
}
```

`printUpperCase` is now ready to accept a single node or an array of nodes as it's parameter.

```javascript
printUpperCase("cactus"); 
// => Cactus
printUpperCase(["cactus", "bear", "potato"]);
// => Cactus
//  Bear
//  Potato
```

## #05 - Differences between `undefined` and `null`

> 2016-01-05 by [@loverajoel](https://twitter.com/loverajoel)

- `undefined` means a variable has not been declared, or has been declared but has not yet been assigned a value
- `null` is an assignment value that means "no value"
- Javascript sets unassigned variables with a default value of `undefined`
- Javascript never sets a value to `null`, is used by programmers to indicate that a `var` have no value.
- `undefined` is not valid in JSON while `null` is
- `undefined` typeof is `undefined`
- `null` typeof is an `object`
- Both are primitives
- You can know if a variable is [undefined](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/undefined)

  ```javascript
  typeof(variable) === "undefined"
```
- You can check if a variable is [null](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/null)

  ```javascript
  variable === null
```
- The **equility** operator consider them equal, but the **identity** doesn't

  ```javascript
  null == undefined // true

  null === undefined // false
```

## #04 - Sorting strings with accented characters

> 2016-01-04 by [@loverajoel](https://twitter.com/loverajoel)

Javascript has a native method **[sort](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)** that allows sorting arrays. Doing a simple `array.sort()` each value will be treated as a string and sorted alphabetically. Also you can create your [own custom sorting](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort#Parameters) function passing it how argument.

```javascript
['Shanghai', 'New York', 'Mumbai', 'Buenos Aires'].sort();
// ["Buenos Aires", "Mumbai", "New York", "Shanghai"]
```

But when you try order an array of non ASCII characters like this `['é', 'a', 'ú', 'c']`, you will obtain an strange result `['c', 'e', 'á', 'ú']`. That happens because sort works only with english language.

See the next example:

```javascript
// Spanish
['único','árbol', 'cosas', 'fútbol'].sort();
// ["cosas", "fútbol", "árbol", "único"] // bad order

// German
['Woche', 'wöchentlich', 'wäre', 'Wann'].sort();
// ["Wann", "Woche", "wäre", "wöchentlich"] // bad order
```

Fortunately exists two ways to avoid this mistake [localeCompare](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/localeCompare) and [Intl.Collator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Collator) provided by ECMAScript Internationalization API.

> Both methods have his own custom parameters in order to configure it for work adequately.

### Using `localeCompare()`

```javascript
['único','árbol', 'cosas', 'fútbol'].sort(function (a, b) {
  return a.localeCompare(b);
});
// ["árbol", "cosas", "fútbol", "único"]

['Woche', 'wöchentlich', 'wäre', 'Wann'].sort(function (a, b) {
  return a.localeCompare(b);
});
// ["Wann", "wäre", "Woche", "wöchentlich"]
```

### Using `Intl.Collator()`

```javascript
['único','árbol', 'cosas', 'fútbol'].sort(Intl.Collator().compare);
// ["árbol", "cosas", "fútbol", "único"]

['Woche', 'wöchentlich', 'wäre', 'Wann'].sort(Intl.Collator().compare);
// ["Wann", "wäre", "Woche", "wöchentlich"]
```

- For each method you can customize the location.
- According to [Firefox](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/localeCompare#Performance) Intl.Collator is faster when comparing large numbers of strings.

So remember when you are working with arrays of strings in a language other than English, use this method to avoid unexpected errors.

## #03 - Improve Nested Conditionals
> 2016-01-03 by [AlbertoFuente](https://github.com/AlbertoFuente)

How can we improve and make more efficient nested `if` statement on javascript.

```javascript
if (color) {
  if (color === 'black') {
    printBlackBackground();
  } else if (color === 'red') {
    printRedBackground();
  } else if (color === 'blue') {
    printBlueBackground();
  } else if (color === 'green') {
    printGreenBackground();
  } else {
    printYellowBackground();
  }
}
```

One way to improve the nested `if` statement would be using the `switch` statement. Although it is less verbose and is more ordered, It's not recommended to use it because it's so difficult to debug errors, here's [why](https://toddmotto.com/deprecating-the-switch-statement-for-object-literals/).

```javascript
switch(color) {
  case 'black':
    printBlackBackground();
    break;
  case 'red':
    printRedBackground();
    break;
  case 'blue':
    printBlueBackground();
    break;
  case 'green':
    printGreenBackground();
    break;
  default:
    printYellowBackground();
}
```

But what if we have a conditional with several checks in each statement? In this case, if we like to do less verbose and more ordered, we can use the conditional `switch`.
If we pass `true` as parameter to the `switch` statement, It allows us to put a conditional in each case.

```javascript
switch(true) {
  case (typeof color === 'string' && color === 'black'):
    printBlackBackground();
    break;
  case (typeof color === 'string' && color === 'red'):
    printRedBackground();
    break;
  case (typeof color === 'string' && color === 'blue'):
    printBlueBackground();
    break;
  case (typeof color === 'string' && color === 'green'):
    printGreenBackground();
    break;
  case (typeof color === 'string' && color === 'yellow'):
    printYellowBackground();
    break;
}
```

But we must always avoid having several checks in every condition, avoiding use of `switch` as far as possible and take into account that the most efficient way to do this is through an `object`.

```javascript
var colorObj = {
  'black': printBlackBackground,
  'red': printRedBackground,
  'blue': printBlueBackground,
  'green': printGreenBackground,
  'yellow': printYellowBackground
};

if (color && colorObj.hasOwnProperty(color)) {
  colorObj[color]();
}
```

Here you can find more information about [this](http://www.nicoespeon.com/en/2015/01/oop-revisited-switch-in-js/).

## #02 - ReactJs - Keys in children components are important

> 2016-01-02  by [@loverajoel](https://twitter.com/loverajoel)


The [key](https://facebook.github.io/react/docs/multiple-components.html#dynamic-children) is an attribute that you must pass to all components created dynamically from an array. It's unique and constant id that React use for identify each component in the DOM and know that it's a different component and not the same one. Using keys will ensure that the child component is preserved and not recreated and prevent that weird things happens.

> Key is not really about performance, it's more about identity (which in turn leads to better performance). randomly assigned and changing values are not identity [Paul O’Shannessy](https://github.com/facebook/react/issues/1342#issuecomment-39230939)

- Use an exisiting unique value of the object.
- Define the keys in the parent components, not in child components

	```javascript
	//bad
	...
	render() {
		<div key={{item.key}}>{{item.name}}</div>
	}
	...

	//good
	<MyComponent key={{item.key}}/>
	```
- [Use the array index is a bad practice.](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318#.76co046o9)
- `random()` will not work

	```javascript
	//bad
	<MyComponent key={{Math.random()}}/>
	```

- You can create your own unique id, be sure that the method be fast and attach it to your object.
- When the amount of child are big or involve expensive components, use keys has performance improvements.
- [You must provide the key attribute for all children of ReactCSSTransitionGroup.](http://docs.reactjs-china.com/react/docs/animation.html)

## #1 - AngularJs: `$digest` vs `$apply`

> 2016-01-01  by [@loverajoel](https://twitter.com/loverajoel)

One of the most appreciated features of AngularJs is the two way data binding. In order to make this work AngularJs evaluate the changes between the model and the view through of cycles(`$digest`). You need to understand this concept in order to understand how the framework works under the hood.

Angular evaluate each watcher whenever one event was fired, this is the known `$digest` cycle.
Sometimes you have to force to run a new cycle manually and you must choose the correct option because this phase is one of the most influential in terms of performance.

### `$apply`
This core method lets you to start the digestion cycle explicitly, that means that all watchers are checked, the entire application starts the `$digest loop`. Internally after execute an optional function parameter, call internally to `$rootScope.$digest();`.

### `$digest`
In this case the `$digest` method starts the `$digest` cycle for the current scope and its children. You should notice that the parents scopes will not be checked
 and not be affected.

### Recomendations
- Use `$apply` or `$digest` only when browser DOM events have triggered outside of AngularJS.
- Pass a function expression to `$apply`, this have a error handling mechanism and allow integrate changes in the digest cycle

	```javascript
	$scope.$apply(() => {
		$scope.tip = 'Javascript Tip';
	});
	```

- If only needs update the current scope or its children use `$digest`, and prevent a new digest cycle for the whole application. The performance benefit it's self evident
- `$apply()` is hard process for the machine and can lead to performance issues when having a lot of binding.
- If you are using >AngularJS 1.2.X, use `$evalAsync` is a core method that will evaluate the expression during the current cycle or the next. This can improve your application's performance.


## #0 - Insert item inside an Array
> 2015-12-29

Insert an item into an existing array is a daily common task. You can add elements to the end of an array using push, to the beginning using unshift, or the middle using splice.

But those are known methods, doesn't mean there isn't a more performant way, here we go...

Add a element at the end of the array is easy with push(), but there is a way more performant.

```javascript
var arr = [1,2,3,4,5];

arr.push(6);
arr[arr.length] = 6; // 43% faster in Chrome 47.0.2526.106 on Mac OS X 10.11.1
```
Both methods modify the original array. Don't believe me? Check the [jsperf](http://jsperf.com/push-item-inside-an-array)

Now we are trying to add an item to the beginning of the array

```javascript
var arr = [1,2,3,4,5];

arr.unshift(0);
[0].concat(arr); // 98% faster in Chrome 47.0.2526.106 on Mac OS X 10.11.1
```
Here is a little bit detail, unshift edit the original array, concat return a new array. [jsperf](http://jsperf.com/unshift-item-inside-an-array)

Add items at the middle of an array is easy with splice and is the most performant way to do it.

```javascript
var items = ['one', 'two', 'three', 'four'];
items.splice(items.length / 2, 0, 'hello');
```

I tried to run these tests in various Browsers and OS and the results were similar. I hope this tips will be useful for you and encourage to perform your own tests!

### License
[![CC0](http://i.creativecommons.org/p/zero/1.0/88x31.png)](http://creativecommons.org/publicdomain/zero/1.0/)
