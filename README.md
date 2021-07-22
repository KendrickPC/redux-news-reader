# Redux News Reader

### Write Your First Asynchronous Action Creator

1a. We’re going to start by using createAsyncThunk to create an asynchronous action creator, loadCommentsForArticleId, that fetches all the comments for a particular article.

In commentsSlice.js, import createAsyncThunk from Redux Toolkit.
[createAsyncThunk documentation](https://redux-toolkit.js.org/api/createAsyncThunk)
```javascript
import { createAsyncThunk } from '@reduxjs/toolkit';
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
    state.byArticleId[action.payload.articleId] = action.payload.comments
    state.isLoadingComments = false;
    state.failedToLoadComments = false;
  }
}
```
### Display Comments for the Current Article

In comments.js:

8a. Replace the empty array currently assigned to comments with the comments currently in state

Note: According to the documentation, [React Redux Hooks](https://react-redux.js.org/api/hooks#useselector) useSelector() allows you to extract data from the Redux store state, using a selector function.
```javascript
// const comments = [];
const comments = useSelector(selectComments);
```

8b. Replace the false boolean currently assigned to commentsAreLoading with the state value representing whether or not there is currently a pending request to fetch comments.
```javascript
// const commentsAreLoading = false;
const commentsAreLoading = useSelector(isLoadingComments);
```

 Fetch all the comments for the current article any time the the current article changes. Once we have the comments for the current article, we’ll pass them to CommentList to make them show up on the page.

In Comments.js: 

9a. Using useEffect, dispatch loadCommentsForArticleId any time article changes and only if article is not undefined.
```javascript
// Dispatch loadCommentsForArticleId with useEffect here.
useEffect( () => {
  if (article !== undefined) {
    dispatch(loadCommentsForArticleId(article.id));
  }
}, [dispatch, article]);
```
9b. Define a constant, commentsForArticleId, which should be an empty array when article is undefined and otherwise should be equal to comments[article.id].
```javascript
const commentsForArticleId = article === undefined ? [] : comments[article.id];
```
9c. Replace the empty array we’ve passed as the CommentList‘s comments prop with commentsForArticleId.
```javascript
<CommentList comments={commentsForArticleId} />
```

Make CommentList component render the comments it receives.
Inside the <ul> of the CommentList component’s return statement (from CommentList.js file):

10a. Map over the comments prop and render a Comment (we’ve imported this component for you) for each value.
```javascript
{comments.map(comment => {
  return <Comment />
})}
```
10b. Each Comment component needs to be passed a comment prop.
```javascript
{comments.map(comment => {
  return <Comment comment={comment} />
})}
```

Checkpoint: Click on an article (LG, Kamala, Scientists), you should see that article’s comments displayed below it!

### Write postCommentForArticleId

Go through a similar process to add another form for creating new comments for the current article.

First, back in commentsSlice.js, you’ll write another asynchronous action creator, postCommentForArticleId, which will be called like so:
```javascript
postCommentForArticleId({
  articleId: 1,
  comment: "This article is great!"
}}
```

Inside commentsSlice.js:

11a. Declare a new exported variable called postCommentForArticleId.
```javascript
export const postCommentForArticleId;
```
11b. Call createAsyncThunk with two arguments—an action type string, and an asynchronous callback—and store the result in postCommentForArticleId.
[createAsyncThunk documentation](https://redux-toolkit.js.org/api/createAsyncThunk)
```javascript
export const postCommentForArticleId = createAsyncThunk(
  'comments/postCommentForArticleId',
  async() => {

  }
);
```
11c. The asynchronous callback should accept an object with two properties–articleId and comment–as a parameter. You should destructure the object in your function definition so that you can refer to articleId and comment when you implement the function body.
```javascript
export const postCommentForArticleId = createAsyncThunk(
  'comments/postCommentForArticleId',
  async( {articleId, comment} ) => {
    // do some asynchronous operations here 
  }
);
```

Now let’s implement the body of the asynchronous callback to make an asynchronous POST request to the news API.

To make a POST request, we can again use the fetch() method but we must specify that we want to make a POST request. We must also pass along the comment value included in the parameter object (formatted as a stringified object).

Inside the asynchronous callback passed to createAsyncThunk(),

12a. Declare a new variable called requestBody. Then, call JSON.stringify() and pass in an object with a single property, comment, corresponding to the text of the new comment included in the parameter object. Assign the result to requestBody.
```javascript
export const postCommentForArticleId = createAsyncThunk(
  'comments/postCommentForArticleId',
  async( {articleId, comment} ) => {
    // do some asynchronous operations here 
    const requestBody = JSON.stringify( {comment: comment} ) ;
    fetch(`api/articles/${articleId}/comments`)
  }
);
```

12b. Call the fetch() method to make a request to 'api/articles/ID/comments', replacing 'ID' with the articleId value included in the parameter object.
```javascript
export const postCommentForArticleId = createAsyncThunk(
  'comments/postCommentForArticleId',
  async( {articleId, comment} ) => {
    // do some asynchronous operations here 
    const requestBody = JSON.stringify( {comment: comment} ) ;
    fetch(`api/articles/${articleId}/comments`)
  }
);
```
12c. Pass an options object as the second argument to fetch() after the URL. This object should have a method key with the value equal to the string 'POST'.
```javascript
export const postCommentForArticleId = createAsyncThunk(
  'comments/postCommentForArticleId',
  async( {articleId, comment} ) => {
    // do some asynchronous operations here 
    const requestBody = JSON.stringify( {comment: comment} ) ;
    fetch(`api/articles/${articleId}/comments`, {method: 'POST'})
  }
);
```
12d. Add a body key to the options object with the value equal requestBody.
```javascript
export const postCommentForArticleId = createAsyncThunk(
  'comments/postCommentForArticleId',
  async( {articleId, comment} ) => {
    // do some asynchronous operations here 
    const requestBody = JSON.stringify( {comment: comment} ) ;
    fetch(`api/articles/${articleId}/comments`, {
      method: 'POST',
      body: requestBody,
    })
  }
);
```
12e. Since fetch is asynchronous, you’ll want to await the result and store it in a variable called response.
```javascript
export const postCommentForArticleId = createAsyncThunk(
  'comments/postCommentForArticleId',
  async( {articleId, comment} ) => {
    // do some asynchronous operations here 
    const requestBody = JSON.stringify( {comment: comment} ) ;
    const response = await fetch(`api/articles/${articleId}/comments`, {
      method: 'POST',
      body: requestBody,
    })
  }
);
```
12f. To get the actual JSON data from the response body, call .json() on response. .json() is also asynchronous, so you’ll once again want to await the result and store it in a variable called json.
```javascript
export const postCommentForArticleId = createAsyncThunk(
  'comments/postCommentForArticleId',
  async( {articleId, comment} ) => {
    // do some asynchronous operations here 
    const requestBody = JSON.stringify( {comment: comment} ) ;
    const response = await fetch(`api/articles/${articleId}/comments`, {
      method: 'POST',
      body: requestBody,
    })
    const json = await response.json()
  }
);
```
12g. Finally, return json.
```javascript
export const postCommentForArticleId = createAsyncThunk(
  'comments/postCommentForArticleId',
  async( {articleId, comment} ) => {
    // do some asynchronous operations here 
    const requestBody = JSON.stringify( {comment: comment} ) ;
    const response = await fetch(`api/articles/${articleId}/comments`, {
      method: 'POST',
      body: requestBody,
    })
    const json = await response.json()
    return json;
  }
);
```

Like all action creators generated by createAsyncThunk, postCommentForArticleId will dispatch actions for each of the pending/fulfilled/rejected promise lifecycle states. In order to make our app reflect these changing states, we have to keep track of them in the store.

13a. Add two booleans — createCommentIsPending and failedToCreateComment — to initialState, and set their initial values appropriately.
Note: Inside the byArticleId object, the developer notes above hint to "add initial state properties here."
```javascript
export const commentsSlice = createSlice({
  name: 'comments',
  initialState: {
    // Add initial state properties here.
    byArticleId: {
      isLoadingComments: false,
      failedToLoadComments: false,
      createCommentIsPending: false,
      failedToCreateComment: false,
    },
  },
})
```


Now modify the extraReducers property of your slice configuration options by adding a reducer for each of the three promise lifecyle actions dispatched by postCommentsForArticleId:

14a. The pending promise lifecycle action
```javascript
extraReducers: {
  ...
  [postCommentForArticleId.pending]: (state, action) => {
    state.createCommentIsPending = true;
    state.failedToCreateComment = false;
  },
}
```
14b. The rejected promise lifecycle action
```javascript
extraReducers: {
  ...
  [postCommentForArticleId.pending]: (state, action) => {
    state.createCommentIsPending = true;
    state.failedToCreateComment = false;
  },
  [postCommentForArticleId.rejected]: (state, action) => {
    state.createCommentIsPending = false;
    state.failedToCreateComment = true;
  },
}
```
14c. The fulfilled promise lifecycle action. Note that in this case, action.payload will be a comment object including an articleId that you can use to add the comment object to correct article’s comment list in state.
Each one should update the state accordingly.
```javascript
extraReducers: {
  ...
  [postCommentForArticleId.pending]: (state, action) => {
    state.createCommentIsPending = true;
    state.failedToCreateComment = false;
  },
  [postCommentForArticleId.rejected]: (state, action) => {
    state.createCommentIsPending = false;
    state.failedToCreateComment = true;
  },
  [postCommentForArticleId.fulfilled]: (state, action) => {
    // The fulfilled reducer should add the newly created comment to the byArticleId object you added to initialState
    state.byArticleId[action.payload.articleId].push(action.payload)
    state.createCommentIsPending = false;
    state.failedToCreateComment = false;
  },
}
```

### Improve Comment Form UX

Connect the CommentForm to our new asynchronous action creator.

In CommentForm.js:
15a. Import postCommentForArticleId from the comments slice.
```javascript
import {
  createCommentIsPending, postCommentForArticleId
} from '../features/comments/commentsSlice';
```
15b. In handleSubmit, dispatch postCommentForArticleId passing in an object with articleId and comment.
```javascript
dispatch(postCommentForArticleId( {articleId, comment} ));
```

Checkpoint: You should be able to create new comments with the comment form.

Improve the user experience slightly by disabling the submit button when a request to create a new comment is pending. This will prevent users from accidentally creating the same comment twice.

In CommentForm.js
16a. Define a constant, isCreatePending, using useSelector and the imported selector createCommentIsPending.
```javascript
const isCreatePending = useSelector(createCommentIsPending);
```
16b. Add a disabled attribute to the form’s button and set it equal to isCreatePending. Refresh the browser and try to create the same comment twice by double clicking. You’ll see that it’s now impossible to do so.
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

