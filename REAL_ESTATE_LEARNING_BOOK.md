# RealEstate.com — 30-Page Learning Book

**How to use this book.** Read one page at a time, open the named project file, and answer the two questions before moving forward. The pages are deliberately short so that you learn by inspecting and changing the application, not by memorizing definitions.

**Your project in one sentence:** this is a MERN-style real-estate marketplace with a React user interface, an Express API, MongoDB data, login protection, Google sign-in, and property listing search.

---

## Page 1 — Your learning map

You will learn these 20 topics: project architecture; Node and npm; Vite; React components; React state; routing; forms and fetch; Redux; persisted state; Express; middleware; REST APIs; MongoDB; Mongoose schemas; authentication; JWT and cookies; authorization; Google/Firebase OAuth; image storage; and search/pagination.

The frontend is in `client/`; the backend is in `API/`; database-related storage rules are in `supabase/`.

**Starting question:** Which folder contains code shown in the browser, and which folder contains code that runs on the server?

**Follow-up:** Why is it useful to keep browser code and server code separate?

---

## Page 2 — Study method and safety

Use this loop: read the file, predict what it does, run or trace one path, then explain it in your own words. Do not place secrets in source code. Environment variables such as `MONGO`, `JWT_SECRET`, and `VITE_FIREBASE_API_KEY` belong in a local `.env` file that is not committed.

Useful commands: `npm run dev` starts the API watcher at the project root; from `client/`, `npm run dev` starts Vite. `npm run build` checks whether the frontend can make a production build.

**Starting question:** What information would you expect in a `.env` file?

**Follow-up:** What could happen if a database password is committed to a public repository?

---

## Page 3 — Architecture: browser, API, database

Trace a “create listing” action: a React page gathers form data, `fetch` sends it to `/api/listing/create`, Express routes it to a controller, Mongoose writes a `Listing` document to MongoDB, and the API returns JSON.

Files to inspect: `client/src/Pages/CreateListing.jsx`, `API/Routes/listing.route.js`, `API/Controller/listing-controller.js`, and `API/Models/listing.model.js`.

**Starting question:** In this path, where does a user click, where is the request handled, and where is the data saved?

**Follow-up:** Which layer should decide whether the signed-in user may create a listing, and why?

---

## Page 4 — Node.js, npm, and package.json

Node.js runs JavaScript outside the browser. npm installs packages and runs scripts. The root `package.json` provides the backend scripts and packages such as Express and Mongoose. `client/package.json` separately provides React, Vite, Redux, Firebase, and UI libraries.

`package-lock.json` records exact installed versions so teammates get consistent dependencies.

**Starting question:** Why does this repository have two `package.json` files?

**Follow-up:** What is the difference between a dependency and an npm script?

---

## Page 5 — Vite and the React entry point

Vite is the frontend development/build tool. The browser loads `client/index.html`, which loads `client/src/main.jsx`. That file creates the React root and wraps `<App />` in Redux `Provider` and `PersistGate`.

This wrapper makes shared Redux state and its saved version available to the whole app.

**Starting question:** Which file is the first JavaScript entry point for the client?

**Follow-up:** What would break if `<App />` were not inside `<Provider store={store}>`?

---

## Page 6 — React components

A React component is a JavaScript function that returns UI. Components divide the interface into focused pieces. For example, `Header.jsx`, `ListingItem.jsx`, `Contact.jsx`, and `ImageSlider.jsx` are reusable components, while files in `Pages/` represent complete screens.

Props are inputs passed from a parent. `ListingItem({ listing })` receives one property listing and renders its name, picture, address, price, beds, and baths.

**Starting question:** What is one advantage of rendering a listing through `ListingItem` instead of repeating its markup on every page?

**Follow-up:** Name three values that the `listing` prop must provide for this component to display well.

---

## Page 7 — React state and events

State is data a component remembers between renders. In `ImageSlider.jsx`, `useState(0)` stores the current slide. Clicking an arrow calls an event handler, updates the state, and React renders the selected image.

