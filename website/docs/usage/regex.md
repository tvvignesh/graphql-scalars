---
id: regex
title: Using the RegularExpression Scalar Factory
sidebar_label: Generate your own scalars from regular expressions
---

### Using the RegularExpression scalar

First an explanation: To create a new scalar type to the GraphQL schema language, you must create an
instance of a new `GraphQLScalarType` object that implements three general functions/methods:
`serialize`, `parseValue` and `parseLiteral` which are used at different stages of processing your
GraphQL types during queries and mutations. So creating a new scalar looks like this:

```javascript
const MyScalar = new GraphQLScalarType({
    'MyScalar',

    description: 'A description of my scalar',

    serialize(value) {
      // ...
      return value;
    },

    parseValue(value) {
      // ...
      return value;
    },

    parseLiteral(ast) {
      // ...
      return ast.value;
    }
  });
```

Given this, if we want to create a new type that is essentially the same except for one little
customizable aspect (e.g., a regular expression type that has all the same code except the regex is
different) then we need to dynamically _generate_ a new `GraphQLScalarType` object given some
parameters. That's the approach we take here.

Therefore the `RegularExpression` scalar type is really a `GraphQLScalarType` object _generator_
that takes two arguments:

- a name
- the regex you want it to use

So to create a new scalar for a given regex, you will do this:

```javascript
const MyRegexType = new RegularExpression('MyRegexType', /^ABC$/);
```

Now `MyRegexType` is your new GraphQL scalar type that will enforce a value of, in this case, "ABC".

Add your new scalar type to your resolver map:

```javascript
export default {
  MyRegexType,
};
```

And to your schema:

```graphql
scalar MyRegexType
```

That's it. Now you can use `MyRegexType` as a type in the rest of your schema.

#### RegularExpression options

There is an optional third `options` argument to the RegularExpression constructor that can be used like this:

```javascript
const options = {
  errorMessage: (regex, value) => {
    if (process.env.NODE_ENV === 'production')
      return `Value is invalid format: ${value} `;
    else
      return `Value does not match the regular expression ${regex}: ${value}`;
  },
};

const MyRegexType = new RegularExpression('MyRegexType', /^ABC$/, options);
```
