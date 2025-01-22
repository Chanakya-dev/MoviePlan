### `src/components/Navbar.js`

```javascript
// Add a search form
import React, { useState } from 'react';
import { useNavigate } from 'react-router-dom';
import { AppBar, Toolbar, Typography, Box, InputBase, IconButton } from '@mui/material';
import { styled, alpha } from '@mui/material/styles';
import SearchIcon from '@mui/icons-material/Search';

const Search = styled('div')(({ theme }) => ({
  position: 'relative',
  borderRadius: theme.shape.borderRadius,
  backgroundColor: alpha(theme.palette.common.white, 0.15),
  '&:hover': {
    backgroundColor: alpha(theme.palette.common.white, 0.25),
  },
  marginLeft: theme.spacing(2),
  width: 'auto',
}));

const SearchIconWrapper = styled('div')(({ theme }) => ({
  padding: theme.spacing(0, 2),
  height: '100%',
  position: 'absolute',
  pointerEvents: 'none',
  display: 'flex',
  alignItems: 'center',
  justifyContent: 'center',
}));

const StyledInputBase = styled(InputBase)(({ theme }) => ({
  color: 'inherit',
  '& .MuiInputBase-input': {
    padding: theme.spacing(1, 1, 1, 0),
    paddingLeft: `calc(1em + ${theme.spacing(4)})`,
    transition: theme.transitions.create('width'),
    width: '12ch',
    [theme.breakpoints.up('md')]: {
      width: '20ch',
    },
  },
}));

function Navbar() {
  const [searchQuery, setSearchQuery] = useState('');
  const navigate = useNavigate();

  const handleSearch = (e) => {
    e.preventDefault();
    if (searchQuery.trim()) {
      navigate(`/search?q=${encodeURIComponent(searchQuery.trim())}`);
      setSearchQuery('');
    }
  };

  return (
    <AppBar position="fixed">
      <Toolbar>
        <Typography variant="h6" component="div">
          Movie App
        </Typography>
        <Box component="form" onSubmit={handleSearch} sx={{ ml: 'auto' }}>
          <Search>
            <SearchIconWrapper>
              <SearchIcon />
            </SearchIconWrapper>
            <StyledInputBase
              placeholder="Search..."
              inputProps={{ 'aria-label': 'search' }}
              value={searchQuery}
              onChange={(e) => setSearchQuery(e.target.value)}
            />
          </Search>
        </Box>
      </Toolbar>
    </AppBar>
  );
}

export default Navbar;
```

### `src/redux/movieSlice.js (update)`

```javascript
import api, { getPopularMovies, getTrendingMovies } from '../util/api';
export const searchMoviesAsync = createAsyncThunk(
  'movies/search',
  async (query) => {
    const response = await api.get(`/search/movie?query=${query}`);
    return response.data.results;
  }
);


const movieSlice = createSlice({
    searchResults: [],
  // other reducers
  extraReducers: (builder) => {
    builder.addCase(searchMoviesAsync.fulfilled, (state, action) => {
      state.searchResults = action.payload;
    });
  },
});

export default movieSlice.reducer;
```
### `src/redux/movieSlice.js (main)`

```javascript
  import { createSlice, createAsyncThunk } from '@reduxjs/toolkit';
import { getPopularMovies, getTrendingMovies } from '../utils/api';
import api  from '../utils/api';

export const fetchPopularMovies = createAsyncThunk(
  'movies/fetchPopular',
  async () => {
    const response = await getPopularMovies();
    return response.data.results;
  }
);
export const searchMoviesAsync = createAsyncThunk(
    'movies/search',
    async (query) => {
      const response = await api.get(`/search/movie?query=${query}`);
      return response.data.results;
    }
  );

export const fetchTrendingMovies = createAsyncThunk(
  'movies/fetchTrending',
  async () => {
    const response = await getTrendingMovies();
    return response.data.results;
  }
);

const movieSlice = createSlice({
  name: 'movies',
  initialState: {
    popular: [],
    trending: [],
    searchResults: [],
    loading: false,
    error: null,
  },
  reducers: {
    
  },
  extraReducers: (builder) => {
    builder
      .addCase(fetchPopularMovies.pending, (state) => {
        state.loading = true;
      })
      .addCase(fetchPopularMovies.fulfilled, (state, action) => {
        state.loading = false;
        state.popular = action.payload;
      })
      .addCase(fetchPopularMovies.rejected, (state, action) => {
        state.loading = false;
        state.error = action.error.message;
      })
      builder.addCase(searchMoviesAsync.fulfilled, (state, action) => {
        state.searchResults = action.payload;
      })
      .addCase(fetchTrendingMovies.fulfilled, (state, action) => {
        state.trending = action.payload;
      });
  },
});  
export default movieSlice.reducer;
```

`src/pages/Search.js`

```javascript
import React, { useEffect } from 'react';
import { useDispatch, useSelector } from 'react-redux';
import { useLocation } from 'react-router-dom';
import { Container, Typography, Grid } from '@mui/material';
import { searchMoviesAsync } from '../redux/movieSlice';
import MovieCard from '../components/MovieCard';
import Loading from '../components/Loading';

function useQuery() {
  return new URLSearchParams(useLocation().search);
}

function Search() {
  const query = useQuery();
  const searchQuery = query.get('q');
  const dispatch = useDispatch();
  const { searchResults, loading } = useSelector((state) => state.movies);

  useEffect(() => {
    if (searchQuery) {
      dispatch(searchMoviesAsync(searchQuery));
    }
  }, [dispatch, searchQuery]);

  if (loading) {
    return <Loading message="Searching movies..." />;
  }

  return (
    <Container sx={{ py: 4 }}>
      <Typography variant="h4" gutterBottom>
        Search Results for "{searchQuery}"
      </Typography>
      <Grid container spacing={3}>
        {searchResults.map((movie) => (
          <Grid item xs={12} sm={6} md={4} lg={3} key={movie.id}>
            <MovieCard movie={movie} />
          </Grid>
        ))}
      </Grid>
    </Container>
  );
}

export default Search;
```

### `src/pages/App.js (updated)`

```javascript

import Home from './pages/Home';
import Search from './pages/Search';
import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';

<Router>
          <Navbar />
          <Box sx={{ mt: 8 }}>
            <Routes>
              <Route path="/" element={<Home />} />
              <Route path="/search" element={<Search />} />
            </Routes>
          </Box>
        </Router>

```
### `src/pages/App.js(main)`
```javascript
  import React from 'react';
  import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';
  import { ThemeProvider, CssBaseline, Box } from '@mui/material';
  import { Provider } from 'react-redux';
  import theme from './styles/theme';
  import Navbar from './components/NavBar';
  import { store } from './redux/store';
  
  // Pages
  import Home from './pages/Home';
  import Search from './pages/Search';
  
  function App() {
    return (
      <Provider store={store}>
        <ThemeProvider theme={theme}>
          <CssBaseline />
          <Router>
            <Navbar />
            <Box sx={{ mt: 8 }}>
              <Routes>
                <Route path="/" element={<Home />} />
                <Route path="/search" element={<Search />} />
              </Routes>
            </Box>
          </Router>
        </ThemeProvider>
      </Provider>
    );
  }
  
  export default App;
```
### `Installations`
```
npm install react-router-dom
```