The code uses a functional update (`setCurrentSlide(prev => ...)`) because the next value depends on the previous one. This is safer when updates are scheduled together.

**Starting question:** What value does `currentSlide` represent?

**Follow-up:** Why does the next-slide logic return to `0` after the last image?

---

## Page 8 — Client-side routing

`react-router-dom` changes screens without a full document reload. `App.jsx` maps URLs to pages, for example `/`, `/signin`, `/listing/:listingId`, and `/search`. A colon means a dynamic URL parameter.

`<Link>` creates internal navigation; `useNavigate` performs navigation from code after an action succeeds.

**Starting question:** If a listing ID is `abc123`, what route opens its detail page?

**Follow-up:** Why is a dynamic route better than making a separate hard-coded route for every listing?

---

## Page 9 — Forms, JSON, and fetch

Forms collect user input. The client sends an API request with `fetch`, usually setting `Content-Type: application/json` and converting a JavaScript object with `JSON.stringify`. The server enables `express.json()` so it can read that JSON from `req.body`.

The OAuth component shows the pattern: receive identity data, `POST` it to `/api/auth/google`, parse JSON response data, then update Redux state.

**Starting question:** Why is `JSON.stringify` used before sending an object in a request body?

**Follow-up:** What server middleware makes `req.body` available for JSON requests?

---

## Page 10 — Redux state management

Redux holds app-wide state in one store. This project’s user slice tracks `currentUser`, `loading`, and `error`. Components dispatch actions such as `signInStart` and `signInSuccess`; reducers describe how the state changes.

Inspect `client/src/Pages/Redux/User/UserSlice.js` and `Store.js`.

**Starting question:** Which state field tells the app who is currently signed in?

**Follow-up:** Why is a `loading` state useful while an API request is in progress?

---

## Page 11 — Persisting browser state

`redux-persist` saves selected Redux data in browser storage and restores it after a refresh. `Store.js` whitelists the `user` reducer, while `PersistGate` waits for restoration before displaying the app.

Persistence improves convenience, but it is not a replacement for server-side authentication. The server must still check the signed cookie for protected actions.

**Starting question:** What does the `whitelist: ['user']` setting preserve?

**Follow-up:** Why should a server not trust a browser’s saved Redux state as proof of identity?

---

## Page 12 — Express server basics

Express is the backend web framework. `API/Index.js` loads environment variables, connects Mongoose to MongoDB, creates `app`, registers middleware and route groups, then listens on port 3000.

The base paths are `/api/user`, `/api/auth`, and `/api/listing`. Each route module adds the final part of the URL.

**Starting question:** What is the full path for the auth signup endpoint?

**Follow-up:** Why are route groups clearer than defining every route in `Index.js`?

---

## Page 13 — Middleware and error handling

Middleware runs in sequence around requests. `express.json()` parses JSON, `cookieParser()` reads cookies, `verifyToken` checks authentication, and the final error middleware creates a consistent JSON error response.

Controllers call `next(error)` to hand an error to the global handler rather than duplicating error-response code everywhere.

**Starting question:** What job does `cookieParser()` perform in this project?

**Follow-up:** Why must the global error handler be registered after the routes?

---

## Page 14 — REST API design

An API endpoint combines an HTTP method and path. Examples: `POST /api/auth/signup` creates an account, `GET /api/listing/get/:id` reads one listing, `POST /api/listing/create` creates one, and `DELETE /api/listing/delete/:id` removes one.

Responses use status codes: `201` means created, `200` means success, `401`/`403` signal access problems, and `404` means not found.

**Starting question:** Which HTTP method should be used to read a listing without changing it?

**Follow-up:** When would you choose `404` instead of `403`?

---

## Page 15 — MongoDB documents and collections

MongoDB stores flexible JSON-like documents in collections. In this project, users live in the `User` collection and property records in the `Listing` collection. Each document has an `_id`, which is used in URLs and relationships.

For example, each listing includes `userRef`: the ID of the user who owns it.

