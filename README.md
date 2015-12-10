# Upgrading an old Loop installation

Loop release v2.4.0 has cleaned up the structure of the `loopdk`
profile folder which makes it much easier to maintain new Loop
installations and develop new modules.

However, special care must be taken when upgrading “old”
installations.


## Fixing the Drupal code registry

When upgrading Loop,
[Drupal 7's code registry](https://www.drupal.org/node/350780) must be
updated to match the new module layout in the updated installation
profile.

You may be able to rebuild the registry using the Drush module
[Registry Rebuild](https://www.drupal.org/project/registry_rebuild),
but the easiest way to fix the registry is to load it from a clean
Loop (v2.4.0) installation. A dump of the registry is available as
[loopdk.registry.sql](https://raw.github.com/loopdk/upgrading-loop/master/loopdk.registry.sql).

To load this dump, jump into your Loop site directory (where
settings.php sits) and run this command:

```
curl --silent https://raw.github.com/loopdk/upgrading-loop/master/loopdk.registry.sql | drush sql-cli
```

## Upgrading the site

After sorting out the registry, you should be able to run

```
drush updatedb
```

to finish upgrading the Loop installation.


## Installing additional Loop modules

Finally, you should install some additional Loop modules to make the installation match the updated installation profile:

```
drush pm-enable loop_content loop_dashboard loop_friend_notification loop_instruction loop_profession_optional
```

## Cleaning up the menus

Go to /admin/structure/menu/manage/menu-loop-primary-menu and remove any manually added links to “admin/content/abuse”.
