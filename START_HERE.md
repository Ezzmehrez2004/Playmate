# Publish Playmate from GitHub

This package keeps the existing website and its player/venue-owner login. Visitors do not need a ChatGPT account.

GitHub stores the code. Cloudflare hosts the running application and its account database. Uploading this project to GitHub Pages alone will not run its login backend.

## 1. Upload the source to GitHub

1. Extract `Playmate_GitHub.zip` on your computer.
2. Create a new repository on GitHub, for example `playmate`.
3. Select **Add file > Upload files**.
4. Upload the extracted files and folders, then commit them.

At the top level of the repository, you should see `package.json`, `wrangler.json`, `src`, `public`, `migrations` and `seeds`. Upload the contents of the extracted folder rather than the ZIP itself. Do not upload `node_modules`, `.wrangler` or a local `data` folder.

## 2. Create the account database

1. Sign in to your own Cloudflare account and open **Storage & databases > D1**.
2. Create a database named `playmate-db`.
3. Copy its **Database ID**.
4. In your GitHub repository, edit `wrangler.json` and replace `REPLACE_WITH_YOUR_D1_DATABASE_ID` with that ID. Commit the change.
5. Open the new database’s **Console** in Cloudflare.
6. Paste and run the contents of `migrations/0001_accounts.sql`.
7. Paste and run the contents of `seeds/demo_accounts.sql`.

The first SQL file creates the tables. The second creates the two demo accounts with salted password hashes. Running these setup files again preserves existing accounts and saved records.

## 3. Connect GitHub and publish

1. In Cloudflare, open **Workers & Pages > Create application**.
2. Choose the option to import a **GitHub** repository and select `playmate`.
3. Set the Worker/application name to `playmate-web`, matching `name` in `wrangler.json`.
4. Leave the build command empty; this website has no front-end build step.
5. Set the deploy command to `npm run deploy`.
6. Set the root directory to the repository root and the production branch to `main`.
7. Set the build environment variable `NODE_VERSION` to `24` if the build is using an older Node.js version.
8. Select **Save and Deploy**.

Open the public `workers.dev` link Cloudflare returns. Use that link for visitors and your assignment. The link should open the Playmate login screen directly. A custom domain is optional.

Only you need GitHub and Cloudflare accounts to publish. Website visitors use Playmate accounts.

## 4. Test both roles

| Role | Username | Password | Opens |
| --- | --- | --- | --- |
| Player | `player` | `PlaymatePlayer26!` | Player dashboard |
| Venue owner | `owner` | `PlaymateOwner26!` | Venue dashboard |

Sign out between tests. With the player account, opening `/venue` returns you to `/player`. With the owner account, opening `/player` returns you to `/venue`. The server also rejects requests to the other role’s saved-data endpoints.

## Command-line alternative

If you prefer using a terminal, install Node.js 24 and run these commands in the extracted folder:

```sh
npm ci
npx wrangler login
npx wrangler d1 create playmate-db
```

Copy the returned database ID into `wrangler.json`, then run:

```sh
npm run db:migrate
npm run db:seed
npm run deploy
```

The deploy command prints the live website address. You can connect the GitHub repository to this Worker afterward for automatic updates.

## If something does not work

- **The deploy command asks for a database ID:** replace the placeholder in `wrangler.json` with your own D1 ID.
- **Login reports a database error:** check that both SQL setup files were run against that same D1 database.
- **Demo login is rejected:** run `seeds/demo_accounts.sql` in the database Console, then retry with the credentials above.
- **The wrong website appears:** open the Cloudflare deployment URL. The previous ChatGPT-hosted URL is a separate deployment.
- **The Worker name does not match:** use `playmate-web` in both Cloudflare and `wrangler.json`, or change both names together.

## Official references

- GitHub Pages: https://docs.github.com/en/pages/getting-started-with-github-pages/what-is-github-pages
- Cloudflare Git deployment: https://developers.cloudflare.com/workers/ci-cd/builds/
- Static assets and server access checks: https://developers.cloudflare.com/workers/static-assets/binding/
- D1 database setup: https://developers.cloudflare.com/d1/get-started/
