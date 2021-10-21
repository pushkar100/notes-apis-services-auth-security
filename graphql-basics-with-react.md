# GraphQL

[Udemy: Complete GraphQL with React by Stephen Grider](https://www.udemy.com/course/graphql-with-react-course)

## GraphQL vs RESTful APIs

RESTful APIs are built using HTTP methods that have conventions on how to fetch or manipulate data. A simple RESTful API with the general conventions might look like this:

- `/users`  (POST): Creates a user
- `/users`, (GET): Fetches all users
- `/users/:id` (GET): Fetches a user with an id
- `users/:id` (PUT): Updates a user record
- `users/:id` (DELETE): Deletes a user record

When you wish to populate more data in your database, it makes sense to organize it into different tables if your data has a lot of relations to manage within itself. Ex: Users can be a part of a company and have a position in it. For this purpose, you would maintain a set of companies and a set of positions and use keys to point to a user's company & position. This adds some complexity known as nesting.

**REST APIs are good if there isn't too much nesting.**

When nesting increases, REST APIs tend to become very complex.
Ex: Users have friends who are also users (who work for companies and have posts). How would you fetch the position and a company of a friend of a user?

1. **Multiple HTTP requests (too many):** 
	- `users/4/friends` to get friends and their user IDs
	- `users/6` to fetch the user record of a friend
	- `users/6/company` to fetch the company the friend works for
	- `users/6/position` to fetch the position the friend occupies in the company
		- Complex & additional N/W requests each time, increasing the latency 
2. **Overly customized requests that are tightly coupled to frontend requirement & break RESTful conventions**
	- `/users/4/friends/companies_and_positions`
3. **Over fetching data**: This might also fetch **more data than what is required** for the client application

GraphQL helps avoid these shortcomings of RESTful APIs

## GraphQL introduction

GraphQL philosophy is to visualize data as a graph i.e objects as nodes and their relations being the edges. These connections, once known, makes it easy to go and fetch data from different objects that are related to each other.

Incorporating graphQL does not require changing your database schema or architecture.

We use graphQL to organize data into a **graph** & query it! Querying is also termed as "walking along the graph".

An example query:
```
{
	user(id: "10") {
    id,
    firstname,
    age
  }
}
```

**GraphiQL** is a development tool that comes with the graphql library that helps you make requests alongside your graphQL development server. It is useful to write queries and debug your application. It also has a documentation panel to the right when you open up the graphql server in a browser.

### Express & GraphQL setup

We can use GraphQL to take care of some of the Express server requests. This is an added benefit because you can still have parts of your application not falling under a graphQL layer.

Integrating graphQL in this way makes it flexible & easy to use with other services like authentication, templating, other REST APIs, and so on.

Packages that are needed:
	- `express`
	- `graphql`
	- `express-graphql`: This is the express middleware that connects graphQL & Express together

`graphqlHTTP` is the middleware function that we need to extract from `express-graphql`. It takes an object which receives, among many things, a **schema**! This schema is what defines the data (or the graph) to be used by graphQL.

Think of schema as metadata about the data we respond with to a graphQL query.

The other option is `graphiql` (a boolean) which makes the GraphiQL development tool available.

```javascript
const express = require('express');
const { graphqlHTTP } = require('express-graphql');

const schema = require('./schema/schema')

const app = express();

// Only /graphql queries use graphQL:
app.use('/graphql', graphqlHTTP({
	schema,
	graphiql: true
}))

app.listen(4000, () => {
	console.log('Listening');
})
```

If you don't define a schema then your GQL server will display the following error on the page:

```
{"errors":[{"message":"GraphQL middleware options must contain a schema."}]}
```

### Schema

Schema is metadata about the objects, their types, and connections (and so on) in your data store. Without it, GQL will not know how to fetch data and understand how different data objects are connected.

The `graphql` library provides some data types to define in your schema:
- `GraphQLObjectType`: The type you will instantiate for your object model
- `GraphQLString`: String type, `GraphQLInt`: Integer type, ... there are many more including booleans.
- `GraphQLSchema`: The final Schema object that we export & is used by the server

#### Creating a type

Syntax:
```javascript
const UserType = new GraphQLObjectType({
  name: 'User',
  fields: {
    id: { type: GraphQLString },
    firstname: { type: GraphQLString },
    age: { type: GraphQLInt }
  }
});
```

- `name` is mandatory. It sets an identifier for your object. Convention is to capitalize it.
- `fields`: Fields of your object along with their types. Pass in built-in types from GQL

#### Root query

GraphQL will be able to connect the different objects but it does not know **which node to start from** to walk through a graph!

A root query (also an instance of `GraphQLObjectType`) offers a way to identify the root object along with some declarations:
- `name`: `'RootQueryType'`
- `fields`: This is an object that maps a query to a graphql object. Ex: If query is for a `user` then it maps it to `UserType` in the schema
- `args`: The query argument names & their types to be passed on to the the object (ex: an id)
- **`resolve`**: This is an important function. It does not declare anything but performs the actual fetching of data from a datastore. It takes in two values, a `parentValue` and the `args`. The fetch operation can be a DB query, hitting another external API, etc. It's anything.
	- `resolve` can return an value or a **promise**. For many data fetching operations, we will have to end up using promises.

Example:
```javascript
const RootQuery = new GraphQLObjectType({
  name: 'RootQueryType',
  fields: {
    user: {
      type: UserType,
      args: { id: { type: GraphQLString } },
      resolve(parentValue, args) {
        return axios
          .get(`http://localhost:3000/users/${args.id}`)
          .then(resp => resp.data)
      }
    }
  }
});
```

The above schema can essentially be read as: "If you ask me for details of a user with a particular id, I can fetch the user details and return them to you"

#### Exporting a schema

Use a `GraphQLSchema` object. The `query` field must contain a reference to the root query.

Example schema:
```javascript
const graphql = require('graphql');
const axios = require('axios');

