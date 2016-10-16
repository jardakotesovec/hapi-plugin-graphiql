
hapi-plugin-graphiql
====================

[HAPI](http://hapijs.com/) plugin for [GraphiQL](https://github.com/graphql/graphiql) integration

<p/>
<img src="https://nodei.co/npm/hapi-plugin-graphiql.png?downloads=true&stars=true" alt=""/>

<p/>
<img src="https://david-dm.org/rse/hapi-plugin-graphiql.png" alt=""/>

Installation
------------

```shell
$ npm install hapi hapi-plugin-graphiql
```

About
-----

This is a small plugin for the [HAPI](http://hapijs.com/)
server framework for seamless integration of
[GraphiQL](https://github.com/graphql/graphiql), an interactive
[GraphQL](http://graphql.org/) user interface. This variant of GraphiQL
especially integrates a username/password based login dialog and reports
network responses in the GraphiQL status bar.

Usage
-----

The following example shows the plugin in action.
The shown options are actually the default ones:

```js
server.register({
    register: require("hapi-plugin-grapiql"),
    options: {
        graphiqlGlobals: "",
        graphiqlURL: "/api",
        graphqlFetchURL: "/api/data/graph",
        graphqlFetchOpts: `{
            method: "POST",
            headers: {
                "Content-Type": "application/json",
                "Accept":       "application/json"
            },
            body: JSON.stringify(graphQLParams),
            credentials: "same-origin"
        }`,
        loginFetchURL: "/api/auth/login",
        loginFetchOpts: `{
            method: "POST",
            headers: {
                "Content-Type": "application/json"
            },
            body: JSON.stringify({
                username: username,
                password: password
            }),
            credentials: "same-origin"
        }`,
        loginFetchSuccess: "",
        loginFetchError: "",
        graphqlExample:
            "query Example {\n" +
            "    Session {\n" +
            "        __typename # schema introspection\n" +
            "    }\n" +
            "}\n"
    }
})
```

This assumes you have a REST-based authentication endpoint
`/api/auth/login` and a GraphQL endpoint `/api/data/graphql`. The
GraphiQL UI then can be accessed under `/api`. The authentication
endpoint is assumed to accept `{ username: "...", password: "..." }` and
sets the authentication token as a HTTP cookie for `/api`. If you your
authentication endpoint returns `{ token: "..." }` and the token has to be passed in an
`Authorization` header as a Bearer token, use the following configuration instead:

```js
server.register({
    register: require("hapi-plugin-grapiql"),
    options: {
        graphiqlGlobals: `var token = null;`,
        graphiqlURL: "/api",
        graphqlFetchURL: "/api/data/graph",
        graphqlFetchOpts: `{
            method: "POST",
            headers: Object.assign({
                "Content-Type":  "application/json",
                "Accept":        "application/json"
            }, token ? {
                "Authorization": "Bearer " + token
            } : {}),
            body: JSON.stringify(graphQLParams),
            credentials: "same-origin"
        }`,
        loginFetchURL: "/api/auth/login",
        loginFetchOpts: `{
            method: "POST",
            headers: {
                "Content-Type": "application/json"
            },
            body: JSON.stringify({
                username: username,
                password: password
            }),
            credentials: "same-origin"
        }`,
        loginFetchSuccess: `token = JSON.parse(response.text()).token;`
        loginFetchError:   `token = null;`,
        graphqlExample:
            "query Example {\n" +
            "    Session {\n" +
            "        __typename # schema introspection\n" +
            "    }\n" +
            "}\n"
    }
})
```

License
-------

Copyright (c) 2016 Ralf S. Engelschall (http://engelschall.com/)

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
"Software"), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be included
in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

