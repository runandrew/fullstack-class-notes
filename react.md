# React notes

## Props

### How to access them
* In *dumb* components through the function parameter (e.g. `function (props) => { return (<div> { props.nameOfVariable } </div>) }`)
* Class - this.props.nameOfVariable

### How to send them down
(e.g. `<BestComponentEver handleChange={this.handleChange} />` OR `<BestComponentEver {…this.props} />`)

## Creating and changing state

### Class definition style
```js
import React from 'react';

class Album extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            foo: defaultFooValue,
            bar: defaultBarValue
        }
    }

    render() {
        return ();
    }
}
```

### Mutate the state
`this.state = { stateVariableName: newStateValue }`

## Forms
For more information checkout the [React docs](https://facebook.github.io/react/docs/forms.html).

### How to grab the information from forms
In general:

```js
class EssayForm extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            value: 'default value'
        }

        this.handleChange = this.handleChange.bind(this);
    }

    handleChange(event) {
        this.setState({ value: event.target.value });
    }

    render() {
        return (
            <div>
                <textarea value={this.state.value} onChange={this.handleChange} />
            </div>
        );
    }
}
```

For a form:
```js
<form onSubmit={this.onSubmit}>
    <input
        name="email"
        type="email"
        className="form-control"
        required
    />
    <input
      name="password"
        type="password"
        className="form-control"
        required
    />
    <button type="submit" className="btn btn-block btn-primary">{message}</button>
</form>
```

You can use this `onSubmit` function:

```js
onSubmit(event) {
    event.preventDefault();
    const { message, login, signup } = this.props;
    const credentials = {
        email: event.target.email.value,
        password: event.target.password.value
    }
}
```
