### `src/pages/MovieDetails.js`

```javascript
import React, { useState, useEffect } from 'react';
import { useParams } from 'react-router-dom';

import { useDispatch } from 'react-redux';
import { Container, Grid, Typography, Box, Chip, Rating } from '@mui/material';
import api from '../utils/api'; // Ensure this points to your API utility
import Loading from '../components/Loading'; // Ensure this component exists and is correctly implemented

function MovieDetails() {
  const { id } = useParams();
  const [movie, setMovie] = useState(null);
  const [cast, setCast] = useState([]);
  const dispatch = useDispatch();

  useEffect(() => {
    const fetchData = async () => {
      try {
        const [movieRes, creditsRes] = await Promise.all([
          api.get(`/movie/${id}`),
          api.get(`/movie/${id}/credits`),
        ]);
        setMovie(movieRes.data);
        setCast(creditsRes.data.cast.slice(0, 10));
      } catch (error) {
        console.error('Failed to fetch movie details:', error);
      }
    };
    fetchData();
  }, [id]);

  if (!movie) {
    return <Loading />;
  }

  return (
    <Container>
      <Grid container spacing={4}>
        <Grid item xs={12} md={4}>
          <img
            src={`https://image.tmdb.org/t/p/w500${movie.poster_path}`}
            alt={movie.title}
            style={{ width: '100%', borderRadius: '8px' }}
          />
        </Grid>
        <Grid item xs={12} md={8}>
          <Typography variant="h3" gutterBottom>
            {movie.title}
          </Typography>
          <Rating value={movie.vote_average / 2} readOnly />
          <Typography paragraph>{movie.overview}</Typography>
          <Typography>Release Date: {movie.release_date}</Typography>
          <Box mt={2}>
            {cast.map((actor) => (
              <Chip
                key={actor.id}
                label={actor.name}
                sx={{ margin: '4px' }}
              />
            ))}
          </Box>
        </Grid>
      </Grid>
    </Container>
  );
}

export default MovieDetails;

```
### `src/componenets/MovieCard.js(update)`

```javascript
import { useNavigate } from 'react-router-dom';

 const navigate = useNavigate();

const handleCardClick = () => {
    navigate(`/movie/${movie.id}`); 
  };
```

### `src/components/MovieCard.js(main)`
```javascript
import React from 'react';
import { Card, CardContent, Typography, CardMedia } from '@mui/material';
import { addToWatchlist, removeFromWatchlist } from '../redux/movieSlice';
import { useDispatch, useSelector } from 'react-redux';
import {BookmarkBorder,Bookmark} from '@mui/icons-material';
import {IconButton} from '@mui/material';
import { useNavigate } from 'react-router-dom';

function MovieCard({ movie }) {
    const dispatch = useDispatch();
    const watchlist = useSelector((state) => state.movies.watchlist);
    const isInWatchlist = watchlist.some((m) => m.id === movie.id);
    const navigate = useNavigate();
    const handleCardClick = () => {
      navigate(`/movie/${movie.id}`); 
    };
    const handleWatchlistClick = (e) => {
        e.stopPropagation();
        if (isInWatchlist) {
          dispatch(removeFromWatchlist(movie));
        } else {
          dispatch(addToWatchlist(movie));
        }
      };  
  return (
    <Card onClick={handleCardClick}
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


### `src/App.js (update)`
```javascript
            <Routes>
              <Route path="/" element={<Home />} />
              <Route path="/search" element={<Search />} />
              <Route path='/movie/:id' element={<MovieDetails />} />
              <Route path="/watchlist" element={<Watchlist />} />
            </Routes>
```

### `src/App.js (main)`

```javascript
import React from 'react';
import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';
import { ThemeProvider, CssBaseline, Box } from '@mui/material';
import { Provider } from 'react-redux';
import theme from './styles/theme';
import Navbar from './components/NavBar';
import { store } from './redux/store';

import GenreDrawer from './components/GenreDrawer';
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
          <Box
            component="aside"
            sx={{
              width: { xs: '100%', sm: '240px' }, // Full width on small screens, fixed on larger
              flexShrink: 0,
              position: 'fixed',
              height: 'calc(100vh - 64px)', // Subtract Navbar height
              overflowY: 'auto',
              overflowX:'hidden',
              borderRight: '1px solid #e0e0e0',
              bgcolor: 'background.paper',
            }}
          >
            <GenreDrawer />
          </Box>

          {/* Main Content */}
          <Box
            component="main"
            sx={{
              flexGrow: 1,
              ml: { sm: '240px' }, // Leave space for the sidebar on larger screens
              p: 3,
            }}
          >
            <Routes>
              <Route path="/" element={<Home />} />
              <Route path="/search" element={<Search />} />
              <Route path="/watchlist" element={<Watchlist />} />
            </Routes>
          </Box>
        </Box>
        </Router>
      </ThemeProvider>
    </Provider>
  );
}

export default App;
```
