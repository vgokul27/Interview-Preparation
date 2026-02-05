# Frontend Interview Preparation - Recipe Finder Application

## 📋 Overview
This document covers essential React and frontend concepts used in the Recipe Finder application to help you prepare for technical interviews.

---

## 🎯 Core React Hooks

### 1. `useState()` - State Management

**What it does**: Creates and manages component state (data that changes over time).

```javascript
const [recipes, setRecipes] = useState([]);
const [loading, setLoading] = useState(false);
const [currentPage, setCurrentPage] = useState(1);
```

**Key Points**:
- Returns array with 2 elements: `[currentValue, setterFunction]`
- Setter function triggers re-render when state changes
- Initial value passed as argument: `useState(initialValue)`
- State persists between re-renders

**Interview Questions**:
- *Q: Why do we use useState instead of regular variables?*
  - **A**: Regular variables reset on every re-render. useState preserves values and triggers re-renders when updated.

- *Q: What happens when you call setRecipes()?*
  - **A**: React schedules a re-render with the new value. The component re-executes with updated state.

**In Your App**:
```javascript
// Managing recipe list
const [recipes, setRecipes] = useState([]);
setRecipes(data.data); // Updates recipes and re-renders

// Managing loading state for user feedback
const [loading, setLoading] = useState(false);
setLoading(true); // Show spinner
```

---

### 2. `useEffect()` - Side Effects & Lifecycle

**What it does**: Runs code after component renders (API calls, subscriptions, DOM updates).

```javascript
useEffect(() => {
  if (!isSearching) {
    fetchRecipes();
  } else {
    handleSearchWithPagination();
  }
}, [currentPage, itemsPerPage]); // Dependency array
```

**Key Points**:
- Runs after every render by default
- **Dependency array** controls when it runs:
  - `[]` - Runs once on mount (like componentDidMount)
  - `[value]` - Runs when `value` changes
  - No array - Runs after every render
- Return function = cleanup (like componentWillUnmount)

**Interview Questions**:
- *Q: What is the dependency array for?*
  - **A**: It tells React when to re-run the effect. Only re-runs if dependencies change.

- *Q: What happens if you forget the dependency array?*
  - **A**: Effect runs after EVERY render, potentially causing infinite loops if it updates state.

- *Q: When would you return a cleanup function?*
  - **A**: To clean up subscriptions, timers, or event listeners when component unmounts or before re-running effect.

**In Your App**:
```javascript
// Fetch data when page or items per page changes
useEffect(() => {
  if (!isSearching) {
    fetchRecipes(); // Normal pagination
  } else {
    handleSearchWithPagination(); // Search pagination
  }
}, [currentPage, itemsPerPage]); // Re-fetch when these change
```

---

## 🔄 Async/Await & API Calls

### 3. `async/await` Pattern

**What it does**: Handles asynchronous operations (API calls) in a cleaner way than promises.

```javascript
const fetchRecipes = async () => {
  setLoading(true);
  setError(null);
  try {
    const data = await recipeAPI.getAllRecipes(currentPage, itemsPerPage);
    setRecipes(data.data);
    setTotalPages(data.totalPages);
  } catch (err) {
    setError('Failed to fetch recipes...');
  } finally {
    setLoading(false);
  }
};
```

**Key Points**:
- `async` makes function return a Promise
- `await` pauses execution until Promise resolves
- **Always use try-catch** for error handling
- `finally` always runs (cleanup)

**Interview Questions**:
- *Q: What's the difference between async/await and .then()?*
  - **A**: Same functionality, but async/await is more readable and easier to handle errors with try-catch.

- *Q: Why do we need try-catch?*
  - **A**: To catch errors from failed API calls (network issues, 404, 500 errors) and handle them gracefully.

**In Your App**:
```javascript
const handleSearch = async (filters) => {
  setLoading(true);      // Show loading spinner
  setError(null);        // Clear previous errors
  setIsSearching(true);  // Enter search mode
  
  try {
    // Wait for API response
    const data = await recipeAPI.searchRecipes(filters, 1, itemsPerPage);
    setRecipes(data.data);     // Update UI with results
    setTotalRecipes(data.total);
  } catch (err) {
    // Handle errors (show error message)
    setError('Search failed...');
  } finally {
    // Always stop loading spinner
    setLoading(false);
  }
};
```

