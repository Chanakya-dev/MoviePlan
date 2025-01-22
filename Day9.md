
### ` src/redux/MovieSlice.js(updated) `
```javascript
import { createSlice } from '@reduxjs/toolkit';

const movieSlice = createSlice({
  name: 'movies',
  initialState: {
    watchlist: [],
  },
  reducers: {
    addToWatchlist: (state, action) => {
      state.watchlist.push(action.payload);
    },
    removeFromWatchlist: (state, action) => {
      state.watchlist = state.watchlist.filter(
        (movie) => movie.id !== action.payload.id
      );
    },
  },
});

export const {
  addToWatchlist,
  removeFromWatchlist,
} = movieSlice.actions;

export default movieSlice.reducer;

```
### ` src/redux/MovieSlice.js(main) `
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
    watchlist: [],
    searchResults: [],
    loading: false,
    error: null,
  },
  reducers: {
    addToWatchlist: (state, action) => {
        state.watchlist.push(action.payload);
      },
      removeFromWatchlist: (state, action) => {
        state.watchlist = state.watchlist.filter(
          (movie) => movie.id !== action.payload.id
        );
        
      },
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
export const {
    addToWatchlist,
    removeFromWatchlist,
  } = movieSlice.actions;
  
export default movieSlice.reducer;
```    

### ` src/components/MovieCard.js(update) `
```javascript
import { Bookmark, BookmarkBorder } from '@mui/icons-material';
import { useDispatch, useSelector } from 'react-redux';
import { addToWatchlist, removeFromWatchlist } from '../redux/movieSlice';

function MovieCard({ movie }) {
  const dispatch = useDispatch();
  
  const watchlist = useSelector((state) => state.movies.watchlist);
  const isInWatchlist = watchlist.some((m) => m.id === movie.id);

  const handleWatchlistClick = (e) => {
    e.stopPropagation();
    if (isInWatchlist) {
      dispatch(removeFromWatchlist(movie));
    } else {
      dispatch(addToWatchlist(movie));
    }
  };

// Add on Code  
  return (
      <IconButton
        onClick={handleWatchlistClick}
        sx={{
          position: 'absolute',
          top: 8,
          right: 8,
          color: isInWatchlist ? 'primary.main' : 'text.secondary', // Change icon color based on watchlist status
        }}
      >
        {isInWatchlist ? <Bookmark /> : <BookmarkBorder />}
      </IconButton>
  );
}

export default MovieCard;

```

### ` src/components/MovieCard.js(main) `
  
  ```javascript
    import React from 'react';
import {
  Card,
  CardContent,
  Typography,
  CardMedia,
  IconButton,
} from '@mui/material';
import { Bookmark, BookmarkBorder } from '@mui/icons-material';
import { useDispatch, useSelector } from 'react-redux';
import { addToWatchlist, removeFromWatchlist } from '../redux/movieSlice';

function MovieCard({ movie }) {
  const dispatch = useDispatch();
  
  const watchlist = useSelector((state) => state.movies.watchlist);
  const isInWatchlist = watchlist.some((m) => m.id === movie.id);

  const handleWatchlistClick = (e) => {
    e.stopPropagation();
    if (isInWatchlist) {
      dispatch(removeFromWatchlist(movie));
    } else {
      dispatch(addToWatchlist(movie));
    }
  };

  
  return (
    <Card
      sx={{
        height: '100%',
        display: 'flex',
        flexDirection: 'column',
        cursor: 'pointer',
        borderRadius: 2,
        boxShadow: 3,
        position: 'relative', // Ensure the IconButton is positioned correctly
      }}
    >
      <CardMedia
        component="img"
        height="250"
        image={`https://image.tmdb.org/t/p/w500${movie.poster_path}`} // Use the movie poster URL
        alt={movie.title}
        sx={{ objectFit: 'cover', borderRadius: 2 }}
      />
      <CardContent sx={{ flexGrow: 1 }}>
        <Typography gutterBottom variant="h6" component="div">
          {movie.title}
        </Typography>
        <Typography variant="body2" color="textSecondary">
          {movie.release_date ? movie.release_date.slice(0, 4) : 'N/A'} {/* Display release year */}
        </Typography>
      </CardContent>
      <IconButton
        onClick={handleWatchlistClick}
        sx={{
          position: 'absolute',
          top: 8,
          right: 8,
          color: isInWatchlist ? 'primary.main' : 'text.secondary', // Change icon color based on watchlist status
        }}
      >
        {isInWatchlist ? <Bookmark /> : <BookmarkBorder />}
      </IconButton>
    </Card>
  );
}

export default MovieCard;
```



### ` src/pages/Watchlist.js `
  ```javascript
  import React from 'react';
import { useSelector } from 'react-redux';
import { Container, Typography, Grid } from '@mui/material';
import MovieCard from '../components/MovieCard'; // Ensure the path to MovieCard is correct

function Watchlist() {
  const watchlist = useSelector((state) => state.movies.watchlist);

  return (
    <Container>
      <Typography variant="h4" gutterBottom>
        My Watchlist
      </Typography>
      <Grid container spacing={3}>
        {watchlist.map((movie) => (
          <Grid item xs={12} sm={6} md={4} key={movie.id}>
            <MovieCard movie={movie} />
          </Grid>
        ))}
      </Grid>
    </Container>
  );
}

export default Watchlist;
  ```


### ` src/components/NavBar.js(updated)`  

```javascript
import BookmarkIcon from '@mui/icons-material/Bookmark';
import { useNavigate } from 'react-router-dom';
import BookmarkIcon from '@mui/icons-material/Bookmark';

const handleWatchlistClick = () => {
    navigate('/watchlist');
  };

 <Button
          color="inherit"
          onClick={handleWatchlistClick}
          sx={{ display: { xs: 'none', md: 'block' } }}
        >
          Watchlist
        </Button>
```
### ` src/components/NavBar.js(main)` 
  
  ```javascript
import React, { useState } from 'react';
import { useNavigate } from 'react-router-dom';
import { AppBar, Toolbar, Typography, Box, InputBase, IconButton, Button } from '@mui/material';
import { styled, alpha } from '@mui/material/styles';
import SearchIcon from '@mui/icons-material/Search';
import BookmarkIcon from '@mui/icons-material/Bookmark';

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

  const handleWatchlistClick = () => {
    navigate('/watchlist');
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
        <IconButton color="inherit" onClick={handleWatchlistClick}>
          <BookmarkIcon />
        </IconButton>
        <Button
          color="inherit"
          onClick={handleWatchlistClick}
          sx={{ display: { xs: 'none', md: 'block' } }}
        >
          Watchlist
        </Button>
      </Toolbar>
    </AppBar>
  );
}

export default Navbar;

  ```

### ` src/App.js(update)` 

```javascript

    import Watchlist from './pages/Watchlist';
    <Routes>
                <Route path="/" element={<Home />} />
                <Route path="/search" element={<Search />} />
                <Route path="/watchlist" element={<Watchlist />} />
              </Routes>
```

### ` src/App.js(main)` 
```javascript
import React from 'react';
import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';
import { ThemeProvider, CssBaseline, Box, Grid } from '@mui/material';
import { Provider } from 'react-redux';
import theme from './styles/theme';
import Navbar from './components/NavBar';
import { store } from './redux/store';
import Home from './pages/Home';
import Search from './pages/Search';
import Watchlist from './pages/Watchlist';

function App() {
  return (
    <Provider store={store}>
      <ThemeProvider theme={theme}>
        <CssBaseline />
        <Router>
          <Navbar />
          <Box sx={{ display: 'flex', mt: 8 }}>
            {/* Sidebar */}
         

              <Routes>
                <Route path="/" element={<Home />} />
                <Route path="/search" element={<Search />} />
                <Route path="/watchlist" element={<Watchlist />} />
              </Routes>
            </Box>
        </Router>
      </ThemeProvider>
    </Provider>
  );
}

export default App;
```