const {
  GraphQLObjectType,
  GraphQLString,
  GraphQLInt,
  GraphQLSchema
} = graphql;

const UserType = new GraphQLObjectType({
  name: 'User',
  fields: {
    id: { type: GraphQLString },
    firstname: { type: GraphQLString },
    age: { type: GraphQLInt }
  }
});

const RootQuery = new GraphQLObjectType({
  name: 'RootQueryType',
  fields: {
    user: {
      type: UserType,
      args: { id: { type: GraphQLString } },
      resolve(parentValue, args) {
        return axios
          .get(`http://localhost:3000/users/${args.id}`)
          .then(resp => resp.data)
      }
    }
  }
});

module.exports = new GraphQLSchema({
  query: RootQuery
})
```

### GraphQL playground

Once you open a graphql endpoint on the server and **graphiQL** is enabled in the middleware, a dev tool will open up which serves as a **playground** for testing your graphql queries, reading the documentation for the objects, etc.

An example query:
```javascript
{
  user(id: "10") {
    id,
    firstname,
    age
  }
}
```

- You may list only the fields you want and get only them returned
- Supply arguments in the parentheses `()`
- No arguments passed? GraphQL throws an error

GQL will map the query to the object defined in its schema and use the argument. It will then run the resolver function to get this data It is strongly typed and therefore throws errors if the arguments types supplied or the data types in the object that is returned are wrong .

Example data returned (Data is contained in the `data` field):
```javascript
{
  "data": {
    "user": {
      "id": "10",
      "firstname": "Rishi",
      "age": 39
    }
  }
}
```

The benefit of graphQL queries is that you **do not over-fetch any data** (which was a shortcoming of RESTful APIs)

#### GraphQL as an interface

We need not have only one data store (DB) and use GQL on top of it. You may have an existing application with different services. GQL can act as a layer that the clients can interact with but internally, this layer fetches data from different services.

This is possible because GQL does not dictate how the data is fetched. It is a specification. Hence, whether you fetch it from another API, Relational DB, NoSQL DB, or so on does not matter to it.

This flexibility makes the graphQL layer as an interface that assembles many different services a great architecture!

## Fetching data with queries

### Nested queries

The biggest positive about GQL is fetching data from connected components i.e related data.

Connected components are **fields** just like other values in the schema. The type is the **schema type** we create for the connected component. However, GQL still doesn't know how to fetch this connected data. Therefore, it requires a **resolve** method as well.

```javascript
const UserType = new GraphQLObjectType({
  name: 'User',
  fields: () => ({
    id: { type: GraphQLString },
    firstname: { type: GraphQLString },
    age: { type: GraphQLInt },
    company: {
      type: CompanyType, // Defined elsewhere in the schema
      resolve(parentValue, args) {
        return axios
          .get(`http://localhost:3000/companies/${parentValue.companyId}`)
          .then(res => res.data)
      }
    }
  })
});
```
- `GraphQLList` is a built-in class which helps us create a list of items (an array) from the type that is provided
- `parentValue` is the object that represents the object that is seeking a connection. Ex: If `companyId` connects `user` and a `company` then `parentValue.companyId` is what needs to be used to make that connection in the resolve method.

**Note**: The DB might have a `companyId` foreign key in our User table but in our GQL schema, we have a `company` field. This is a computed field and is possible because of the resolve method used to fetch the connected object. Therefore, we can directly map an object to another without presenting keys to the end user. If the model does not have certain fields, our schema can still have them!

**Note**: `fields` can also be wrapped in a **function**. This is useful because it avoids issues in Javascript where using variables not yet declared in the scope throws an error! Functions make sure that this check happens after the top level declarations in the module are identified.

### More root queries

The GQL can have many root endpoints. We just add a new field to the root query.
This helps us fetch different objects independently (Ex: You can fetch a company without first fetching a user)

```javascript
const RootQuery = new GraphQLObjectType({
  name: 'RootQueryType',
  fields: {
    user: {
      type: UserType,
      args: { id: { type: GraphQLString } },
      resolve(parentValue, args) {
        return axios
          .get(`http://localhost:3000/users/${args.id}`)
          .then(resp => resp.data)
      }
    },
    company: {
      type: CompanyType,
      args: { id: { type: GraphQLString} },
      resolve(parentValue, args) {
        return axios
          .get(`http://localhost:3000/companies/${args.id}`)
          .then(res => res.data)
      }
    }
  }
});
```

### Bi-directional patterns & circular dependencies

*Scenario*: A user works for a company. A company object too might hold a set of users it employs. 

GQL allows adding circular dependencies. It works like magic. We can fetch a company from a user and fetch users from that company and so on. Not an issue with nesting. The question is, why would we want to do that, anyway?

```graphql
{
  company(id: "2") {
    name,
    users {
      firstname,
      age,
      company{
        name
      }
    }
  }
}
```

```
{
  "data": {
    "company": {
      "name": "Google",
      "users": [
        {
          "firstname": "Samantha",
          "age": 25,
          "company": {
            "name": "Google"
          }
        },
        {
          "firstname": "Pavan",
          "age": 39,
          "company": {
            "name": "Google"
          }
        }
      ]
    }
  }
}
```

### More about queries

1. Making multiple queries is possible. It is better to **name the queries** to avoid errors with key duplication in the javascript object that is returned.
```graphql
{
  "data": {
    "company": {
      "name": "Google",
      "users": [
        {
          "firstname": "Samantha",
          "age": 25,
          "company": {
            "name": "Google"
          }
        },
        {
          "firstname": "Pavan",
          "age": 39,
          "company": {
            "name": "Google"
          }
        }
      ]
    }
  }
}
```
```
{
  "data": {
    "apple": {
      "name": "Apple",
      "description": "iphone"
    },
    "google": {
      "name": "Apple",
      "description": "iphone"
    }
  }
}
```
2. We can create **fragments** for the fields we wish to receive. This helps in avoiding copy-pasting / duplicating keys in different queries. The `on` keyword maps fields to a GQL object (provides stricter checks on the fragment fields)
```graphql
{
  apple: company(id: "1") {
    ...companyDetails
  }
  google: company(id: "1") {
    ...companyDetails
  }
}

