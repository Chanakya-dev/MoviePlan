### `src/utils/api.js`

```javascript
import axios from 'axios';

const api = axios.create({
  baseURL: 'https://api.themoviedb.org/3',
  headers: {
    Authorization: `Bearer ${process.env.REACT_APP_TMDB_ACCESS_TOKEN}`,
    'Content-Type': 'application/json',
  },
});

export const getPopularMovies = () => api.get('/movie/popular');
export const getTrendingMovies = () => api.get('/trending/movie/day');

export default api;
```

### `src/redux/movieSlice.js`

```javascript
import { createSlice, createAsyncThunk } from '@reduxjs/toolkit';
import { getPopularMovies, getTrendingMovies } from '../utils/api';

export const fetchPopularMovies = createAsyncThunk(
  'movies/fetchPopular',
  async () => {
    const response = await getPopularMovies();
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
    loading: false,
    error: null,
  },
  reducers: {},
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
      .addCase(fetchTrendingMovies.fulfilled, (state, action) => {
        state.trending = action.payload;
      });
  },
});

export default movieSlice.reducer;
```

### `src/pages/Home.js`

```javascript
import React, { useEffect } from 'react';
import { useDispatch, useSelector } from 'react-redux';
import { fetchPopularMovies, fetchTrendingMovies } from '../redux/movieSlice';

function Home() {
  const dispatch = useDispatch();
  const { popular, trending, loading } = useSelector((state) => state.movies);

  useEffect(() => {
    dispatch(fetchPopularMovies());
    dispatch(fetchTrendingMovies());
  }, [dispatch]);

  if (loading) {
    return <h2>Loading...</h2>;
  }

  return (
    <div style={{ padding: '20px' }}>
      <h2>Popular Movies</h2>
      <ul>
        {popular.map((movie) => (
          <li key={movie.id}>{movie.title}</li>
        ))}
      </ul>

      <h2>Trending Movies</h2>
      <ul>
        {trending.map((movie) => (
          <li key={movie.id}>{movie.title}</li>
        ))}
      </ul>
    </div>
  );
}

export default Home;
```

---
