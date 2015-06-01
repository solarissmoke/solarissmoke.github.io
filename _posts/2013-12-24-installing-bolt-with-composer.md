---
layout: post
title: Installing Bolt with Composer
---
<p><a href="http://bolt.cm">Bolt</a> is a great little CMS, but I am not fond of the recommended <a href="http://docs.bolt.cm/installation">installation methods</a> because they don’t make it easy to version control your site without having to include the whole Bolt repository. Better, then, to install it as a Composer library. These instructions are based on <a href="http://dev.richardhinkamp.nl/blog/installing-bolt-with-composer">these ones</a>, modified to work with the latest version of Bolt.
<p>Create your project directory with the following structure:
<code><pre>/project
  /config
  /theme
  /web
    /bolt-public
    /files
</pre></code>

<p>At the root of your project create a <code>composer.json</code> file that looks like this:
<code><pre>{
    "repositories": [
        {
            "type": "vcs",
            "url": "https://github.com/bolt/krumo"
        }
    ],
    "require": {
        "bolt/bolt": "dev-master"
    },
    "minimum-stability": "dev",
    "prefer-stable": true,
    "scripts": {
        "post-install-cmd": [
            "Bolt\\Composer\\ScriptHandler::installAssets"
        ],
        "post-update-cmd": [
            "Bolt\\Composer\\ScriptHandler::installAssets"
        ]
    }
}
</pre></code>

<p>Note that Bolt requires the <code>bolt/krumo</code> respository which is not published on Packagist, so you have to specify it as a GitHub repo.

<p>Now install the dependencies with <code>composer install</code>.

<p>Put your bolt configuration files in the <code>config</code> directory. You can start with the templates in <code>vendor/bolt/bolt/app/config</code>. Add a configuration entry to tell Bolt where your theme files are (you should put your template files directly inside this directory):

<code><pre>theme_path: /theme/</pre></code>

<p>Copy the default Bolt <code>.htaccess</code> file (if you’re using Apache &mdash; otherwise create the equivalent configuration file for your server) from <code>vendor/bolt/bolt/.htaccess</code> to your project’s <code>web</code>, and set this directory as the root of your site in your server configuration. You will need to modify the paths referenced in the file to match the directory structure created above (the classes are placed in <code>/web/bolt-public/classes/</code>.

<p>Create an <code>index.php</code> file in the <code>web</code> directory that bootstraps Bolt:
<code><pre>&lt;?php
require_once '../vendor/bolt/bolt/app/bootstrap.php';

/* If you want to modify the Silex application before running it, you can do that here, e.g., */
$app->before(function (Request $request) {
    // ...
});

$app->run();
</pre></code>

<p>Now you should be able to browse to the root of your site and let Bolt handle the rest!