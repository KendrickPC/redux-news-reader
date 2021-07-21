# Redux News Reader

### Write Your First Asynchronous Action Creator

1a. We’re going to start by using createAsyncThunk to create an asynchronous action creator, loadCommentsForArticleId, that fetches all the comments for a particular article.

In commentsSlice.js, import createAsyncThunk from Redux Toolkit.
[createAsyncThunk documentation](https://redux-toolkit.js.org/api/createAsyncThunk)
```javascript
import { createAsyncThunk, createSlice } from '@reduxjs/toolkit';
```

Now that you’ve imported createAsyncThunk, let’s use it to define an asynchronous action creator that fetches all the comments for a particular article.
[CC createAsyncThunk cheatsheat](https://www.codecademy.com/learn/learn-redux/modules/redux-middleware-and-thunks/cheatsheet)

2a. Call createAsyncThunk with two arguments—an action type string, and an asynchronous callback that takes an article id as its first argument.
```javascript
createAsyncThunk(
  'comments/loadCommentsForArticleId',
  async(id) => {
    
  }
) 
```

2b. Store the result in a constant called loadCommentsForArticleId and export it.
```javascript
export const loadCommentsForArticleId = createAsyncThunk(
  'comments/loadCommentsForArticleId',
  async(id) => {
    
  }
)
```

3a. Call fetch() with the proper route for fetching the comments of the given article id parameter.
```javascript
export const loadCommentsForArticleId = createAsyncThunk(
  'comments/loadCommentsForArticleId',
  async(id) => {
    fetch(`api/articles/${id}/comments`)
  }
)
```
3b. Since fetch() is asynchronous, you’ll want to await the result and store it in a variable called response.
```javascript
export const loadCommentsForArticleId = createAsyncThunk(
  'comments/loadCommentsForArticleId',
  async(id) => {
    const response = await fetch(`api/articles/${id}/comments`)
  }
);
```
3c. fetch returns a Promise containing the HTTP response to the request. To get the actual JSON data from the response body, call .json() on response. .json() is also asynchronous, so you’ll once again want to await the result and store it in a variable called json.
```javascript
export const loadCommentsForArticleId = createAsyncThunk(
  'comments/loadCommentsForArticleId',
  async(id) => {
    const response = await fetch(`api/articles/${id}/comments`)
    const json = await response.json();
  }
);
```
3d. Finally, return json.
```javascript
export const loadCommentsForArticleId = createAsyncThunk(
  'comments/loadCommentsForArticleId',
  async(id) => {
    const response = await fetch(`api/articles/${id}/comments`)
    const json = await response.json();
    return json;
  }
);
```

3e. Nothing is showing up on the screen so right click on the browser and go to inspect. The console will say "createSlice is not defined". To fix this error, go to the top and add createSlice next to createAsyncThunk. Now, there will be news feeds showing up in our browser window. 
```javascript
import { createAsyncThunk, createSlice } from '@reduxjs/toolkit';
```

### Add Extra Reducers to the Comments Slice
4a. In commentsSlice.js, add a byArticleId property to initialState, and set it equal to an empty object.
```javascript
export const commentsSlice = createSlice({
  name: 'comments',
  initialState: {
    // Add initial state properties here.
    byArticleId: {},
  },
  // Add extraReducers here.
});
```

5a. Update our slice’s initial state to include two booleans: isLoadingComments and failedToLoadComments, and initialize them appropriately.
```javascript
export const commentsSlice = createSlice({
  name: 'comments',
  initialState: {
    // Add initial state properties here.
    byArticleId: {
      isLoadingComments: false,
      failedToLoadComments: false,
    },
  },
  // Add extraReducers here.
});
```

6a. Add an extraReducers property to the configurations options object passed to createSlice. You can use the map object notation you learned in the lesson, or study the documentation for builder callback notation and use that instead.
[extraReducers property cheatsheet](https://www.codecademy.com/learn/learn-redux/modules/redux-middleware-and-thunks/cheatsheet)

6b. For now, set the property to an empty object.
```javascript
export const commentsSlice = createSlice({
  name: 'comments',
  initialState: {
    // Add initial state properties here.
    byArticleId: {
      isLoadingComments: false,
      failedToLoadComments: false,
    },
  },
  // Add extraReducers here.
  extraReducers: {
    
  }
});
```

Add a reducer for each of the three actions dispatched by loadCommentsForArticleId:
Note: I referenced the past lesson, before the quiz, from MANAGING PROMISE LIFECYCLE ACTIONS: Conclusion and followed the structure for extraReducers from there.
7a. The pending promise lifecycle action
```javascript
extraReducers: {
  [loadCommentsForArticleId.pending]: (state, action) => {
    state.isLoadingComments = true;
    state.failedToLoadComments = false;
  },
}
```
7b. The rejected promise lifecycle action
```javascript
extraReducers: {
  [loadCommentsForArticleId.pending]: (state, action) => {
    state.isLoadingComments = true;
    state.failedToLoadComments = false;
  },
  [loadCommentsForArticleId.rejected]: (state, action) => {
    state.isLoadingComments = false;
    state.failedToLoadComments = true;
  },
}
```
7c. The fulfilled promise lifecycle action. Note that in this case, action.payload is a comment object with an articleId property you can use to add the comment to the correct article’s comment list in state.
```javascript
  // Add extraReducers here.
extraReducers: {
  [loadCommentsForArticleId.pending]: (state, action) => {
    state.isLoadingComments = true;
    state.failedToLoadComments = false;
  },
  [loadCommentsForArticleId.rejected]: (state, action) => {
    state.isLoadingComments = false;
    state.failedToLoadComments = true;
  },
  [loadCommentsForArticleId.fulfilled]: (state, action) => {
    // state.byArticleId[action.payload.byArticleId] = action.payload.comments
    // state.byArticleId[action.payload.articleId] = action.payload.comments
    state.isLoadingComments = false;
    state.failedToLoadComments = false;
  }
}
```
### Display Comments for the Current Article

In comments.js:
8a. Replace the empty array currently assigned to comments with the comments currently in state
Note: useSelector() allows you to extract data from the Redux store state, using a selector function.
```javascript
// const comments = [];
const comments = useSelector(selectComments);
```

8b. Replace the false boolean currently assigned to commentsAreLoading with the state value representing whether or not there is currently a pending request to fetch comments.
```javascript
// const commentsAreLoading = false;
const commentsAreLoading = useSelector(isLoadingComments);
```

```javascript
```
```javascript
```

```javascript
```

```javascript
```

```javascript
```
```javascript
```

```javascript
```

```javascript
```

```javascript
```
```javascript
```

```javascript
```

```javascript
```

```javascript
```