fragment companyDetails on Company {
  id,
  name,
  description
}
```
```
{
  "data": {
    "apple": {
      "id": "1",
      "name": "Apple",
      "description": "iphone"
    },
    "google": {
      "id": "1",
      "name": "Apple",
      "description": "iphone"
    }
  }
}
```

## Mutations

We write queries for reading data. For anything to do with changing data (Add, Update, Delete), we need to define **mutations** in the GQL schema.

Defining mutations:
- Create a schema object
- Add a `name`
- Add a `fields` property

It is very similar to writing a schema for an object to be queried. The only difference is in the interpretation of the `field`s' properties.

Each field is an **operation** on an object (instead of property of an object as in a query). It has the following properties:
- `type`: The type of the value that is returned from the mutation action (Not the type of the object in the schema in a query)
- `args`: The arguments passed in to the mutation (same as in a query)
- `resolve`: The function that performs the mutation! It must also return a value or a promise just like a query. This returned value is whatever the internal API/DB responds with for a mutation.

```javascript
const mutation = new GraphQLObjectType({
  name: 'Mutation',
  fields: {
    addUser: {
      type: UserType,
      args: { 
        firstname: { type: new GraphQLNonNull(GraphQLString) },
        age: { type: new GraphQLNonNull(GraphQLInt) },
        company: { type: GraphQLString }
      },
      resolve(parentValue, { firstname, age }) {
        return axios
          .post(`http://localhost:3000/users/`, { firstname, age })
          .then(res => res.data)
      }
    }
  }
});
```

- `GraphQLNonNull` is a built-in class that allows us to determine if an argument is mandatory or not. Ex: You might want to add a new user but not assign a company just yet. 

Add mutations to the final schema that is exported:
```javascript
module.exports = new GraphQLSchema({
  query: RootQuery,
  mutation
});
```

- Edit & delete mutations example:
```javascript
const mutation = new GraphQLObjectType({
  name: 'Mutation',
  fields: {
    addUser: {
      type: UserType,
      args: { 
        firstname: { type: new GraphQLNonNull(GraphQLString) },
        age: { type: new GraphQLNonNull(GraphQLInt) },
        company: { type: GraphQLString }
      },
      resolve(parentValue, { firstname, age }) {
        return axios
          .post(`http://localhost:3000/users/`, { firstname, age })
          .then(res => res.data)
      }
    },
    deleteUser: {
      type: UserType,
      args: {
        id: { type: new GraphQLNonNull(GraphQLString) }
      },
      resolve(parentValue, args) {
        return axios
          .delete(`http://localhost:3000/users/${args.id}`)
          .then(res => res.data)
      }
    },
    editUser: {
      type: UserType,
      args: {
        id: { type: new GraphQLNonNull(GraphQLString) },
        firstname: { type: GraphQLString },
        age: { type: GraphQLInt },
        companyId: { type: GraphQLString }
      },
      resolve(parentValue, args) {
        return axios
          .patch(`http://localhost:3000/users/${args.id}`, args)
          .then(res => res.data)
      }
    }
  }
})
```

- Example mutations in GraphiQL: 
```graphql
mutation {
  addUser(firstname:"Pushkar", age: 27) {
    id,
    firstname,
    age
  }
}
```
```
{
  "data": {
    "addUser": {
      "id": "6ivnleT",
      "firstname": "Pushkar",
      "age": 27
    }
  }
}
```
Another example:
```graphql


