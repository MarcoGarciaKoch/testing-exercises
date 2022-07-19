# Testing Exercises

In this file I´m going to explain the testing exercises I did during my Full Stack Development Bootcamp.
Even do I was not part of the subjects, our Head Teacher agreed with us that we would have an introduction to testing if we had time. 
Luckily I had the opportunity to practice **unitary testing**. 

One of my main goals now is to continue learning and implementing more ans more testing in my developments.

I practiced with 2 different libraries:

- **Jest**
- **Supertest**

For each exercise I will use at least 2 different files, the one that contains the functions to test and the file that contains the testing environment.


# Configuration

First I install all the necessary libraries to prepare the environment:

>   Install `jest` using the following `npm i -D jest`
  
As Jest comes pre-configured for CJS by default and I want to configure it for ESM, I do the following:

>   Install `npm install -D cross-env` for the environment variables

In the Package.json I need to make sure that we have both `jest` and `cross-env` in the devDependencies:

    ```js
    "devDependencies": {
        "cross-env": "^7.0.3",
        "jest": "^28.1.0"
    }
    ```

Change the test script in the package.json as follows:

    ```json
     "scripts": {
            "test": "cross-env NODE_OPTIONS=--experimental-vm-modules jest --watch --coverage"
        }
    ```

>   Install `supertest` using the following `npm install supertest --save-dev`

Now the environment is set up to do the exercises.


# Methodology

During the testing phase, I follow the next steps:
    - Phase 1: Prepare the testing environment
    - Phase 2: Execute the code to test
    - Phase 3: Check the result (output)   


# Exercise 1

```js
// main.js file

// Function that add the array of numbers we pass as parameter

export const add = (...numbers) => {
    if(numbers.some(n => typeof n !== 'number')) return NaN;
    return numbers.reduce((acc,v) => acc+v, 0);
}


// main.spec.js file

// Testing cases for the add function

import { add } from './main.js';

it('Given two numbers returns its add', () => {
    // phase 1
    const a = 4;
    const b = 44;
    // phase 2
    const result = add(a,b);
    // phase 3
    expect(result).toBe(48);
});

it('Given two negative numbers returns a negative num with its add', () => {
    // phase 1
    const a = -10;
    const b = -11;
    // phase 2
    const r = add(a,b);
    // phase 3
    expect(r).toBeLessThan(0);
    expect(r).toBe(-21);
});

it('Given a not number param should return NaN', () => {
    // phase 1
    const a = 'hola';
    const b = 44;
    // phase 2
    const r = add(a,b);
    // phase 3
    expect(r).toBeNaN();
});

it('Given more than two params should return its add', () => {
    // phase 1
    const a=4,b=5,c=10, d=14;
    // phase 2
    const r = add(a,b,c,d);
    // phase 3
    expect(r).toBe(33);
});

it('Given more than two params and one of them is not a Number should return NaN', () => {
    // phase 1
    const a=true,b=5,c=10, d=14;
    // phase 2
    const r = add(a,b,c,d);
    // phase 3
    expect(r).toBeNaN();
});

```


# Exercise 2

```js
// main.js file

// Function that checks if a string is a palindrome

export const palindrome = str => {
    const processedStr = str.toLowerCase()
                            .replaceAll(' ', '')
                            .replaceAll(/á|à|â|ä/g, 'a')
                            .replaceAll(/ó|ò|ô|ö/g, 'o')
                            .replaceAll(/í|ì|î|ï/g, 'i')
                            .replaceAll(/é|è|ê|ë/g, 'e')
                            .replaceAll(/ú|ù|û|ü/g, 'u');
    const reverseStr = processedStr.split('').reverse().join('');
    return reverseStr === processedStr;
}


// main.spec.js file

// Testing cases for the add function

import { palindrome } from './main.js';

it('Given "radar" string should be palidrome', () => {
    // phase 1
    const string = 'radar';
    // phase 2
    const r = palindrome(string);
    // phase 3
    expect(r).toBe(true);
});

it('Given "hola" string should not be palidrome', () => {
    // phase 1
    const string = 'hola';
    // phase 2
    const r = palindrome(string);
    // phase 3
    expect(r).toBe(false);
});


it('Given a palindrome sentence with uppercase an accent should detect the palidrome', () => {
    // phase 1
    const string = 'Dábale arroz a la zorra el abad';
    // phase 2
    const r = palindrome(string);
    // phase 3
    expect(r).toBe(true);
});

```


# Exercise 3

In this exercise I test some backend functions and I group them under **describe** and use one of the 4 special functions available **afterEach**.