---

## 📡 Axios API Integration

### 4. Axios HTTP Client

**What it does**: Makes HTTP requests to backend API (cleaner than fetch()).

```javascript
import axios from 'axios';

export const recipeAPI = {
  getAllRecipes: async (page = 1, limit = 15) => {
    const response = await axios.get(`${API_BASE_URL}/recipes`, {
      params: { page, limit }
    });
    return response.data;
  }
};
```

**Key Points**:
- Automatically parses JSON (unlike fetch)
- Better error handling
- Interceptor support for auth tokens
- `params` object automatically builds query string

**Interview Questions**:
- *Q: Why use Axios instead of fetch()?*
  - **A**: Axios auto-parses JSON, has better error handling, supports request/response interceptors, and works in older browsers.

- *Q: How does the params object work?*
  - **A**: Axios converts `{ page: 1, limit: 15 }` to `?page=1&limit=15` query string.

**In Your App**:
```javascript
// GET request with query parameters
getAllRecipes: async (page = 1, limit = 15) => {
  const response = await axios.get(`${API_BASE_URL}/recipes`, {
    params: { page, limit } // Becomes ?page=1&limit=15
  });
  return response.data;
}

// Search with multiple filters
searchRecipes: async (filters, page = 1, limit = 15) => {
  const response = await axios.get(`${API_BASE_URL}/recipes/search`, {
    params: { ...filters, page, limit } // Spread operator
  });
  return response.data;
}
```

---

## 🎨 Framer Motion Animations

### 5. Framer Motion - Animation Library

**What it does**: Adds smooth animations and transitions to React components.

```javascript
import { motion } from 'framer-motion';

<motion.div
  initial={{ opacity: 0, y: 20 }}
  animate={{ opacity: 1, y: 0 }}
  transition={{ delay: 0.1 }}
>
  Content
</motion.div>
```

**Key Points**:
- Replace HTML tags with `motion.div`, `motion.button`, etc.
- `initial` - Starting state
- `animate` - End state
- `transition` - How to animate
- `whileHover`/`whileTap` - Interaction states

**Interview Questions**:
- *Q: Why use Framer Motion instead of CSS transitions?*
  - **A**: More control, easier syntax, physics-based animations, gesture support, and better for complex animations.

- *Q: What does AnimatePresence do?*
  - **A**: Allows exit animations when components unmount (like closing a modal).

**In Your App**:
```javascript
// Fade in header from top
<motion.header
  initial={{ y: -100 }}
  animate={{ y: 0 }}
  className="..."
>

// Stagger animation for table rows
<motion.tr
  initial={{ opacity: 0, y: 20 }}
  animate={{ opacity: 1, y: 0 }}
  transition={{ delay: index * 0.05 }} // Each row delays more
>

// Button hover/tap effects
<motion.button
  whileHover={{ scale: 1.05 }}
  whileTap={{ scale: 0.95 }}
  onClick={handleClick}
>

// Drawer with exit animation
<AnimatePresence>
  {isOpen && (
    <motion.div
      initial={{ x: '100%' }}
      animate={{ x: 0 }}
      exit={{ x: '100%' }}
      transition={{ type: 'spring', damping: 25 }}
    />
  )}
</AnimatePresence>
```

---

## 🎯 Event Handlers

### 6. Event Handling Functions

**What it does**: Responds to user interactions (clicks, form submissions, input changes).

```javascript
const handlePageChange = (page) => {
  setCurrentPage(page);
  window.scrollTo({ top: 0, behavior: 'smooth' });
};

const handleRecipeClick = (recipe) => {
  setSelectedRecipe(recipe);
  setIsDrawerOpen(true);
};
```

**Key Points**:
- Named with `handle` prefix (convention)
- Pass as props: `onClick={handleClick}`
- Arrow functions for inline handlers: `onClick={() => handleClick(id)}`
- Prevent default with `e.preventDefault()`

**Interview Questions**:
- *Q: What's the difference between onClick={handleClick} vs onClick={handleClick()}?*
  - **A**: First passes the function, second calls it immediately. Use `() =>` when passing arguments: `onClick={() => handleClick(id)}`