mutation {
  editUser(id: "6ivnleT", firstname: "Pavan") {
    id,
    firstname,
    age
  }
}
```
```
{
  "data": {
    "editUser": {
      "id": "6ivnleT",
      "firstname": "Pavan",
      "age": 27
    }
  }
}
```

**Note**: The schema for the different queries, objects, and mutations can be split into different files for modularity. This is a subjective issue, however. Facebook keeps all of their GQL data in one big schema file!

## GraphQL ecosystem

GraphQL by itself is a specification. It is technology agnostic. 

How you implement the data store or how you resolve them is not defined by graphql. Similarly, there are many tools that help you implement GraphQL on both the server & client app

### Server:

1. `graphql` & `express-graphql`: This helps create a graphql schema and make a graphql server. Maintained by FB (official)
2. Apollo for server: Does something similar but varies in syntax & structure of the schema (Ex: Resolve methods are written separately)

You may use any resource based on your preference. Both are good but just vary in syntax. Do ***not*** go for an Apollo server if you want to **co-locate** your schema details

### Clients

GraphQL clients form the *glue* between frontend and a graphql server.

A graphql client fetches data from a graphql server using the queries supplied to it and saves it internally on the client side. It also caches some queries.

1. **Lokka**: Simple to use. Has basic caching.
2. **Apollo**: A good balance between features & complexity (Developed by the MeteorJS team)
3. **Relay**: Extremely performant esp. for mobile but is insanely complex (esp. while writing mutations

## Apollo client

An **Apollo client** gets the data from the server & stores it internally.

- It is frontend framework agnostic i.e it does not care about which framework is being used (Ex: React/Angular)
- Usage: `import  ApolloClient  from  'apollo-client';`

An **Apollo provider**, on the other hand, is specific to React! We can wrap our render tree in this provider to use the apollo client store values in a component.

- It is React specific
- Usage: `import { ApolloProvider } from  'react-apollo';`

We create a new client & pass it to the provider like so:
```javascript
import ApolloClient from 'apollo-client';
import { ApolloProvider } from 'react-apollo';
//...
const client = new ApolloClient({});
//...
const Root = () => {
  return (
    <ApolloProvider client={client}>
	    <MyApp>
		    <!-- ... Need to use a H.O.C provided by `react-apollo` down the line -->
		</MyApp>
    </ApolloProvider>
  );
};
```

Think of `apollo-client` as `redux` & `react-apollo` as `react-redux`.

### Strategy to use while writing GraphQL with Apollo

1. Identify data required
2. Write the query for it in graphiQL for practice 
3. Write the same query inside the component file **(`gql` library (`graphql-tag` module))**
4. Bond the query & the component so that it becomes available as props (Declarative and not imperative) using **`graphql`** from `react-apollo` (A connector)

**Example of the procedure:**
The query:
```graphql
{
    songs {
      id,
      title
    }
  }
