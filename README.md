# Demonstration of the Heroku Pipeline
https://devcenter.heroku.com/articles/pipelines


1. add an `app.json` file

• Be sure to specify a `postdeploy` hook for loading your schema in the release environment
• Do not use db:create in your `postdeploy` hook
• Do not use db:setup in your `postdeploy` hook

Instead, use `db:schema:load` or `db:structure:load`

see https://help.heroku.com/63D7ALXT/why-am-i-seeing-user-does-not-have-connect-privilege-error-with-heroku-postgres-on-review-apps

Here's a basic setup that provisions the hobby-dev plan for Postgres onto your review apps but provisions the standard-0 add-on onto the staging app.


```
{
  "scripts":  {
    "postdeploy": "bundle exec db:schema:load"
  },
  "addons": [
    "heroku-postgresql:standard-0"
  ],
  "environments": {
    "review": {
      "addons": [
        "heroku-postgresql:hobby-dev"
      ]
    }
  }
}
```


2. `Procfile`

A typical Heroku Profile with a release phase might look like:
```
web: bundle exec rails server -p $PORT
release: ./release-tasks.sh

```



3. `release-tasks.sh` file

The executable permission is checked-in to the Git repo **along with the file**,

```
chmod +x release-tasks.sh
git add release-tasks.sh
git commit -m "Make release-tasks.sh executable"
```

This file contains:

```
bundle exec rails db:migrate
```

If you are using data migrations, you can use



```
bundle exec rails db:migrate && bundle exec rails data:migrate
```

In this file you should:
• Running database schema migrations
• Data migrations
• Sending CSS, JS, and other assets from your app’s slug to a CDN or S3 bucket
• Priming or invalidating cache stores


4. `.github/workflows/rake_spec.yml` file(s)


