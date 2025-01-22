### `src/components/MovieCard.js`

```javascript
import React from 'react';
import { Card, CardContent, Typography, CardMedia } from '@mui/material';

function MovieCard({ movie }) {
  return (
    <Card
      sx={{
        height: '100%',
        display: 'flex',
        flexDirection: 'column',
        cursor: 'pointer',
      }}
    >
      <CardMedia
        component="img"
        image={`https://image.tmdb.org/t/p/w500${movie.poster_path}`} // Assuming poster_path is the key in your movie object
        alt={movie.title}
        sx={{
          height: 300,
          objectFit: 'cover',
        }}
      />
      <CardContent sx={{ flexGrow: 1 }}>
        <Typography gutterBottom variant="h6" component="div">
          {movie.title}
        </Typography>
        <Typography variant="body2" color="text.secondary">
          Rating: {movie.vote_average ? movie.vote_average.toFixed(1) : 'N/A'} / 10
        </Typography>
      </CardContent>
    </Card>
  );
}

export default MovieCard;

```
### `src/pages/Loading.js`

```javascript
import React from 'react';
import { Box, CircularProgress, Typography } from '@mui/material';

function Loading({ message = 'Loading...' }) {
  return (
    <Box
      sx={{
        display: 'flex',
        flexDirection: 'column',
        alignItems: 'center',
        justifyContent: 'center',
        minHeight: '50vh',
      }}
    >
      <CircularProgress size={40} sx={{ mb: 2 }} />
      <Typography color="text.secondary">{message}</Typography>
    </Box>
  );
}

export default Loading;
```


### `src/pages/Home.js(Updated)`

```javascript

  return (
    <Container sx={{ py: 4 }}>
      {/* Popular Movies */}
      <Typography variant="h4" gutterBottom>
        Popular Movies
      </Typography>
      <Grid container spacing={3} sx={{ mb: 4 }}>
  {popular.slice(0, 6).map((movie) => (
    <Grid item xs={12} sm={6} md={4} lg={3} key={movie.id}>
      <MovieCard movie={movie} />
    </Grid>
  ))}
</Grid>

      {/* Trending Now */}
      <Typography variant="h4" gutterBottom>
        Trending Now
      </Typography>
      <Grid container spacing={3}>
  {trending.slice(0, 6).map((movie) => (
    <Grid item xs={12} sm={6} md={4} lg={3} key={movie.id}>
      <MovieCard movie={movie} />
    </Grid>
  ))}
</Grid>
    </Container>
  );
}

```

### src/pages/Home.js(main)
````javascript
  import React, { useEffect } from 'react';
import { useDispatch, useSelector } from 'react-redux';
import { Container, Grid, Typography } from '@mui/material';
import { fetchPopularMovies, fetchTrendingMovies } from '../redux/movieSlice';
import Loading from '../components/Loading';
import MovieCard from '../components/MovieCard';

const IMAGE_BASE_URL = 'https://image.tmdb.org/t/p/w500';

function Home() {
  const dispatch = useDispatch();
  const { popular, trending, loading } = useSelector((state) => state.movies);

  useEffect(() => {
    dispatch(fetchPopularMovies());
    dispatch(fetchTrendingMovies());
  }, [dispatch]);

  if (loading) {
    return <Loading message="Fetching movies..." />;
  }

  return (
    <Container sx={{ py: 4 }}>
      <Typography variant="h4" gutterBottom>
        Popular Movies
      </Typography>
      <Grid container spacing={3} sx={{ mb: 4 }}>
  {popular.slice(0, 6).map((movie) => (
    <Grid item xs={12} sm={6} md={4} lg={3} key={movie.id}>
      <MovieCard movie={movie} />
    </Grid>
  ))}
</Grid>


      <Typography variant="h4" gutterBottom>
        Trending Now
      </Typography>
      <Grid container spacing={3}>
  {trending.slice(0, 6).map((movie) => (
    <Grid item xs={12} sm={6} md={4} lg={3} key={movie.id}>
      <MovieCard movie={movie} />
    </Grid>
  ))}
</Grid>
    </Container>
  );
}

export default Home;
````    
  
