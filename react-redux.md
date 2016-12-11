## React-redux notes

## Provider
Makes the Redux store available to the connect() calls in the component hierarchy.
```js
import store from './store';

ReactDOM.render(
  <Provider store={store}>
    <Router history={history}>
      <Route path="/" component={App}>
        <Route path="foo" component={Foo}/>
        <Route path="bar" component={Bar}/>
      </Route>
    </Router>
  </Provider>,
  document.getElementById('root')
);
```

## Connect

### mapStateToProps
```js
const mapStateToProps = (state, ownProps) => {
    return {
        currentSong: state.player.currentSong,
        progress: state.player.progress
    }
}
```
### mapDispatchToProps
```js
const mapDispatchToProps = (dispatch, ownProps) => {
    return {
        toggle: (song, list) => {
            dispatch(toggleSong(song, list));
        },
        next: () => {
            dispatch(next());
        }
    }
}
```

```js
const PlayerContainer = connect(mapStateToProps, mapDispatchToProps)(Player);

export default PlayerContainer;
```