- *Q: Why use e.preventDefault()?*
  - **A**: Stops default browser behavior (like form submission or link navigation).

**In Your App**:
```javascript
// Handle form submission
const handleSearch = async (filters) => {
  // Prevent page reload
  setLoading(true);
  // Make API call
  const data = await recipeAPI.searchRecipes(filters, 1, itemsPerPage);
  setRecipes(data.data);
};

// Handle clicks with parameters
const handleRecipeClick = (recipe) => {
  setSelectedRecipe(recipe);  // Store clicked recipe
  setIsDrawerOpen(true);      // Open detail drawer
};

// Handle page changes
const handlePageChange = (page) => {
  setCurrentPage(page);       // Update page number
  window.scrollTo({ top: 0, behavior: 'smooth' }); // Scroll to top
};
```

---

## 🧩 Component Props

### 7. Props - Component Communication

**What it does**: Passes data from parent to child components.

```javascript
// Parent passes props
<RecipeTable
  recipes={recipes}
  onRecipeClick={handleRecipeClick}
  loading={loading}
/>

// Child receives props
const RecipeTable = ({ recipes, onRecipeClick, loading }) => {
  // Use props
}
```

**Key Points**:
- Props are **read-only** (immutable)
- Use destructuring: `{ prop1, prop2 }`
- Callback props for child-to-parent communication
- Children prop for nested content

**Interview Questions**:
- *Q: Can you modify props inside a component?*
  - **A**: No! Props are read-only. Use state if you need to modify data.

- *Q: How do child components communicate with parents?*
  - **A**: Through callback props. Parent passes function, child calls it with data.

**In Your App**:
```javascript
// Passing multiple props
<RecipeTable
  recipes={recipes}              // Array of recipes
  onRecipeClick={handleRecipeClick} // Callback function
  loading={loading}              // Boolean state
/>

// Pagination component
<Pagination
  currentPage={currentPage}
  totalPages={totalPages}
  onPageChange={handlePageChange}        // Callback
  itemsPerPage={itemsPerPage}
  onItemsPerPageChange={handleItemsPerPageChange} // Callback
/>

// Drawer with conditional rendering
<RecipeDrawer
  recipe={selectedRecipe}        // Object or null
  isOpen={isDrawerOpen}          // Boolean
  onClose={handleCloseDrawer}    // Callback
/>
```

---

## 📊 Array Methods & Rendering

### 8. `.map()` - Array Iteration

**What it does**: Transforms array into JSX elements for rendering.

```javascript
{recipes.map((recipe, index) => (
  <motion.tr key={recipe._id}>
    <td>{recipe.title}</td>
    <td>{recipe.cuisine}</td>
  </motion.tr>
))}
```

**Key Points**:
- Always use unique `key` prop (preferably ID)
- Returns new array of JSX elements
- Common with API data

**Interview Questions**:
- *Q: Why do we need the key prop?*
  - **A**: React uses keys to identify which items changed/added/removed. Improves rendering performance and prevents bugs.

- *Q: Can we use array index as key?*
  - **A**: Only if list never reorders. Better to use unique IDs from database.

**In Your App**:
```javascript
// Rendering recipe rows
{recipes.map((recipe, index) => (
  <motion.tr
    key={recipe._id}  // Unique database ID
    onClick={() => onRecipeClick(recipe)}
  >
    <td>{recipe.title || 'Untitled Recipe'}</td>
    <td>{recipe.cuisine || 'N/A'}</td>
    <td><StarRating rating={recipe.rating} /></td>
  </motion.tr>
))}

// Rendering filter badges
{Object.entries(activeFilters).map(([key, value]) => (
  <span key={key} className="badge">
    {key}: {value}
  </span>
))}

// Rendering ingredients
{recipe.ingredients.map((ingredient, index) => (
  <li key={index}>
    <span>•</span>
    <span>{ingredient}</span>
  </li>
))}
```

---

## 🎭 Conditional Rendering

### 9. Conditional UI Display

**What it does**: Shows/hides components based on conditions.

```javascript
// && operator (short-circuit)
{loading && <Spinner />}

// Ternary operator
{isSearching ? <SearchResults /> : <AllRecipes />}

// Early return
if (loading) return <Spinner />;
```

