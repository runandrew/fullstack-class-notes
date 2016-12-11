# Redux notes

## Dealing with the store
* Subscribe / unsubscribe, dispatch

### Subscribing and unsubscribing
Inside the container component:
```js
componentDidMount() {
    this.unsubscribe = store.subscribe(() => {
        this.setState(store.getState());
    });
}

componentWillUnmount() {
    this.unsubscribe();
}
```

## Anatomy of a Sync and Async action creator

```js
import { SET_LYRICS } from '../constants';
import axios from 'axios';

export const setLyrics = text => ({
  type: SET_LYRICS,
  text: text
});

export const searchLyrics = (artist, song) => {
  return dispatch => {
    return axios.get(`/api/lyrics/${artist}/${song}`)
      .then(res => {
        dispatch(setLyrics(res.data.lyric));
      });
  };
};
```

The async action creator must be called with a dispatch:
```js
componentDidMount() {
    dispatch(searchLyrics(artist, song));
}
```

## Anatomy of create store (with middleware)

```js
import { createStore } from 'redux';
import rootReducer from '/reducers';

const store = createStore(rootReducer);
export default store;
```

With middleware:
```js
import { createStore } from 'redux';
import rootReducer from '/reducers';
import thunkMiddleware from 'redux-thunk';
import createLogger from 'redux-logger';

const loggerMiddleware = createLogger();

const store = createStore(
    rootReducer,
    applyMiddleware(
        thunkMiddleware,
        loggerMiddleware
    )
);
export default store;
```

## Root reducer

```js
import { combineReducers } from 'redux';
import {
    SELECT_SUBREDDIT, INVALIDATE_SUBREDDIT,
    REQUEST_POSTS, RECEIVE_POSTS
} from '../actions';

function selectedSubreddit(state = 'defaultState', action) {
    switch (action.type) {
        case SELECT_SUBREDDIT:
        return action.subreddit
        default:
        return state
    }
}

function postsBySubreddit(state = {}, action) {
  switch (action.type) {
    case INVALIDATE_SUBREDDIT:
    case RECEIVE_POSTS:
    case REQUEST_POSTS:
      return Object.assign({}, state, {
        [action.subreddit]: posts(state[action.subreddit], action)
      })
    default:
      return state
  }
}

const rootReducer = combineReducers({
    postsBySubreddit: postsBySubreddit,
    selectedSubreddit: selectedSubreddit
});

export default rootReducer
```
