# Tips on Writing BadJavaScript

Welcome to the tips section hopefully this will help you write some aweful javascript code.

To put it lightly this may offend you, and you should be offended. However it was your curiousity that lead you here.

JavaScript is a VERY quirky language, and I am going to show you how to write the worst code possible.

## Coercion
 
Coercion is going to be your best freind on making your code more unreadable. Here are a few examples to help you out!

### === or the 'Strict Equal'

In javascript if we want to check if something is equal we use the `===` statement. This is great and short however it is readable. Obviously something we don't want in our BadJavaScript code.

How do we get around this?

There are two ways one with numbers, and one with strings.

#### Numbers

In javascript any number that is not 0 is `true`. From that we can devise if two numbers are `===`. given that we have two numbers 1, 2. The most practical way to test them is with subtraction.

`1 - 2 = -1 or 2 - 1 = 1` Either way javaSricpt will evaluate it to true.

`2 - 2 = 0`  A number that is subtracted from itself is always 0 which is always false.

```JavaScript

if (1-2){
  if any number but 0
  return 'the numbers are not equal'
}
// if 0
return 'the numbers are equal'

```

##### Strings

Strings are a lot more complicated as `===` makes comparisions trivial. 

The best way I could think of would to use `localeCompare()`. 

```JavaScript

let a = 'test'.localCompare('test')
// a equals 1 which is true.
let b = 'test'.localCompare('t')
// b equales 0 which is false

```

This works however it is readable. Instead we could use regex to make it less readable, I mean who actually learns regex :-)

```JavaScript

let text1 = 'test';
let text2 = 'test';
text2.search(new RegExp(`^${text1}$`,'g')) //Will return 0 if it matches and -1 if there are no matches
//by specifying our our regex and using it inline our code becomes more complicated and less readable.
//-1 is true and 0 is false so we need to use ! to flip that.

```

Well I guess regex did not answer your question so lets try something fancier?

Looping seems to be the only way to determine whether a string matches another... That just won't do!
So here is a great solution. JavaScript support string comparision with `>` and `<`. JS uses “dictionary” or “lexicographical” order while looping through the strings to determine if one is Greater/Less than the other.

So how can we exploit this? well if a string is equal to another then it is neither less than or greater than the other. If we write a expression that returns true when the string is greater than or less than the other string we can effectively create a comparision that replaces `===`

```JavaScript
let a = 'test';
let b = 'Test'

(a < b || b < a)
// equals true

//this can be rewritten in many different ways
(a > b || b > a)
(b > a || a > b)
(b > a || b > a)

```

### Bool/String/Number Coercion

JavaScript is a weakly type language, and this is what allows us write BadJS. There are Two ways to convert different data types to other data types, Implicit and Explicit conversions. Explicit conversions are readable which means they are useless in BadJS.

#### To Strings

The `+` operater is the easiest and fastest way to convert any data type into a string. Another way is to use template literals this could be considered less readable at some points.

```JavaScript

let a = false

let b = a+''
//b = 'false'

let c = `${a}`
c = 'false'
```

#### To Numbers

This is where it gets a little more complicated, because the JS compiler will automatically trigger implicit conversions in certian cases including: 

