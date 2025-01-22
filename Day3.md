#### `src/styles/theme.js`

```js
import { createTheme } from '@mui/material/styles';

const theme = createTheme({
  palette: {
    mode: 'dark',
    primary: {
      main: '#e50914', // Netflix-like red
    },
    background: {
      default: '#141414',
      paper: '#1f1f1f',
    },
  },
  typography: {
    fontFamily: [
      'Roboto',
      '"Helvetica Neue"',
      'Arial',
      'sans-serif',
    ].join(','),
  },
});

export default theme;
```


#### `src/components/Navbar.js`

```js
import React from 'react';
import { AppBar, Toolbar, Typography } from '@mui/material';

function Navbar() {
  return (
    <AppBar position="fixed">
      <Toolbar>
        <Typography variant="h6" component="div">
          Movie App
        </Typography>
      </Toolbar>
    </AppBar>
  );
}

export default Navbar;
```


#### `src/App.js (main)`

```js
import React from 'react';
import { ThemeProvider, CssBaseline, Box } from '@mui/material';
import theme from './styles/theme';
import Navbar from './components/NavBar';
import Home from './pages/Home';


function App() {
  return (
    <ThemeProvider theme={theme}>
      <CssBaseline />
   
        <Navbar />
        <Box sx={{ mt: 8 }}>
          <Home/>
        </Box>
    
    </ThemeProvider>
  );
}

export default App;
```
---
### `Installations`

```
npm install @mui/material @emotion/react @emotion/styled @mui/icons-material
```

