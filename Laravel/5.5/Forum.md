```https://laracasts.com/series/lets-build-a-forum-with-laravel/episodes/1```

* `ar` => `php artisan`
* `dc` = `docker-compose`

# Kata: Building a Forum

## Setup docker Laravel dev environment [cmd]
  - `cd /Volumes/casesensitive/learning`
  - `mkdir throw`
  - `cd throw`
  - `cp -R /Volumes/casesensitive/other/environments/ProjectTemplates/Laravel/5.5/ .`
  - `vim docker/vhost.conf`
    - `down` * 3
    - `left` * 23
    - `i`
    - `delete` * 5
    - `forum`
    - `ESC :wq`
  - `dc up --build -d`

## Create new laravel project 'forum' [app_c]
  - `dc exec app bash`
  - `laravel new forum`
  - `cd forum`
  - `yarn install`

## Create new Model `Thread` as a resource controller [app_c]
  - `cd forum`
  - `ar make:model Thread -mr`

## Add fields to Thread table migration `user_id, title, body` [PhpStorm]
  - `CMD+ALT+O up` - select Thread file
  - `down` * 3
  - `CMD+D`
  - `ALT+right` * 2
  - `i TAB`
  - `ALT+right user_`
  - `CMD+D`
  - `ALT+right` * 2
  - `s TAB`
  - `ALT+right SHIFT+ALT+right title`
  - `CMD+D`
  - `ALT+right` * 2
  - `s TAB`
  - `ALT+right SHIFT+ALT+right body`

## Create MySql database in database container [app_c]
  - `mysql -hdatabase -uroot -proot -e 'create schema forum'`

## Update app environment with dbname:forum, user:root, and password:root [PhpStorm]
  - `CMD+SHIFT+O .env`
  - `CMD+F data ESC`
  - `CMD+right`
  - `ALT+up forum`
  - `down ALT+up root`
  - `down ALT+up root`

## Migrate database [app_c]
  - `ar migrate`

## Create new model `Reply` as a resource controller [app_c]
  - `ar make:model Reply -mr`

## Add `thread_id, user_id, body` to migration [PhpStorm]
  - see above making Thread migration
  - thread_id `CMD+D CMD+F user ESC thread`

## Create new db table [app_c]
  - `ar migrate`

## Create Thread factory, associating each Thread to a new user [PhpStorm]
  - `CMD+SHIFT+O userf`
  - `CMF+F return ESC`
  - `ALT+up` * 5
  - `CMD+C`
  - `CMD+end RETURN`
  - `CMD+V ; RETURN`
  - `CMD+F user RETURN`
  - `ESC T Return`
  - `down` * 4
  - `ALT+up` * 4
  - `[ RETURN`

'user_id' => function () {
  return factory('App\User')->create()->id;
},
'title' => $faker->sentence,
'body' => $faker->paragraph

## Create 50 new threads in the db [tinker]
  - `factory('App\Thread', 50)->create()`

## Create Reply factory, associating reply with a new user and a new thread [PhpStorm]
  - `LEFT`
  - `ALT+up` * 2
  - `CMD+D left RETURN`
  - `CMD+F \ ESC ESC R TAB RETURN`
  - `CMD+F [ ESC ALT+down [ RETURN`

  return [
      'thread_id' => function() {
          return factory('App\Thread')->create()->id;
      },
      'user_id' => function() {
          return factory('App\User')->create()->id;
      },
      'body' => $faker->paragraph()
  ];

## Iterate over the threads created previously and create 10 new replies for each thread [tinker]
  - `App\Thread::all()->each(function($thread) { factory('App\Reply', 10)->create(['thread_id' => $thread->id]); })`