**Starting question:** What is the relationship between a listing’s `userRef` and a user document’s `_id`?

**Follow-up:** Why is a document database a comfortable fit for property records with arrays such as `imageUrls`?

---

## Page 16 — Mongoose schemas and validation

Mongoose defines the expected shape of MongoDB documents. `user-model.js` requires a unique username and email. `listing.model.js` requires name, description, prices, address, beds, baths, flags, image URLs, and owner reference. Both schemas use timestamps.

Schema validation protects the database from missing required fields, but API-level validation should also give users helpful messages before a save is attempted.

**Starting question:** Which listing field is an array, and what does it store?

**Follow-up:** What timestamps does `{ timestamps: true }` add to a document?

---

## Page 17 — Password security

Passwords must not be stored as readable text. On signup, the API hashes the password with `bcryptjs.hashSync(password, 10)`. On sign-in, `compareSync` checks the supplied password against the stored hash.

The API removes the password from user data before returning it in a response. This is a vital habit even if the hash cannot be used as a normal password.

**Starting question:** Why can the app verify a password without ever decoding the saved hash?

**Follow-up:** Why should a user response omit the password field?

---

## Page 18 — JWTs and HTTP-only cookies

After a successful sign-in, the API creates a JWT whose payload contains the user ID. It sends the token in an `access_token` cookie marked `httpOnly`. Browser JavaScript cannot read an HTTP-only cookie, reducing exposure to some XSS attacks.

`verifyToken.js` reads the cookie, verifies it with `JWT_SECRET`, and assigns the verified payload to `req.user`.

**Starting question:** What information does this project place inside its JWT payload?

**Follow-up:** What protection does the `httpOnly` cookie option provide?

---

## Page 19 — Authorization and private routes

Authentication answers “who are you?” Authorization answers “may you do this?” `verifyToken` authenticates the request. Controllers then compare `req.user.id` with a URL ID or listing `userRef` before allowing update/delete actions.

On the frontend, `PrivateRoute.jsx` sends users without `currentUser` to `/signin`; this improves user experience, but the backend checks are the real security boundary.

**Starting question:** Why are both `PrivateRoute` and `verifyToken` useful?

**Follow-up:** What comparison prevents one user from deleting another user’s listing?

---

## Page 20 — Google sign-in with Firebase

`OAuth.jsx` opens a Firebase Google popup using `GoogleAuthProvider`. After Google returns a user, the client sends name, email, and photo URL to the project’s backend. The backend either finds the existing user or creates one, then issues the project’s JWT cookie.

This separates Google identity from your application’s own user record and authorization system.

**Starting question:** Why does the client send Google user data to the backend after the popup succeeds?

**Follow-up:** What must the backend do for a first-time Google user?

---

## Page 21 — Environment variables and Supabase

The Firebase and Supabase clients read settings through `import.meta.env.VITE_*`. Vite exposes only variables with the `VITE_` prefix to browser code, so they must be public client configuration—not secret server credentials.

`client/src/Components/supabase.js` creates a Supabase client, and `supabase/storage-policies.sql` belongs to storage access configuration. Avoid logging credentials, even during debugging.

**Starting question:** Why does a client-side Vite variable begin with `VITE_`?

**Follow-up:** Which kind of key must never be placed in the frontend environment file?

---

## Page 22 — Images and user-facing cards

Listings hold an `imageUrls` array. `ListingItem.jsx` chooses the first image, provides accessible alt text, formats the correct sale/rent price, and displays address, description, beds, and baths. It also handles a failed image load by logging diagnostic information.

`ImageSlider.jsx` is a separate reusable example of cycling through images with buttons and dots.

**Starting question:** Why does `ListingItem` use `listing?.imageUrls?.[0]` instead of directly reading `listing.imageUrls[0]`?

**Follow-up:** How would you improve the user experience when the first image fails to load?

---

## Page 23 — Search, filtering, sorting, and pagination

