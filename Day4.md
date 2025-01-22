### `src/redux/movieSlice.js`


```js
import { createSlice } from '@reduxjs/toolkit';

const initialState = {
  placeholder: 'Redux is working!',
};

const movieSlice = createSlice({
  name: 'movies',
  initialState,
  reducers: {
    // Add actions here in the future
  },
});

export default movieSlice.reducer;
```

### `src/redux/store.js`

```js
import { configureStore } from '@reduxjs/toolkit';
import movieReducer from './movieSlice';

export const store = configureStore({
  reducer: {
    movies: movieReducer,
  },
});

export default store;
```

### `src/App.js (Updated)`

```js
import { Provider } from 'react-redux';
import { store } from './redux/store';


function App() {
  return (
    <Provider store={store}>
      //Adding Provider and Configuring Store in it
    </Provider>
  );
}

export default App;
```

### `src/App.js(main)`

```js
    import React from 'react';
    import { ThemeProvider, CssBaseline, Box } from '@mui/material';
    import { Provider } from 'react-redux';
    import theme from './styles/theme';
    import Navbar from './components/NavBar';
    import { store } from './redux/store';
    
    // Pages
    import Home from './pages/Home';
    
    function App() {
      return (
        <Provider store={store}>
          <ThemeProvider theme={theme}>
            <CssBaseline />
              <Navbar />
              <Box sx={{ mt: 8 }}>
                <Home/>
              </Box>
          </ThemeProvider>
        </Provider>
      );
    }
    
    export default App;
```

### `src/pages/Home.js (main)`

```javascript
    import React from 'react';
    import { useSelector } from 'react-redux';
    
    function Home() {
    const select=useSelector(state=>state.movies.placeholder);
      return <h2>Home Page {select}</h2>;
    }
    
    export default Home;
```     

---
### `Installations`
```
npm install @reduxjs/toolkit react-redux
```