**Key Points**:
- `&&` for simple show/hide
- Ternary `? :` for either/or
- Early return for guard clauses
- Falsy values render nothing

**Interview Questions**:
- *Q: What's the difference between && and ternary?*
  - **A**: `&&` shows something or nothing. Ternary shows one thing OR another.

- *Q: Why not use if/else statements in JSX?*
  - **A**: JSX only accepts expressions, not statements. Use ternary or move logic outside JSX.

**In Your App**:
```javascript
// Show loading spinner
{loading && (
  <div className="spinner">
    <div className="animate-spin..."></div>
  </div>
)}

// Show pagination only when data exists
{!loading && recipes.length > 0 && (
  <Pagination {...props} />
)}

// Different messages for search vs normal
<p>
  {isSearching 
    ? "No recipes match your search criteria."
    : "No recipes found in database."}
</p>

// Show total recipes only when not searching
{!isSearching && totalRecipes > 0 && (
  <div>
    <p>Total Recipes</p>
    <p>{totalRecipes.toLocaleString()}</p>
  </div>
)}
```

---

## 🛠️ Important Utility Functions

### 10. Object/Array Destructuring

**What it does**: Extracts values from objects/arrays into variables.

```javascript
// Object destructuring
const { data, total, totalPages } = response.data;

// Array destructuring
const [recipes, setRecipes] = useState([]);

// Props destructuring
const RecipeTable = ({ recipes, onRecipeClick, loading }) => {
```

**Key Points**:
- Cleaner than dot notation
- Can rename: `{ data: recipeData }`
- Default values: `{ page = 1 }`
- Nested destructuring possible

---

### 11. Spread Operator

**What it does**: Copies or merges objects/arrays.

```javascript
// Spread array
params: { ...filters, page, limit }

// Spread props
<Pagination {...paginationProps} />
```

**Key Points**:
- Creates shallow copy
- Merges objects: `{ ...obj1, ...obj2 }`
- Add/override properties: `{ ...filters, page: 1 }`

**In Your App**:
```javascript
// Merge filters with pagination
const response = await axios.get(url, {
  params: { ...filters, page, limit }
});
// If filters = { title: 'pasta', cuisine: 'Italian' }
// Becomes: { title: 'pasta', cuisine: 'Italian', page: 1, limit: 15 }

// Pass all props at once
<Pagination {...paginationProps} />
```

---

### 12. Short-Circuit Evaluation

**What it does**: Returns first truthy/falsy value.

```javascript
recipe.title || 'Untitled Recipe'  // Fallback value
recipe.rating && <StarRating />    // Conditional render
```

**Key Points**:
- `||` returns first truthy value (default values)
- `&&` returns first falsy or last value (conditional render)
- `??` nullish coalescing (only for null/undefined)

---

## 🎯 Common Interview Questions & Answers

### React Fundamentals

**Q: What is React and why use it?**
- **A**: React is a JavaScript library for building user interfaces. It uses a component-based architecture, virtual DOM for performance, and declarative syntax for easier development.

**Q: What is the Virtual DOM?**
- **A**: A lightweight copy of the real DOM. React updates the virtual DOM first, compares it with previous version (diffing), and only updates changed parts in real DOM (reconciliation). This is faster than updating real DOM directly.

**Q: What's the difference between state and props?**
- **A**: 
  - **State**: Mutable data owned by component, managed with useState
  - **Props**: Immutable data passed from parent, read-only

**Q: What are React Hooks?**
- **A**: Functions that let you use state and lifecycle features in function components. Examples: useState, useEffect, useContext.

### Your Application Specific

**Q: How does pagination work in your app?**
- **A**: 
  1. Store `currentPage` and `itemsPerPage` in state
  2. Pass to API call: `getAllRecipes(currentPage, itemsPerPage)`
  3. Backend returns paginated data + total pages
  4. Pagination component calls `handlePageChange(newPage)`
  5. useEffect detects page change and re-fetches data

**Q: How do you handle loading states?**
- **A**: 
  1. Set `loading` to true before API call
  2. Show loading spinner with conditional: `{loading && <Spinner />}`
  3. Set `loading` to false in `finally` block (always runs)
  4. Hide content while loading to prevent flashing

