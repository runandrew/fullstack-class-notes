# React-router Notes

More information at [React Router Training](https://github.com/ReactTraining/react-router/).

## Child routes

With a parent child relationship, which is created from the Routes hierarchy, we gain a relationship where the parent can access the child component (which is chosen from the URI route that is visited).

```js
  <Router history={ hashHistory } >
    <Route path="/" component={ AppContainer }>
        <Route path="/albums/:albumId" component={ Album } />
        <Route path="/albums" component={ Albums } />
        <IndexRedirect to="/albums" />
    </Route>
  </Router>
```

With a child component, the parent now has access to a `children` prop.

To render this current child of the parent:

```js
class Parent extends React.Component {
  render () {
    return (
      <div>
        {
            this.props.children ?
            React.cloneElement(this.props.children, { foo: this.state.foo }) {/* This allows props */}
            : null
        } {/* This will cause that Child component to get rendered here! */}
      </div>
    );
  }
}
```

Child routes can take *absolute* and *relative* paths as a prop, and the paths of child routes are built on top of their parent paths.

The following:
```js
<Route path='/hello' component={Greetings}>
    <Route path='/hello/world' component={PlanetEarth} />
</Route>
```
Can be refactored to:
```js
<Route path='/hello' component={Greetings}>
    <Route path='world' component={PlanetEarth} />
</Route>
```

## IndexRedirect and IndexRoute

### IndexRoute
If a route path's children are not in the path, it will render a default component.

```js
<Router>
  <Route path="/" component={App}>
    <IndexRoute component={Home}/>
    <Route path="accounts" component={Accounts}/>
    <Route path="statements" component={Statements}/>
  </Route>
</Router>
```

### IndexRedirect
If a route path's children are not in the path, it will redirect to a default URI.

```js
<Route path="/" component={App}>
  <IndexRedirect to="/welcome" />
  <Route path="welcome" component={Welcome} />
  <Route path="about" component={About} />
</Route>
```

## Link
Links can have an active style class applied.

```js
<Link to={`/users/${user.id}`} activeClassName="active">{user.name}</Link>
```
