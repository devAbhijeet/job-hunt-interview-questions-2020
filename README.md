<div align="center">
  <img height="60" src="https://raw.githubusercontent.com/devAbhijeet/devAbhijeet/master/assets/javascript.svg"> 
  <h1>Interview questions for front end</h1>
</div>
<span>
This readme is a compilation of all the question asked during my recent COVID-19 job hunt. I've also attached a list of resources that I'd referred for the preparations.

<br/>
<br/>

The questions are divided into following sections.
<ul align="left">
	<li>JS</li>
	<li>Coding</li>
	<li>Assignments</li>
	<li>Miscellaneous</li>
</ul>
</span>

### JS

1) Given a multidimensional array with depth of n, flatten it. Once flattened make it available as a method on `array` instance

<details><summary><b>Answer</b></summary>
<p>

```javascript
/**
 * [1,2,[3,4]] -> [1,2,3,4]
 */

let arr = [1,2,[3,4, [5,6, [7, [8, 9, 10]]]]]

function flatten(arr) {
  return arr.reduce(function(acc, next){
    let isArray =  Array.isArray(next)
    return acc.concat(isArray ? flatten(next) : next)
  }, [])
}

if (!Array.prototype.flatten) {
  Array.prototype.flatten = function() {
    return flatten(this)
  }
}
console.log(arr.flatten());
```

</p>
</details>

---

2) Create a promise from scratch

<details><summary><b>Answer</b></summary>
<p>

```javascript
class CustomPromise {
  state = "PENDING"
  value = undefined
  thenCallbacks = []
  errorCallbacks = []

  constructor(action) {
    action(this.resolver.bind(this), this.reject.bind(this))
  }

  resolver(value) {
    this.state = "RESOLVED"
    this.value = value
    this.thenCallbacks.forEach((callback) => {
      callback(this.value)
    })
  }

  reject(value) {
    this.state = "REJECTED"
    this.value = value
    this.errorCallbacks.forEach((callback) => {
      callback(this.value)
    })
  }

  then(callback) {
    this.thenCallbacks.push(callback)
    return this 
  }
  
  catch (callback) {
    this.errorCallbacks.push(callback)
    return this 
  }
}

let promise = new CustomPromise((resolver, reject) => {
  setTimeout(() => {
    const rand = Math.ceil(Math.random(1 * 1 + 6) * 6)
    if (rand > 2) {
      resolver("Success")
    } else {
      reject("Error")
    }
  }, 1000)
})

promise
  .then(function(response){
    console.log(response)
  })
  .catch(function(error){
    console.log(error)
  })
```

</p>
</details>

---

3) Filter movie list by average rating, name. Sort filtered list by any field inside movie object

<details><summary><b>Answer</b></summary>
<p>

```javascript
// O(M)
function getMovies() {
  return []; // [{id, name, year}]
}

// O(R)
function getRatings() {
  return []; // [{id, movie_id, rating}]   0 <= rating <= 10   // e.g 9.3
}

/**
 * minAvgRating ->
 *    avgRating >= minAvgRating
 *
 * sort ->
 *    name -> ascending order movies by name
 *   -name -> descending
 *
 *    avgRating
 * 
 *
 * search ->
 *   'ave' -> 'Avengers'
 *   'avengers' -> 'Avengers'
 *   'AvengersInfinitywar' -> 'Avengers'
 */
const toLower = str => str.toLocaleLowerCase()

const getAvrgRating = (movie, movingWithRatings) => {
  let count = 0;
  return movingWithRatings.reduce((acc, value, index) => {
    const movieMatch = movie.id === value.movie_id
    if (movieMatch) {
      acc+=value.rating
      count++
    }
    if (index === movingWithRatings.length - 1) {
      acc = acc/count
    }
    return acc
  }, 0)
}

const isSubString = (str1, str2) => {
  str1 = toLower(str1.split(" ").join(""))
  str2 = toLower(str2.split(" ").join(""))
  if (str1.length > str2.length) {
    return str1.startWith(str2)
  } else {
    return str2.startWith(str1)
  }
}

const moviesList = getMovies()
const movingWithRatings = getRatings();
function queryMovies({ search, sort, minAvgRating }) {
  let filteredMovies = movingWithRatings.filter(movie => getAvrgRating(movie, movingWithRatings) >= minAvgRating);
  filteredMovies = filteredMovies.map(movie => moviesList.filter(listItem => listItem.id === movie.movie_id).pop())
  filteredMovies = filteredMovies.filter(movie => isSubString(toLower(movie.name), toLower(search)))
  filteredMovies = filteredMovies.sort((a, b) => {
    const isDescending = sort[0] === '-' ? true : false
    let sortCopy = isDescending ? sort.slice(1) : sort
    const value1 = a[sortCopy]
    const value2 = b[sortCopy]
    if (isDescending) {
      return value1 > value2 ? -1 : 1
    }else {
      return value1 < value2 ? -1 : 1
    }
  })
  filteredMovies = filteredMovies.map(movie => ({
    ...movie,
    avgRating: movingWithRatings.filter(ratedMovie => ratedMovie.movie_id === movie.id)[0].rating
  }))
  return filteredMovies
}
```