**Q: Explain the search functionality**
- **A**:
  1. User enters filters in form
  2. `handleSearch` sets `isSearching` to true
  3. Calls `searchRecipes(filters, page, limit)`
  4. Backend filters MongoDB documents and returns paginated results
  5. Update state with results and show pagination
  6. Pagination works same as regular browsing

**Q: How does the drawer work?**
- **A**:
  1. Click recipe row → calls `handleRecipeClick(recipe)`
  2. Sets `selectedRecipe` and `isDrawerOpen` to true
  3. RecipeDrawer renders with `recipe` prop
  4. AnimatePresence handles slide-in animation
  5. Click close → sets `isDrawerOpen` to false
  6. Exit animation plays before unmounting

### Performance & Optimization

**Q: How do you optimize React performance?**
- **A**: 
  - Use keys in lists for efficient re-rendering
  - Avoid inline functions in JSX (define outside)
  - Use React.memo() for expensive components
  - Lazy load components with React.lazy()
  - Debounce search inputs
  - Paginate large datasets

**Q: What causes unnecessary re-renders?**
- **A**: 
  - Parent re-renders → all children re-render
  - State updates trigger re-renders
  - Inline objects/arrays as props create new references
  - Missing dependency array in useEffect

---

## 💡 Pro Tips for Interview

### Code Explanation Strategy

1. **Start with the big picture**: "This function handles searching recipes with pagination"
2. **Explain the flow**: "First we set loading state, then call the API, then update state with results"
3. **Mention error handling**: "We use try-catch to handle API failures"
4. **Discuss edge cases**: "We reset to page 1 on new searches"

### Common Mistakes to Avoid

❌ **Don't say**: "I don't know what this does"
✅ **Instead say**: "This appears to be handling X, let me trace through it..."

❌ **Don't**: Memorize code without understanding
✅ **Do**: Understand the concepts and explain in your own words

❌ **Don't**: Skip over error handling
✅ **Do**: Mention try-catch, loading states, error messages

### Key Phrases to Use

- "This is a **controlled component** because we manage input state"
- "We use **dependency array** to optimize useEffect"
- "This is a **callback prop** for parent-child communication"
- "We **lift state up** to share between components"
- "We use **conditional rendering** to show different UI states"

---

## 📚 Study Checklist

### Core Concepts
- [ ] useState - Creating and updating state
- [ ] useEffect - Side effects and lifecycle
- [ ] async/await - Handling asynchronous operations
- [ ] Props vs State - When to use each
- [ ] Event handlers - onClick, onChange, onSubmit

### Your App Features
- [ ] Pagination logic (page, limit, total pages)
- [ ] Search with filters (how filters are applied)
- [ ] Loading states (when and how)
- [ ] Error handling (try-catch, error messages)
- [ ] Drawer functionality (open/close, animations)

### Advanced Topics
- [ ] Framer Motion animations
- [ ] Axios API calls vs fetch
- [ ] Conditional rendering patterns
- [ ] Array methods (map, filter, reduce)
- [ ] Spread operator and destructuring

### Performance
- [ ] Key prop in lists
- [ ] useEffect dependency arrays
- [ ] Preventing unnecessary re-renders
- [ ] Lazy loading concepts

---

## 🎓 Practice Questions

### Beginner
1. What does useState return?
2. When does useEffect run?
3. What is the key prop for?
4. How do you pass data to child components?

### Intermediate
1. Explain how pagination works in your app
2. What's the purpose of async/await?
3. How do you handle errors from API calls?
4. When would you use conditional rendering?

### Advanced
1. How does React know when to re-render?
2. Explain the data flow from API to UI in your app
3. How would you optimize a slow-rendering list?
4. What's the difference between controlled and uncontrolled components?

---

## 🚀 Final Tips

1. **Run the app** before interview - be familiar with all features
2. **Be ready to explain** any line of code in your components
3. **Discuss trade-offs** - "I used Axios because..." shows deeper understanding
4. **Mention best practices** - error handling, loading states, accessibility
5. **Be honest** - If you don't know something, explain your thought process

**Good luck with your interview! 🎯**