`getListings` accepts query parameters: `limit`, `startIndex`, `offer`, `furnished`, `parking`, `type`, `searchTerm`, `sort`, and `order`. MongoDB matches case-insensitive names with a regular expression, sorts results, limits their count, and skips earlier results.

For “load more,” request the same filters with a larger `startIndex`.

**Starting question:** Which query parameters are needed to request the next page after the first nine results?

**Follow-up:** Why should the backend limit result size rather than return every listing at once?

---

## Page 24 — Debugging and code quality

Use the browser console, Network tab, API terminal logs, and focused `console.log` statements to follow one request. Check status codes and response JSON before changing unrelated code. `client/package.json` supplies `npm run lint`; production changes should also pass `npm run build`.

Prefer user-friendly error states over console-only errors. Remove noisy diagnostic logs once the issue is fixed.

**Starting question:** If a listing card is blank, what two values would you inspect first?

**Follow-up:** Why is checking the browser Network response often faster than guessing at the React component?

---

## Page 25 — Security and improvement review

Before production, strengthen request validation, restrict accepted file types/sizes, set cookie `secure` and appropriate `sameSite` settings for HTTPS, add rate limiting, avoid logging keys, and validate allowed search/sort fields. Also review the Google-user field naming: the schema uses `profilePicture`, while the controller currently writes `avatar`; consistent field names matter.

**Starting question:** Why should sort fields be restricted to an approved list?

**Follow-up:** What visible bug might result from writing `avatar` when the schema/UI expects `profilePicture`?

---

## Page 26 — 20-topic completion checklist

Mark each only after you can explain it without looking:

- Architecture, Node/npm, Vite, components, state
- Routing, forms/fetch, Redux, persistence, Express
- Middleware, REST, MongoDB, Mongoose, password hashing
- JWT/cookies, authorization, Google/Firebase, image handling, search/pagination

**Starting question:** Which three topics currently feel least clear?

**Follow-up:** Which exact file will you reopen for each one?

---

## Page 27 — A five-session learning plan

Session 1: Pages 3–7 (frontend foundations). Session 2: Pages 8–11 (navigation and shared state). Session 3: Pages 12–16 (API and data). Session 4: Pages 17–20 (identity and access). Session 5: Pages 21–25 (storage, search, quality).

End each session by describing one request’s full journey from UI to database and back.

**Starting question:** Which session will you do first, and when?

**Follow-up:** What small, safe change could prove you understood that session?

---

## Page 28 — Mini-projects to prove learning

1. Add a fallback image to `ListingItem`.
2. Add a loading indicator while listings are fetched.
3. Add a maximum value validation for bedrooms.
4. Add an “only my listings” explanation when authorization fails.
5. Add a page-size selector that safely changes `limit`.

Make one change at a time and test the route it affects.

**Starting question:** Which mini-project builds on the topic you most want to practice?

**Follow-up:** What test result would show that your change worked?

---

## Page 29 — Your next assessment phase

When you finish a topic—or all 20—tell me its number or name. I will then give you **10 questions for that topic**, each with **one follow-up question**, plus **10 flashcards**. Answer first without checking the files; then we can review your answers together.

Suggested request: “Test me on Topic 8: client-side routing.”

**Starting question:** Which topic do you want to be tested on first?

**Follow-up:** Would you prefer multiple-choice, short-answer, or a mix?

---

## Page 30 — Glossary and final challenge

**API:** a contract for programs to communicate. **Component:** reusable React UI. **Controller:** backend function handling a route. **Middleware:** function that runs during a request. **Schema:** data shape and rules. **JWT:** signed identity token. **Cookie:** browser-stored request data. **Authentication:** proving identity. **Authorization:** checking permission. **Pagination:** fetching results in pieces.

Final challenge: explain what happens when a signed-in user searches for rent listings, opens one, and tries to edit a listing owned by somebody else.

**Starting question:** Can you trace that story through the frontend route, API request, middleware, controller, and database?

**Follow-up:** At which point should the edit be refused, and what response should the user receive?