```js
// main.js file

// Set of functions that write, read and remove the hello.txt file

const FILE_PATH = './hello.txt';

export const writeHelloInFile = async (string) => {
    await fs.writeFile(FILE_PATH, string);
}

export const readHelloFromFile = async () => {
    try {
        const d = await fs.readFile(FILE_PATH, { encoding: 'utf-8'});
        return d;
    }catch(err){
        return 'Not Found';
    }
}

export const removeHello = async () => {
    await fs.rm(FILE_PATH);
}


// main.spec.js file

// Testing cases for the set of functions

import { writeHelloInFile, readHelloFromFile, removeHello } from './main.js';

describe('Demo File System' , () =>{

    afterEach(async () => {
        // After each testing case, I delete the file to make sure it does not exist 
        try {
            await removeHello()
        }catch(err){}
        
    });

    it('Calling writeHelloInFile it should write "Hello" in the hello.txt file', async () => {
        //phase 1
        const string = 'hello';
        //phase 2
        await writeHelloInFile(string);
        const r = await readHelloFromFile();
        //phase 3
        expect(r).toBe('Hello');
    })
    
    it('Reading hello.txt Given a hello.txt file into os fs, "Hello" should be returned', async () => {
        //phase 1
        const string = 'hello';
        //phase 2
        await writeHelloInFile(string); // I write to make sure precondition is met
        const r = await readHelloFromFile();
        //phase 3
        expect(r).toBe('Hello');
    })
    
    it('Reading hello.txt, When no hello.txt file in os, "Not Found" should be returned', async () => {
        //phase 2
        const r = await readHelloFromFile();
        //phase 3
        expect(r).toBe('Not Found');
    })
});

```


# Exercise 4

In this exercise I´m going to test Express.js (backend)

For that, I prepare the following environment:

1. I take the **listen** method of the app.js to a different file called **index.js** used to initialize.
2. When I do **const app = express()** I add **export** at the beginning.
3. Import **supertest** and **app.js** file on the **index.js** file.
5. Create the testing file **app.spec.js**.
6. Using **describe** I group each entity or collection.
7. Make as many **it** as testing cases there are.
 

### app.js file

```js
import express from 'express';

export const app = express();

app.use(express.json()) // for parsing application/json

// route definition
app.get('/example-json', (req, res) => {
    // send json to client
    res.json({ demo:'hello JSON'}); 
});

app.get('/example-status', (req, res) => {
    // send status code before sending the answer to client
    res.status(412);
    res.send('This is a 412 error'); 
});

app.get('/example-headers', (req, res) => {
    // send headers before sending the answer to client
    res.set('x-custom-header', 'hello world custom header');
    res.send('This is an answer with a custom headers'); 
});


app.post('/example-post', (req, res) => {
    console.log(req.body); // to get the request body
    res.send('This is what I´m returning in the post'); 
});

/**
 * If I need a variable route to operate with a specific element, I use path params,
 * a define variable in the route that I can get the value from the express route handler. 
 */
app.get('/example-path-params/:id', (req, res) => {
    console.log(req.params); // to get the request path params
    res.send('You requested info about the ID: ' + req.params.id);
});

app.get('/example-path-params-double/:id/other/:name', (req, res) => {
    console.log(req.params); // to get the request path params
    const {id, name} = req.params; // deconstruction of params object
    res.send(`You requested info about the ID: ${id}
              You requested info about the name: ${name}`);
});

// to delete
app.delete('/example-path-params/:id', (req, res) => {
    console.log(req.params); // esto es para obtener los path params de la petición
    res.send(`You requested to delete the ID: ${req.params.id}`);
});
```


### index.js file

```js
import {app} from './app.js';
import supertest from 'supertest';


// Initialize the server
app.listen(4563, () => {
    // This is the function that executes when the server is initilized
    console.log('Server on port 4563');
});
```


### app.spec.js file

```js
// After every it, pass `done` function to finish the asynchronous test
// Call the function and pass the app.js file --> supertest(app)
// After that I get an object to which I can apply methods
// After the methods we call the expect
// Finally we add and end() to which I pass `done` to finish the test

import supertest from 'supertest';
import {app} from './app.js';

describe('Demo Express Testing' , () => {

    it('When GET on example-json path is called it should return "hello JSON"', done => {
        supertest(app)
            .get('/example-json')
            .expect(200)
            .expect('Content-Type', /json/) //regular expression to verify that content-type is json
            .expect({demo:'hello JSON'})
            .end(done);
    });

    it('When GET on example-status path is called it should return 412', done => {
        supertest(app)
            .get('/example-status')
            .expect(412)
            .expect('Content-Type', /text/) //regular expression to verify that content-type is text
            .expect('This is an error 412')
            .end(done);
    });

    it('When GET on example-headers path is called it should return "hello world custom header"', done => {
        supertest(app)
            .get('/example-headers')
            .expect(200)
            .expect('x-custom-header', 'hello world custom header')
            .expect('Content-Type', /text/) //regular expression to verify that content-type is text
            .expect('This is an answer with a custom headers')
            .end(done);
    });

    it('When POST on example-post path is called it should return "This is what I´m returning in the post"', done => {
        supertest(app)
            .post('/example-post')
            .send({demo:'hello POST'})
            .expect(200)
            .expect('Content-Type', /text/) //regular expression to verify that content-type is text
            .expect('This is what I´m returning in the post')
            .end(done);
    });

    it('When GET on /example-path-params/:id path is called with an id it should return "You requested info about the ID :id"', done => {
        supertest(app)
            .get('/example-path-params/1')
            .expect(200)
            .expect('Content-Type', /text/) //regular expression to verify that content-type is text
            .expect('You requested info about the ID: 1')
            .end(done);
    });
});

```
