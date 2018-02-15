---
title: "Sequential Execution in asynchronous Javascript"
date: 2018-02-09T23:00:20+05:30
draft: false
---

Getting sequential tasks done in `callback` based asynchronous code is hard. Let us assume 2 I/O intensive tasks, `eat` and `sleep`.
Consider the following snippet 
```javascript

eat(function(err){
         console.log('Bob ate');
});

drink(function(err){
         console.log('Bob drank');
});

```

The output of the above snippet can depend on the I/O associated with both eating  and sleeping. Notice that we have only defined the callback function for these tasks and not the task itself. The functions eat and sleep tasks are defined elsewhere, but they are guaranteed to execute the callback after the task is completed. In the sequential world this looks like,

```javascript
eat();
console.log('Bob ate');
drink();
console.log('Bob drank');


```
The trouble with the callback based approach is that there is no guarantee that Bob will eat before he sleeps. Depending on the current state of the system, bob can complete drinking first or bob can complete eating first. Let us try to serialize the tasks eat and drink

### Plain Javascript ###

```javascript
var tasks = [eat, drink];

function iterate(count){
  if (count == tasks.length)
    return;
  else{
    //You could bind variables too
    tasks[count](function(err){
       if(err){
         //handle error
         //return if if you dont want the tasks to continue
       }
       iterate(count + 1);
    });
   }
}
iterate(0);
```

### async library ###




```javascript
var tasks = [eat, drink]

async.series(tasks); //as simple as that

```

#### Apply a function on a collection sequentially ####

```javascript
var data = [meat, carne, fleish]
async.eachSeries(data, eat);
```

### Promisify the function ###

```javascript
const eatAsync = new Promise((resolve,reject) => {
    eat(function(err){
      if(err){
        reject(err);
        return;
      }
      resolve();
    });
}

const drinkAsync = new Promise((resolve,reject) => {
    drink(function(err){
      if(err){
        reject(err);
        return;
      }
      resolve();
    });
}

eat().then(sleep())
```

#### Async/Await ####

```javascript
await eat();
await sleep()
```