</p>
</details>

---

4) Given an end point URL to fetch all the `posts` and `comments`. Do the following.

- Map all the comments to the posts it belongs to. The resultant data after mapping should be of below structure.

<details><summary><b>Answer</b></summary>
<p>

[Answer](https://github.com/devAbhijeet/cure-fit-interview-challenge/tree/master/)
```javascript

//service.js
const POSTS_URL = `https://jsonplaceholder.typicode.com/posts`;
const COMMENTS_URL = `https://jsonplaceholder.typicode.com/comments`;

export const fetchAllPosts = () => {
  return fetch(POSTS_URL).then(res => res.json());
};

export const fetchAllComments = () => {
  return fetch(COMMENTS_URL).then(res => res.json());
};


import { fetchAllPosts, fetchAllComments } from "./service";



const fetchData = async () => {
  const [posts, comments] = await Promise.all([
    fetchAllPosts(),
    fetchAllComments()
  ]);

  const grabAllCommentsForPost = postId =>
    comments.filter(comment => comment.postId === postId);

  const mappedPostWithComment = posts.reduce((acc, post) => {
    const allComments = grabAllCommentsForPost(post.id);
    acc[post.id] = allComments;
    return acc;
  }, {});

  console.log("mappedPostWithComment ", mappedPostWithComment);
};

fetchData();
```

</p>
</details>

---


5) Implement a method `getHashCode` on string instance. The method should be available on all strings.

<details><summary><b>Answer</b></summary>
<p>

```javascript
let s1 = "sample"

if (!String.prototype.getHashCode) {
  String.prototype.getHashCode = function(){
    console.log('String instance ', this)
    return this
  }
}
```

</p>
</details>

---

6) What does the below expressions evaluate to

```javascript
    1+true
    true+true
    ‘1’+true
    ‘2’ > ’3’
    ‘two’>’three’
```

<details><summary><b>Answer</b></summary>
<p>

```javascript
2
2
1true
false
true
```

</p>
</details>

---

7) Implement `bind` and `reduce`.

<details><summary><b>Answer</b></summary>
<p>

```javascript

//bind
if (!Function.prototype.bind) {
  Function.prototype.bind = function(...arg){
    const func = this
    const context = arg[0]
    const params = arg.slice(1)
    return function(...innerParam) {
      func.apply(context, [...params, ...innerParam])
    }
  }
}

//reduce
Array.prototype.reduce = function(func, initState) {
  const arr = this
  const callback = func
  let init = initState
  
  arr.forEach(function(value, index){
      init=callback(init, value)
  })
  return init
}
```

</p>
</details>

---

8) Implement debounce function

<details><summary><b>Answer</b></summary>
<p>

```javascript
const debounce = function(func, interval) {
  let timerId;
  return function(e){
    clearInterval(timerId)
    timer = setTimeout(function(){
      func.apply()
    }, interval)
  }
}
debounce(apiCall, 3000)
```

</p>
</details>

---

9) Implement throtlling function

<details><summary><b>Answer</b></summary>
<p>

```javascript
const throttle = (callback, interval) => {
  let timerId;
  let allowEvents = true;

  return function() {
    let context = this;
    let args = arguments;
    
    if (allowEvents) {
      callback.apply(context, args)
      allowEvents = false;
      timerId = setTimeOut(function(){
        allowEvents = true
      }, interval)
    }
  }
}
```

</p>
</details>

---

10) Design API polling mechanism. The API is called after a fixed interval. The API is a stock API that fetches the latest price of stock. Upon fetching the results, render the UI.

The question demands the design aspect of the solution and not the code. It was open ended question.

<details><summary><b>Answer</b></summary>
<p>

```javascript
//With setInterval, throttling and flags
setInterval=>Endpoint=>Render

//with the inversion of control
Endpoint=>Render=>setTimeout=>Endpoint=>Render=>SetTimeout...
```

</p>
</details>

---

11) Convert class based inheritance code given below to ES5 code.
```javascript
class Parent(name){
  constructor(name) {
    this.name=name
  }

  getName(){return this.name}
}

class Children extends Parent {
  constructor(props){
    super(props)
  }
}
```

<details><summary><b>Answer</b></summary>
<p>

```javascript
function Parent(name) {
  this.name = name
}

Parent.prototype.getName = function() {
  return this.name
}

function Children(name){
  Parent.call(this, name)
}
 
Children.prototype = new Parent()
```

</p>
</details>

---

12) What does following code evaluates to?
```javascript
//Q.1
var x = 1;
var y = x;

x = 0;
console.log(x, y);

//Q.2
var x = [1];
var y = x;

x = [];
console.log(x,y);

//Q.3
function Abc() { console.log(this); };
Abc()
new Abc();

//Q.4
var x = 1;
var obj = {
  x: 2,
  getX: function () {
    return console.log(this.x);
  }
};

obj.getX()
let a = obj.getX
console.log(a)

//Q.5
//How to get the a to log 2 in the above code

//Q.6
console.log("A");
setTimeout(() => console.log("B"), 0);
setTimeout(() => console.log("C"), 0);
console.log("D");

//Q.7
setTimeout(function() {
  console.log("A");
}, 0);
Promise.resolve().then(function() {
  console.log("B");
}).then(function() {
  console.log("C");
});

console.log("D");

//Q.8
let obj1 = {
  a:1,
  b:2
}

function mutate(obj) {
  obj = {a:4, c:6}
}

console.log(obj1)
mutate(obj1)
console.log(obj1)
```

<details><summary><b>Answer</b></summary>
<p>

```javascript
//A.1
0 1

//A.2
[] [1]

//A.3
window object is logged

//A.4
logs 2 and 1

//A.5
a.call(obj);

//A.6
A, D, B , C

//A.7
D, B, C, A

//A.8
{ a: 1, b: 2 }
{ a: 1, b: 2 }
```

</p>
</details>

---

13) Given an array of numbers implement the following
```javascript
const list = [1,2,3,4,5,6,7,8]
const filteredArray = list.filter(between(3, 6)) // [4,5]
```

<details><summary><b>Answer</b></summary>
<p>

```javascript
function between(start, end) {
  return function (value,index) {
    return value>start && value<end
  }
}
```

</p>
</details>

---

### Algorithms

1) Consider the following series:
```javascript
A := 1
B := A*2 + 2
C := B*2 + 3 and so on...
```
Write a program that:

outputs the number corresponding to a given letter

given a string of letters like 'GREP', computes the sum of the numbers corresponding to all the letters in the string (i.e., G + R + E + P), as given by the above series and

given a large number (that would fit into a standard 32-bit integer), finds the shortest string of letters corresponding to it.

You may use a greedy approach for the last part. Compute the values of the numbers corresponding to letters as and when required and DO NOT pre-compute beforehand and store them in a data structure.


<details><summary><b>Answer</b></summary>
<p>

```javascript
//A = 1
//B = A*2 +2 
//C = B*2+ 3
//D = C*2+ 3

var genCharArray = function(charA, charZ) {
    var a = [], i = charA.charCodeAt(0), j = charZ.charCodeAt(0);
    for (; i <= j; ++i) {
        a.push(String.fromCharCode(i));
    }
    return a;
}

var charMap = {};
var charArray = genCharArray('a', 'z');

charArray.forEach(function(char, index){
    charMap[char] = Number(index + 1);
});


var charSequence = function(char){
    if(typeof char==="string"){
        char = charMap[char];
    }
    if(char==1){
        return 1;
    }else{
        return char + 2 * charSequence(char-1);
    }
}

var input = process.argv[2];

if(input.length===1){
    console.log(charSequence(charMap[input]));
}else if(input.length>1){
    var charTotalSequence = input.split("").reduce(function(acc, curr){	
        return acc + charSequence(charMap[curr]);
    },0);
    console.log(charTotalSequence);
}
```

</p>
</details>

---

2) Given an array find a pair such that it sums to a given number

<details><summary><b>Answer</b></summary>
<p>

```javascript
let nums = [2, 7, 10, 1, 11, 15, 9]
let target = 11
let numsMap = new Map()
let pairs = nums.reduce((acc, num) => {
  let numToFind = target - num
  if (numsMap.get(numToFind)) {
    return [...acc, [num, numToFind]]
  } else {
    numsMap.set(num, true)
    return [...acc]
  }
}, [])

console.log("Pairs ", pairs)
```

</p>
</details>

---

3) Find the local maxima in a given array. A local maxima is a element that is greater than it's left and right neighbours. I provided a O(n) solution which was quite straight forward before going for optimisation.

<details><summary><b>Answer</b></summary>
<p>

```javascript
let x = [1, 2, 3, 5, 4] //Outputs: 5
if x.length == 1 return x[0]
else 
 let i = 1
 for(;i<x.length-1;i++){
  if x[i-1]<x[i] and x[i] > x[i+1] return x[i]
 }
 if x.length - 1 == i return x[i]

```

</p>
</details>

---

4) Rotate a matrix clockwise by 90 degree. The solution should be in place.

[leetcode](https://leetcode.com/problems/rotate-image/)

<details><summary><b>Answer</b></summary>
<p>

```javascript
[
 [1, 2, 3],
 [4, 5, 6],
 [7, 8, 9]
]
//The solution is to first take the transpose of the matrix.
//After taking the transpose the resulting matrix is as follows.
[
 [1, 4, 7],
 [2, 5, 8],
 [3, 6, 9]
]
//After the transpose step, All we have to do is to reverse the array @ each entry.
//The resulting matrix after after reversal is as follows.
[
 [7, 4, 1],
 [8, 5, 2],
 [9, 6, 3]
]
//The above matrix is rotated 90 degree
```

</p>
</details>

---

5) Maximum subarray sum modulo m

<details><summary><b>Answer</b></summary>
<p>

[Answer](https://www.geeksforgeeks.org/maximum-subarray-sum-modulo-m/)

</p>
</details>

---

6) Given an array find three element in array that sum to a given target

<details><summary><b>Answer</b></summary>
<p>

```javascript
let x = [1, 2, 3, 4, 5]
let target = 7
let found = []

const twoPointer = (l ,r, current) => {
  while(l<r){
    const totalSum = current + x[l] + x[r]
    if (totalSum === target) {
      found.push([current, x[l], x[r]])
      return
    } else if (totalSum > target) {
      r--
    } else {
      l++
    }
  }
}

const threeSum = (x, target) => {
    for (let i=0;i<x.length;i++) {
      const current = x[i];
      let leftPointer = i+1
      let rightPointer = x.length - 1
  
      if (current+x[leftPointer]+x[rightPointer] === target) {
        found.push([current, x[leftPointer], x[rightPointer]])
      } else {
        twoPointer(leftPointer, rightPointer, current)
      }
  }
  return found
}
```

</p>
</details>

---

7) Given a string and an integer k, find number of substrings in which all the different characters occurs exactly k times.

[link](https://www.geeksforgeeks.org/number-substrings-count-character-k/)

<details><summary><b>Answer</b></summary>
<p>

```javascript
const subStrHasSameCharCount = (str, startIndex, endIndex, totalHop) => {
  let charMap = {}
  for (let k=startIndex;k<endIndex;k++) {
    let currentChar = str[k]
    if (charMap[currentChar]) {
      charMap[currentChar]++
    } else {
      charMap[currentChar] = 1
    }
  }
  let totalCount = Object.values(charMap).length > 0
  return totalCount ? Object.values(charMap).every(item => item == totalHop) : false
}


const characterWithCountK = (str, k) => {
  if (k == 0) return ''
  let count = 0
  let initialHop = k
  while (initialHop < str.length) {
    for (let j=0;j<str.length;j++) {
      let startIndex = j
      let endIndex = j + initialHop
      if(endIndex > str.length) continue
      count = subStrHasSameCharCount(str, startIndex, endIndex, k)
        ? count + 1: count
    }
    initialHop+=k
  }
  count = subStrHasSameCharCount(str, 0, initialHop, k)
        ? count + 1: count
  return count
}


let str = 'aabbcc'
let k = 2
console.log(characterWithCountK(str, k))
```

</p>
</details>

---

8) Given two input strings, s1 and s2, containing characters from a-z in different orders, find if rearranging string in s1 results in a string that is equal to s2.

<details><summary><b>Answer</b></summary>
<p>

```javascript
let s1 = 'dadbcbc'
let s2 = 'ccbbdad'
let charMap = {}

const canBeRearranged = (s1, s2) => {
  if(s1.length!==s2.length){
    return false
  }
  for(let i=0;i<s1.length;i++){
    const charFromString1 = s1[i]
    const charFromString2 = s2[i]
    if(charFromString1 in charMap){
      charMap[charFromString1]++
    } else {
      charMap[charFromString1] = 1
    }
    if(charFromString2 in charMap){
      charMap[charFromString2]--
    } else {
      charMap[charFromString2] = -1
    }
  }
  for(let x in charMap){
    if (charMap[x]!==0){
      return false
    }
  }
  return true
}

canBeRearranged(s1, s2)
```

</p>
</details>

---

9) Given an array or variable input size, write a function to shuffle the array.

<details><summary><b>Answer</b></summary>
<p>

```javascript
const swap = (index1, index2, arr) => {
  let temp = arr[index1]
  arr[index1] = arr[index2]
  arr[index2] = temp
}

const shuffle = (arr) => {
  let totalLength = arr.length
  while(totalLength > 0) {
    let random = Math.floor(Math.random() * totalLength)
    totalLength--
    swap(totalLength, random, arr)
  }
  return arr
}


let arr = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
arr = shuffle(arr)
```

</p>
</details>

---

10) Calculate the sum of all elements in a multidimensional array of infinite depth.

<details><summary><b>Answer</b></summary>
<p>

```javascript
let arr = [4, 5, 7, 8, [5, 7, 9, [3, 5, 7]]]
let sum = 0

const calculateSum = (arr) => {
  arr.reduce(function(acc, currentVal) {
    const isEntryArray = Array.isArray(currentVal)
    if (isEntryArray) {
      return acc.concat(calculateSum(currentVal))
    } else {
      sum+=currentVal
      return acc.concat(currentVal)
    }
  }, [])
}
calculateSum(arr)
console.log(sum)
```

</p>
</details>

---

11) Flatten a nested object of varying debt.

<details><summary><b>Answer</b></summary>
<p>

```javascript
const obj = {
  level1: {
    level2: {
      level3: {
        more: 'stuff', 
        other: 'otherz',
        level4: {
          the: 'end',
        },
      },
    },
    level2still: {
      last: 'one',
    },
    am: 'bored',
  },
  more: 'what',
  ipsum: {
    lorem: 'latin',
  },
};

var removeNesting = function(obj, parent){
  for (let key in obj){
    if (typeof obj[key] === "object") {
      removeNesting(obj[key], parent+"."+key)
    } else {
      flattenedObj[parent+'.'+key] = obj[key]
    }
  }
}

let flattenedObj = {}
const sample = removeNesting(obj, "");
console.log(flattenedObj);
```

</p>
</details>

---

12) Given a json input, where each entry represents a directory such that each directory in turn can have a nested entry of it's own. Create the resulting directory structure.

<details><summary><b>Answer</b></summary>
<p>

[Answer](https://jsbin.com/gajiweq/1/edit?js,console)

</p>
</details>

---

13) Given an array of object containing list of employee data such that each employee has list of reportee. Use this information to construct a hirerachy of employees.

<details><summary><b>Answer</b></summary>
<p>

```javascript
const employeesData = [{
  id: 2,
  name: 'Abhishek (CTO)',
  reportees: [6] 
}, {
  id: 3,
  name: 'Abhiram (COO)',
  reportees: []
}, {
  id: 6,
  name: 'Abhimanyu (Engineering Manager)',
  reportees: [9] 
}, {
  id: 9,
  name: 'Abhinav (Senior Engineer)',
  reportees: []
}, {
  id: 10,
  name: 'Abhijeet (CEO)',
  reportees: [2, 3],
}];

/*
A (CEO)
----B (CTO)
--------D (Engineering Manager)
------------E (Senior Software Engineer)
----C (COO)
*/

const findCeo = (currentEmp) => {
  let parentEmployee = employeesData.filter(emp => emp.reportees.indexOf(currentEmp.id) > -1)
  if (parentEmployee && parentEmployee.length > 0) {
    return findCeo(parentEmployee[0])
  } else {
    return currentEmp
  }
}

const logHierarchy = (currentEmp, indent) => {
  console.log("-".repeat(indent) + currentEmp.name)
  indent+=4;
  for(let i=0;i <currentEmp.reportees.length;i++) {
    let employee = employeesData.filter(emp => emp.id === currentEmp.reportees[i])
    logHierarchy(employee[0], indent)
  }
}

const traverse = (employee) => {
  let ceo = findCeo(employee)
  logHierarchy(ceo, 0)
}

traverse(employeesData[0])
```

</p>
</details>

---

14) Print a given matrix in spiral form
```javascript
const inputMatrix = [
  [1, 2, 3, 4,  5],
  [6, 7, 8, 9, 10],
  [11,12,13,14,15],
  [16,17,18,19,20],
]

const exprectOutput = [1,2,3,4,5,10,15,20,19,18,17,16,11,6,7,8,9,14,13,12]
```

<details><summary><b>Answer</b></summary>
<p>

```javascript
function spiralParser(inputMatrix){
  const output = [];
  let rows = inputMatrix.length;
  let cols = rows > 0 ? inputMatrix[0].length : 0;
  
  //singleEmptyRow => Edge case 1 //[]
  if (rows === 0) {
    return []
  }
  
  if (rows === 1) {
    //singleElementRowNoCol => Edge case 2 //[[]]
    if (cols === 0) {
      return []
    } else if (cols === 1){
      //singleElementRow => Edge case 3 //[[1]]
      output.push(inputMatrix[0][0])
      return output 
    }
  }
  
  let top = 0;
  let bottom = rows - 1;
  let left = 0;
  let right = cols - 1;
  let direction = 0;
  //0 => left->right
  //1 => top->bottom
  //2 => right->left
  //3 => bottom->top
  
  while(left <= right && top <= bottom) {
    if(direction === 0) {
      //left->right
      for (let i=left; i<=right;i++) {
        output.push(inputMatrix[top][i])
      }
      top++;
    } else if (direction === 1) {
      //top->bottom
      for (let i=top; i<=bottom;i++) {
        output.push(inputMatrix[i][right])
      }
      right--
    } else if (direction === 2) {
      //right->left
      for (let i=right; i>=left;i--) {
        output.push(inputMatrix[bottom][i])
      }
      bottom--
    } else if (direction === 3) {
      //bottom->top
      for (let i=bottom; i>=top;i--) {
        output.push(inputMatrix[i][left])
      }
      left++
    }
    direction = (direction + 1) % 4
  }
  return output;
}

console.log(spiralParser(inputMatrix2))
```

</p>
</details>

---

15) Find maximum consecutive repeating char in a give string.
```javascript
let str = 'bbbaaaaccadd'; //max repeating char is a with count 4
```

<details><summary><b>Answer</b></summary>
<p>

```javascript
//sudo code
maxNow = if input string length is 1 or greater than 1 ? 1 : 0
maxOverall = if input string length is 1 or greater than 1 ? 1 : 0

for char in inputString starting from index 1
  if char equals prevChar
    maxNow++
    maxOverall = max(maxOverall, maxNow)
  else if char not equals prevChar    
    maxNow = 1

```

</p>
</details>

---

16) Given a input array of varying length, segregate all the 2's at the end of the array.
```javascript
let inputArr = [2,9,1,5,2,3,1,2,7,4,3,8,29,2,4,6,54,32,2,100]
//ouput => [9,1,5,3,1,7,4,3,8,29,4,6,54,32,100,2,2,2,2,2]
```

<details><summary><b>Answer</b></summary>
<p>

```javascript
let slowRunner = 0

for (let fastRunner=0;fastRunner<arr.length;fastRunner++) {
  if (arr[fastRunner]!==2 && arr[slow] == 2) {
    [arr[fastRunner], arr[slow]] = [arr[slow], arr[fastRunner]]
    slowRunner++
  }
}
```

</p>
</details>

---

17) Reverse a linked list
```javascript
//Input = 1 -> 2 -> 3 -> 4 -> 5 -> 6
//Output = 1 <- 2 <- 3 <- 4 <- 5 <- 6
```


<details><summary><b>Answer</b></summary>
<p>

```javascript
//sudo code
let current = head
let prev = null
let next = null

while(current) {
  next = current.next
  current.next = prev
  prev = current
  current = next
}

```

</p>
</details>

---

18) preorder tree traversal using iteration (No recurssion)

<details><summary><b>Answer</b></summary>
<p>

```javascript
//sudo code
const preorder = (root) => {
  let stack = []
  stack.push(root)

  while(there is element in stack) {
    let current = stack.pop()
    console.log(current.value)
    if (current.right) {
      stack.push(current.right)
    }
    if (current.left) {
      stack.push(current.left)
    }
  }
}
```

</p>
</details>

---

### Assignments

1) Design a parking lot system with following requirements:
- It can hold up to N vehicles. Handle availability of parking lot.
- Entry and exit log of vehicles.
- Automated ticketing system for every vehicle entering/exiting the parking lot will have
vehicle registration with vehicle details like: Registration No., Color, allocated parking
slot.

I should be able to query:
- Registration No. of all vehicles of a particular color.
- Parking slot of a vehicle given registration no.
- Parking slots for vehicles given a color.
- List of available slots in the parking lot.

Requirements:
- Can use anything to structure the code: Classes/Structs.
- Your solution should be extendable for future use cases.

Few code design principles:
- Modularity of code.
- Naming conventions.
- SOLID principles.

<details><summary><b>Answer</b></summary>
<p>

[Answer](https://github.com/devAbhijeet/parking-lot-design-js)

</p>
</details>

---

2) Create a react component `Ping` that makes an API call to a given URL. If the API calls returns status code 200 that means the user is online. However, if the API call receives status code other than 200 it means, the user is offline.

> Try changing `status` form dev tools network panel

<details><summary><b>Answer</b></summary>
<p>

[Answer](https://codesandbox.io/s/admiring-davinci-xnjef)

</p>
</details>

---

3) Create a dynamic form builder from a `json` input. The form can be grouped based on `id`. Each group can have a nested group of it's own.

<details><summary><b>Answer</b></summary>
<p>

[Answer](https://codesandbox.io/s/great-noyce-75kup)

</p>
</details>

---

4) Create a minimal excel sheet in pure javascript that supports `adding` and `removing` rows, columns. There was time limit on this question of 40 minutes.

<details><summary><b>Answer</b></summary>
<p>

[Answer](https://codesandbox.io/s/smoosh-thunder-krv8m)

</p>
</details>

---

5) You have to make a search input box which will search over a list of users.

The user object has the following fields
```javascript
   id: a unique id
   name: user’s name
   items: list of items ordered by user
   address: address of the user
   pincode: user address pin code
```
You have to implement search on all of the fields.

The search results will show up as a list of User Cards.

To Summarize
On typing in the search input box, the search results list opens up. The search could be just a string matching search.

The list of cards can be navigated through keyboard or mouse
only one card should highlight at a time if both mouse and keyboard are used for navigation

(keyboard will take preference if mouse is kept hovered on the list, similarly mouse will take preference if keyboard navigation is not used).

This behaviour is similar to how youtube search works

When no search results are found, an empty card is displayed
The card list would be scrollable.

The highlighted card (via keyboard/mouse) will scroll into view

<details><summary><b>Answer</b></summary>
<p>

[Answer](https://codesandbox.io/s/silly-moon-31m7u)

</p>
</details>

### Miscellaneous

1) How would you architect a front end application [click](https://dev.to/vycke/how-to-create-a-scalable-and-maintainable-front-end-architecture-4f47)
2) Implement Lazy loading [click](https://css-tricks.com/the-complete-guide-to-lazy-loading-images/)
3) What is Server Side Rendering.
4) How to deploy a react app to production.
5) What are service worker/web worker.
6) How to optimise a web app and make it more performant.
7) Explain different type of client side cache strategies.
8) What is CORS.
9) What are higher order component in react.
10) How does connect function work in redux.
11) What are pure components in React.
12) Difference between __proto__ and prototype
13) Difference between inline vs inline block vs block
14) Difference between flex and grid layout
15) Different positioning system in CSS
16) Specificity and selectors priority in CSS
17) Difference between display none vs visibility hidden vs opacity

### Resources
[link](https://github.com/devAbhijeet/learning-resources)
