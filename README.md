# tonari's Notion-backed blog

This is tonari's blog, a static site generated with next.js and using Notion as the source of articles.

See [**tonari-notion-blog-demo.web.app**](https://tonari-notion-blog-demo.web.app/) for a simple demonstration of what it looks like :).

## How it works

Articles are written in the form of pages in a Notion database, and this codebase then fetches them via their public API and coverts those pages into static content that is deployed to a Firebase static site.

This repository is an unbranded version of our blog, presented as-is for others to modify as they please. There's a lot of hardcoded stuff in here (like the Japanese-English language support and GoatCounter metrics) that you'll need to modify to your own needs. We hope it serves as a good starting-point for others, though, and would also be welcome to the idea of others turning it into a more generic configurable blog system.

# Getting started

## 1. Duplicate the sample database

- Go to [the sample database Notion page](https://jakebot.notion.site/jakebot/Blog-Example-92b8d335abda424eb5baf96e4a5208a4) and click "Duplicate" on the top right, copying it into your workspace.
- In your duplicated page, get your new database's ID by hovering over the database title ("Posts"), clicking on "..." -> "Open as page".

  The new URL will look like `https://www.notion.so/yourname/[DATABASE_ID]?v=[VIEW_ID]`.

  Copy _just_ the `DATABASE_ID` and place it in the `databaseId` field in `blog.config.js`.

## 2. Create a new API "integration"

- Go to [My Integrations](https://www.notion.so/my-integrations) on Notion and make a new internal integration.
- Copy `.env.example` to `.env` and fill in your integration key:
  ```bash
  NOTION_API_KEY=[secret_YOURAPIKEY]
  ```

## 3. Share the database with your integration

Your database needs to be "shared" with your integration before the notion API can access it.

- Hover over your database title ("Posts"), click on "..." -> "Open as page".
- Click "Share" on the upper right, click the text box that says "People, emails, groups, or integrations", then find your integration and select it.

## 4. Setup dev environment

Install [yarn](https://yarnpkg.com/) if you don't have it already, then within the root of this repository, run:

```
yarn
```

to grab all dependencies.

### Running Locally

```
yarn dev
```

And then visit http://localhost:3000/

### Building for production

To export a static site to the `out/` directory:

```
yarn export:prod
```

We included some sample scripts in `package.json` and a `firebase.json` for deploying to Firebase, which is what we use for tonari's blog, but you're obviously free to deploy that static site anywhere you want.

## Firebase and Slack deployment helpers

We deploy our blog to staging and production using a Slack slash command that triggers a GitHub Action which builds & deploys the site to Firebase Hosting. It was the simplest way to allow non-technical peeps to update the blog and not need a server running.

Included in this blog is our (totally optional) GitHub action and Firebase function you can modify and deploy to use this functionality yourself if you'd like.

### Firebase

To deploy the functions, set the applicable secrets:

```bash
firebase functions:config:set \
    deploy.region=asia-northeast1 \
    github.username=mygithub \
    github.repository=mygithub/blog \
    github.access_token=ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx \
    slack.signing_secret=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

yarn deploy:functions
```

- `firebase.region` is the [Cloud Function location](https://firebase.google.com/docs/functions/locations) to deploy into.
- `github.username` is the username of the account that owns the Personal Access Token `github.access_token` - we use a special bot account with restricted access to certain repositories, and recommend you do the same.
- `slack.signing_secret` is the signing secret of the app you create in your Slack workspace for this bot.

See this blog for more details on setting it up (thanks to whoever wrote this, by the way): https://whatdafox.com/trigger-github-workflow-from-slack-using-firebase-functions/.

### GitHub Actions

For the GitHub actions to successfully build and deploy your Firebase static site (and send slack notifications), you'll need to set the following secrets on your repo:

- `FIREBASE_TOKEN` - generated by running `firebase login:ci`
- `NOTION_API_KEY` - the API key for the integration that has access to your blog database in Notion.
- `SLACK_BOT_TOKEN` - the auth token for your Slack app that you created in your workspace.

## Credits

This was originally a fork of https://github.com/ijjk/notion-blog, but has since been largely re-written.
