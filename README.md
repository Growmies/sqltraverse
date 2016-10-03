<p align="center">
  <a name="brand" href="#">
    <img src="./docs/sql_traverse_logo.png">
  </a>
</p>

<p align="center">
  <b><a href="#overview">Overview</a></b>
  |
  <b><a href="#features">Features</a></b>
  |
  <b><a href="#installation">Installation</a></b>
  |
  <b><a href="#credits">Credits</a></b>
  |
  <b><a href="#issues">Issues</a></b>
</p>

<br>

<p align="center">
  <a href="https://sqlast.herokuapp.com/"> 
    <img src="https://sqlast.herokuapp.com/badge.svg" alt=" "> 
  </a>
  <a href="https://travis-ci.org/jdrew1303/sqltraverse"> 
    <img src="https://img.shields.io/travis/jdrew1303/sqltraverse.svg?style=flat-square" alt=" "> 
  </a>
  <a href="./LICENSE"> 
    <img src="http://img.shields.io/badge/license-BSD%202%20Clause-blue.svg?style=flat-square" alt=" "> 
  </a>
  <a href=""> 
    <img src="https://img.shields.io/badge/platform-Browser%20%7C%20Node.js-808080.svg?style=flat-square" alt=" "> 
  </a>
  <a href="https://travis-ci.org/jdrew1303/sqltraverse"> 
    <img src="https://img.shields.io/badge/documentation-below-green.svg?style=flat-square" alt=" "> 
  </a>
</p>

## Overview

SQLTraverse is AST (abstract syntax tree) walker. This allows you to move through the AST generated by the [Codeschool sqlite-parser](https://github.com/codeschool/sqlite-parser/) in a structured manner. If you've ever used Estraverse before it works in the exact same way (in fact we use Estraverse under the hood).

<p align="right"><a href="#top">:arrow_up:</a></p>

## Features
The following code will do a simple walk of the AST passed to it. We can take actions either when we enter the node or when we leave the node.

```javascript
sqltraverse.traverse(ast, {
    enter: function (node, parent) {
        if (node.type == 'FunctionExpression' || node.type == 'FunctionDeclaration')
            return sqltraverse.VisitorOption.Skip;
    },
    leave: function (node, parent) {
        if (node.type == 'VariableDeclarator')
          console.log(node.id.name);
    }
});
```

We can use `this.skip`, `this.remove` and `this.break` functions instead of using Skip, Remove and Break.

```javascript
sqltraverse.traverse(ast, {
    enter: function (node) {
        this.break();
    }
});
```

And sqltraverse provides `sqltraverse.replace` function. When returning node from `enter`/`leave`, current node is replaced with it.

```javascript
result = sqltraverse.replace(tree, {
    enter: function (node) {
        // Replace it with replaced.
        if (node.type === 'Literal')
            return replaced;
    }
});
```

By passing `visitor.keys` mapping, we can extend sqltraverse traversing functionality.

```javascript
// This tree contains a user-defined `TestExpression` node.
var tree = {
    type: 'TestExpression',

    // This 'argument' is the property containing the other **node**.
    argument: {
        type: 'Literal',
        value: 20
    },

    // This 'extended' is the property not containing the other **node**.
    extended: true
};
sqltraverse.traverse(tree, {
    enter: function (node) { },

    // Extending the existing traversing rules.
    keys: {
        // TargetNodeName: [ 'keys', 'containing', 'the', 'other', '**node**' ]
        TestExpression: ['argument']
    }
});
```

By passing `visitor.fallback` option, we can control the behavior when encountering unknown nodes.

```javascript
// This tree contains a user-defined `TestExpression` node.
var tree = {
    type: 'TestExpression',

    // This 'argument' is the property containing the other **node**.
    argument: {
        type: 'Literal',
        value: 20
    },

    // This 'extended' is the property not containing the other **node**.
    extended: true
};
sqltraverse.traverse(tree, {
    enter: function (node) { },

    // Iterating the child **nodes** of unknown nodes.
    fallback: 'iteration'
});
```

When `visitor.fallback` is a function, we can determine which keys to visit on each node.

```javascript
// This tree contains a user-defined `TestExpression` node.
var tree = {
    type: 'TestExpression',

    // This 'argument' is the property containing the other **node**.
    argument: {
        type: 'Literal',
        value: 20
    },

    // This 'extended' is the property not containing the other **node**.
    extended: true
};
sqltraverse.traverse(tree, {
    enter: function (node) { },

    // Skip the `argument` property of each node
    fallback: function(node) {
        return Object.keys(node).filter(function(key) {
            return key !== 'argument';
        });
    }
});
```
<p align="right"><a href="#top">:arrow_up:</a></p>

## Installation

<p align="right"><a href="#top">:arrow_up:</a></p>

### Using this project

<p align="right"><a href="#top">:arrow_up:</a></p>

## Credits

[![jdrew1303](https://avatars0.githubusercontent.com/u/2535432?v=3&s=40)](https://twitter.com/intent/follow?screen_name=j_drew1303 "Follow @j_drew1303 on Twitter")

Copyright (c) 2016

## Issues
- Tests, tests and more tests.
- Documentation currently under construction (The examples need to be worked through for SQL instead of JavaScript).
- AST is currently in a state of flux for some node types. We also probably need a builder for these nodes (another project).
- You need to pass in `ast.statement` and not just the raw `ast` given back from the parser. (This is an issue with the base node not having a type.)

<p align="right"><a href="#top">:arrow_up:</a></p>