```

To define the query inside a component, we need **`graphql-tag`** library (known as `gql`)
The query in the component file:
```javascript
import gql from 'graphql-tag';
//...
const query = gql`
  {
    songs {
      id,
      title
    }
  }
`;
```

We have written a query and used the library function `gql` to define a query  in a React component file.

Binding the query to the component:
```javascript
import { graphql } from 'react-apollo';
// ...
class  SongList  extends  Component { ... }
// ...
export default graphql(query)(SongList); 
// graphql(query) returns an HOC that enhances the provided component. 
// What is exported is an enhanced component.
```

Once the component is connected, the connector will take the query & execute it. Resultant data is available as a prop.

We can also use hooks in a functional component to get this data.

### Utilizing the props

The data is contained in a connected component as `this.props.data`.
- It contains a lot of other data
- The fields to look out for are:
	- The query name: This contains the data for the query (Ex: `this.props.data`)
	- The loading attribute: True until data is fetched (async) (`this.props.data.loading`)
	- The error attribute: Not defined unless there is an error (`this.props.error`)

### General architecture

- Graphql server provides the Apollo store (client) on the client with some data
- The Apollo store is connected to an Apollo provider
- The provider wraps a root component
- This component makes the necessary queries & fetches the data
- This data is supplied down the render tree

**Note**: You may centralize the place where you wish to make queries from (like a root component). You may also store the retrieved data into a central store like redux.

## Dynamic data in queries and mutations

How do we populate variables in our queries? This is extremely useful when dynamic data (Ex: User input).

In **GraphiQL**, we can maintain a list of "Query Variables". It is an object (JSON style) like so:
```json
{
  "id": "617028f88ac067aa87fd8b7f"
}
```
We can then use these variables in a query or a mutation.
- They should be named
- Must contain an arguments list
	- The list variables are `$` prefixed
	- We should define their type (Ex: `String`, `ID`, etc)
- The query or mutation inside must refer to these variables for its arguments

Example:
```graphql
mutation AddSong($title: String) {
  addSong(title: $title) {
    id,
    title
  }
}
```

**Note:** We cannot define variables per operation. We have to supply variables for the whole mutation.

Suffixing a **`!`** to the variable name makes it a compulsory argument to the mutation or query

```graphql
query SongQuery($id: ID!) {
  song(id:$id) {
  ...
```

## Mutations from the client

Mutations can be attached to a component in a similar fashion to a query.

1. Add the required imports:
```javascript
import gql from 'graphql-tag';
import { graphql } from 'react-apollo';
```
2. Write the mutations using the graphql variables. Note that a mutation is an **operation** (not data).
```javascript
const mutation = gql`
  mutation AddSong($title: String) {
    addSong(title: $title) {
      id,
      title
    }
  }
`;
```
3. Connect the component:
```javascript
export default graphql(mutation)(SongCreate);
```

4. The above steps are same for a mutation as it is for a query. There are a few differences inside the component though:
- The component receives **`this.props.mutate`** (instead of `this.props.data`)
- The mutate prop takes in an object:
	- `variables`: Define the variables and supply the values

```javascript
onSubmit(event) {
    event.preventDefault();
	this.props.mutate({
      variables: {
        title: this.state.title
      },
    })
 }
```

5. The method returns a promise after a mutation. We can use it to do further processing on the client-side

```javascript
this.props.mutate({ /* ... */}).then(() => hashHistory.push("/"))
```

**Note:** It does not matter if you add, update, or delete. You need to use the same mutation approach defined above.

### Cold cache vs warm cache

Apollo client stores data by running queries and fetching objects. When it is connected to a component, it ***does not re-fetch data by default***. Apollo runs a query for a component only once and caches it.

Therefore, if you perform a mutation and it affects data that you are rendering then you need to refetch the data. If you don't, then the component will not update itself since apollo didn't fetch the updated data since it did not run the query.

## Re-fetching queries

We need to manually re-fetch the data after a mutation.

**Two ways to re-fetch data:**
1. **`refetchQueries`** option in the object passed to `props.mutate`
	- Takes an **array** of queries to be run after a mutation
	- For each item in the array, define a `query` and any `variables` the query needs (optional)

```javascript
this.props.mutate({
      variables: {
        title: this.state.title
      },
      refetchQueries: [{
        query: fetchSongsQuery,
        // variables: { /* ... */ }
      }]
    }).then(() => hashHistory.push("/"))
```

*Use this approach when the mutation in the component affects queries outside of the component as well*

Ex: A create song component mutates the songs' list. This list is displayed in a songs list component. Therefore, use this approach in such a case.

2. **`this.props.data.refetch()`** : When a component has a query attached to it, it receives this method in the props whose invocation automatically queries the server again and fetches the data

```javascript
onSongDelete(id) {
  this.props.mutate({
    variables: { id },
  }).then(() => {
    this.props.data.refetch();
  })
}
```

*Use this approach when the mutation in the component affects ONLY the query the component depends on for data.*

## Connecting multiple queries and mutation to a component

React Apollo has *not* given us an option to:
- Have multiple queries / mutations
- Have a mix of queries and mutations connected to you component.

Therefore, there is no simple solution. However, since the connector returns a HOC that enhances the component, we can use it multiple times as follows to achieve the goal of having many queries & mutations on the component

```javascript
export default graphql(mutation)(graphql(fetchSongsQuery)(SongList));
```

## Executing dynamic queries from a component

For mutations inside a component, we can pass in data from the state or props or another expression. The queries, however, are one level above the component (i.e `graphql(query)(Component)` so we cannot do the same.

To run a query with arguments (Let's say, pass in an ID for a song that the component displays), we have a separate procedure to pass in variables.
- Pass in a second argument to `graphql` function: An object (`{}`)
- It contains an `options` function. It receives the props for the component
	- Use the `props` to return an object with `variables`

```javascript
export default graphql(fetchSongQuery, {
  options: (props) => ({
    variables: { id: props.params.id }
  })
})(SongDetail);
```

## Modularizing queries

Create your queries in such a way that they are ***reusable***
- Add fields if you want to use the queries in multiple places but need different data in both places
- Keep a separate file for a query & import it when required

```javascript

import gql from 'graphql-tag';

export default gql`
  query SongQuery($id: ID!) {
    song(id:$id) {
      id,
      title,
      lyrics {
        id,
        content
      }
    }
  }
`;
```

## Identifying records in Apollo client

We can identify objects in Apollo client by providing an object with a `dataIdFromObject` function in the setup:

```javascript
const client = new ApolloClient({
  dataIdFromObject: o => o.id
});
```

The above code tells Apollo to set the `id` attribute of any object (`o`) it queries as the data ID.

**How does this help?**

This helps because when we make changes to our data, we don't have to always fetch related data. For example, if a lyric has been added to the list of lyrics of a song then the song will auto-update its lyrics' list if it knows the data id of the lyric.

*Such an auto-update saves us the trouble of making another N/W request to fetch up-to-date information.*

Internally, Apollo makes use of a DB concept called normalization. Read more about it [here](https://www.apollographql.com/blog/apollo-client/caching/demystifying-cache-normalization/).

## Error handling on the client side

**Mutation errors**:
- When you perform a mutation and there is an error in the graphQL server, we get back a rejected promise from the `mutate` method.
- We can add a `catch()` class in javascript to handle this error.
- Read the error object from the response in `catch` callback with `response.graphQLErrors`: 
	- It is an array of error objects. Each object has a `message` property describing the error.
- Use the error objects to determine what action needs to be taken by the frontend app on a graphQL server failure (or client error during data fetching)

**Query errors**:
- Errors while connecting a query to a component are available in the `this.props.data.errors` field. 
- It is also an array of error objects just like with errors in mutation
- Use it to gracefully handle errors.
