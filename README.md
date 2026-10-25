# Playmate

Sports matchmaking and court booking coursework website. This is a standalone export of the existing design. Visitors sign in with a Playmate username and password; the application does not depend on ChatGPT accounts or ChatGPT hosting.

**Start with [START_HERE.md](START_HERE.md)** to upload the files to GitHub and publish the website.

| Account | Username | Password |
| --- | --- | --- |
| Player | `player` | `PlaymatePlayer26!` |
| Venue owner | `owner` | `PlaymateOwner26!` |

These shared demo accounts work after running the included database schema and seed files. New users can also register their own account. The account type is fixed at registration; a player cannot open the venue dashboard and an owner cannot open the player dashboard. Sign out to use another account.

## Hosting

The source lives in GitHub. Cloudflare Workers serves the website and Cloudflare D1 stores accounts and saved dashboard records. The public website uses a `workers.dev` address or a custom domain. GitHub Pages alone cannot run the server code that verifies passwords and permissions.

## Run locally

Install Node.js 24 or newer. In this folder, run:

```sh
npm start
```

Open `http://localhost:3000`. The local server creates its SQLite database and the two demo accounts automatically. This local mode does not need a Cloudflare account. Local data is stored in `data/playmate.sqlite` and is excluded from Git.

## Check the application

```sh
npm test
```

Checks cover registration, password validation, duplicate usernames, session expiry, logout, saved account data and both role restrictions. HTTP checks also verify the two demo logins, protected dashboard pages and private scripts.

## Included flows

- Find, filter, save, join and leave sample games; join a waiting list.
- Create provisional games and preview suggested groups.
- Compare sample courts and make or cancel demonstration bookings.
- View games, bookings, sample messages and reviews.
- Manage a venue calendar, offline demonstration bookings, blocked slots and court prices.
- Register, sign in and sign out with server-checked account access.

## Scope

Accounts, password checks, sessions, role permissions and saved account records are implemented. Courts, game groups, booking transactions and conversations remain coursework demonstrations. No real court is reserved, no payment is collected and messages are not delivered to another person. The hosting export does not turn those demonstration flows into external service integrations.

## Main files

- `src/worker.mjs`: HTTP routes and page access checks.
- `src/accounts.mjs`: account, session and saved-state endpoints.
- `src/auth-page.mjs`: login and registration screens.
- `public/playmate/`: shared styles and login form behavior.
- `public/private/`: protected dashboard template and behavior; the Worker blocks direct access.
- `migrations/0001_accounts.sql`: account database tables.
- `seeds/demo_accounts.sql`: the two hashed demo accounts.
- `wrangler.json`: Cloudflare hosting and database configuration.
- `scripts/local-server.mjs`: local Node.js/SQLite server.

Keep `assets.run_worker_first` set to `true` in `wrangler.json`. That makes the server check login and role access before it serves dashboard files.