- comparison operators (`>`, `<`, `<=`,`>=`)
- bitwise operators ( `|` `&` `^` `~)
- arithmetic operators (`-` `+` `*` `/` `%`). Note, that binary+ does not trigger numeric conversion, when any operand is a string.
- unary `+` operator
- loose equality operator `==` (incl. `!=`). 
- Note that `==` does not trigger numeric conversion when both operands are strings.


```JavaScript

+'123'          // implicit
123 != '456'    // implicit
4 > '5'         // implicit
5/null          // implicit
true | 0        // implicit

+undefined     //NaN
+" 12 "        //12
+"\n"          //0
+false         //0
+true          //1
+null          //0
```

JavaScript will trim whitespaces from string before converting it to a number. (This includes `\n` and `\t`)

Remember that NaN !== NaN or anything else, this is important because if you implicitly convert something to a number and JS spits out NaN all comparisions will fail.

When applying `==` to `null` or undefined, numeric conversion does not happen. `null` equals only to `null` or `undefined`, and does not equal to anything else.


#### To Bool

JavaScript implicitly converts values into bools in two cases. The first being logical contexts, and the second is triggered by logical operators. (`||` `&&` `!`)

```JavaScript

if (2) { ... }      // implicit due to logical context
!!2                 // implicit due to logical operator
2 || 'hello'        // implicit due to logical operator

```

Logical operators such as `||` and `&&` do boolean conversions internally, but actually return the value of original operands, even if they are not boolean.

```JavaScript

let x = 'hello' && 123;    // x === 123
let y = true && 'apple';   // y === apple
let z = false && 'orange'; // z === false

```

Empty arrays and Objects are converted to true as well. (Arrays are converted to true in general). The following always convert to false.

```JavaScript

!!('')      // false
!!0         // false
!!(-0)      // false
!!(NaN)     // false
!!(null)    // false
!!undefined // false
!!false     // false

```

## If/Switch

### Ternary Statements
 
Need an `if`? use a ternary statement! Need an `else if`? Nest ternary statements! 

```JavaScript

let a = 0;
let b = 1;
let c = 0;

let d = a?a:b?a:c
// what could d be? nobody knows! *its a... d = a*
```

ternary statements are probably the worst offenders on the list of things you will read here.

### exclusive `&&`/`||`

if you need quick if statements you can use `&&` or `||`. Javascript has this fun quirk where the second value in a comparision with these operators is always returned

```JavaScript

let a = true&&'apples';
// a === 'apples'

let b =- false||'grapes';
// b = 'grapes'

//we get some fun times when you chain them together for if else statments

let c = a && (false||b);

// what do you think c equals? its 'grapes'!

// heres a fun tid bit

```

### The Comma operator `,`

wait `,` is an operator? like `>` or `&&`? Yes!

what would you say `console.log(5, 10)` prints? 

`5 10` is the correct answer, how about `console.log((5, 10))`?

You would think `(5, 10)` but in fact it prints `10`.

Why? well JavaScript actually has an operator called the `comma operator` and it always return the farthest right item in an expression.

For instance `(1, 3, 4)` will always evaluate to `4`.

remember everything in the expression is evaluated!

Of course this is exploitable... 

```JavaScript
let a = 'apples';

// this will run but what will it return?

let d = (c&&(a||b), a+1)

//'apples1' weird right...

function example(a){
 return (a<5&&(a+=5)), a;
}

// If your answer was return `a+5` when a < 5 is true and return a when a >=5 then you are correct. Everything in the comparision is 
// evaluated only if a < 5 is true. however a is always evaluated because the compiler evaluates from left to right. Crazy right?

```

### switch/case
 
Unfortunatly we don't really have access to the `switch` statement but we have the next best thing! 

Let me introduce you to the Inline `MapSwitch`!

```JavaScript

let a = 1;

let b = ({0:'a',1:'e'}[a]||true)
//a === 'e'  

```

Now you might be thinking wait what about `default` that we can use in `switch` statemets? Amazingly if a was a number that was not in the object the statement returns undefined. As we know from coercion `undefined === false` so `undefined||''` always will return `''`.


## for/of/in
  
I feel as though I have failed you. Don't you remember we are trying to write unreadable code. You should not need to use these things. I mean you can, but you will have to make them unreadable.

for statements cannot be written without `{` and `}`. so not realy useful in this instance.
  
Fortunatly with much glee I am happy to tell you there is a way to iterate through things with out for loops or fancy prototype functions.
 
 ### Iterators
 
 iterators are a fun exorcise in writing unreadable code, they obfuscate what your code is doing. So what if I told you we can Iterate any thing that can be turned into an array in javascript?
 
 Let me introduce you to the equivalant of prototype.length but with Iterators
 
 both `Array.prototype.values()` and `Array.prototype.entries()` return a iterator that contains the values of a string or array.
 
 These are very obscure functions and it goes without saying iterators are very hard to keep up with.  
 
 ``` Javascript
 
 const test=(e,d=[...e].values(),c=0)=>(r=>!(r)?c:test(e,d,c+1))(d.next().value)
 
 ```
 
 `Array.prototype.values()` is a nice little bit of code that returns an `Iterator Object` with one property, `next()`. When next hits the end of an array its value is returned as `undefined`. Using IIFE we can create functions that Iterate with out parameter or variables for an index.

## Length Of Arrays/Strings

Normally we would use Object.length in order to find the length of arrays or strings. JS has some interesting string functions

```JavaScript
let test = "ipsum";

let length = test.lastIndexOf("");
//length now equals the length of the string.

```

How does this work? well it's kind of apparent. JS starts at the end of the string searching for `""`. And Imagne that its at the end of the string!. It's kind of readable, would anyone suggest this instead of length? No. And unfortunatly this is a string prototype function which means it wont work on Arrays.

However...

As this is bad javascript there is a better way. And its name is Object.keys(). In JS Arrays and Strings are both objects which have keys. For both Arrays and strings, Their keys are indexes. So we can do something like this:

```JavaScript
  
  let a = +Object.keys('I am a text').pop();
  //this will give you the length of a string or array
  //NOTE THAT THESE ARE KEYS WHICH START AT 0, SO IT IS NECCESARY TO ADD 1 TO GET THE ACTUAL LENGTH 
 
```

How does this work? Object keys returns an array of keys [1,2,3,4] for each index of a String or Array. Array.prototype.pop() returns the last index of an array. then + makes sure the returned item is an integer.

## :cherries: Variable Names

Variable Names are the cherries on top of this giant stinking mess.

For some reason the javascript allow unicode gliphs for 'Identifiers'.

This means that you can use var \u0061 and var a interchangeably. Similarly, since var 1 is invalid, so is var \u0031.

To note we can use `\u{74}\u{65}\u{73}\u{74}` or `\u0074\u0065\u0073\u0074` formats.

Luckily some one was nice enough to create a nice little site that verifies if a unicode string is viable as an identifier [Here](https://mothereff.in/js-variables)

In the example bellow our variable can be call by either `aa` or `a\u{61}`. This is great for obfuscation!

```JavaScript

let a\u{61} = 'test';

console.log(a\u{61})
console.log(aa)

//we can also use gliphs like this 

let ಠ_ಠ = 6;

console.log(ಠ_ಠ)
// 6

//Numbers can be written as follows

let i = +('\u{31}\u{36}');
// i = 16

// You cannot use unicode as paramaters when declaring a function
const badFunc = (/0032) =>{

}
// We can however pass in unicode identifiers or refer to the paramaters by their unicode equivalant

let g = 'hello squirrel'

const gFunc = (s) =>{
 console.log(\u{73})
}

gFunc(\u{67})
```

Here is a Quick function to convert Text to Unicode using unicode~!.

```JavaScript 
const _tU =(s,o='',i=0)=>(!((+Object.keys(s).pop()+1)-i)?o:_tU(\u{73},\u{6f}+'\\u{'+s.charCodeAt(i).toString(+('\u{31}\u{36}'))+'}', \u{69}+1));
```

Quick aside about Emojis.

Currently we can use Emojis as Keys in objects because they are strings. 

```Javscript
let obj = {'🧡': 'hello'}

console.log(obj['🧡'])
// hello

```
