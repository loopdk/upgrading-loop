# Upgrading an old Loop installation

Loop release v1.2.0 has cleaned up the structure of the `loopdk`
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
[loopdk.registry.sql](https://raw.github.com/os2loop/upgrading-loop/master/loopdk.registry.sql).

To load this dump, jump into your Loop site directory (where
settings.php sits) and run this command:

```
curl --location --silent https://raw.github.com/os2loop/upgrading-loop/master/loopdk.registry.sql | drush sql-cli
```

## Upgrading the site

After sorting out the registry, you should be able to run

```
drush updatedb
```

to finish upgrading the Loop installation.

## Apply patches

### Style Settings module

```
cd $(drush php-eval 'echo DRUPAL_ROOT')/$(drush pm-info --fields=path --format=csv style_settings)
```

[Value replacement clobbers CSS pseudo-class selectors in some situations](https://www.drupal.org/node/2648698)

```
curl --silent https://www.drupal.org/files/issues/style_settings-fix-clobbering-of-pseudo-classes-2648698-1-7.patch | patch --strip=1
```

### SAML Service Provider module

```
cd $(drush php-eval 'echo DRUPAL_ROOT')/$(drush pm-info --fields=path --format=csv saml_sp)
```

[Provide a way of knowing if the user has logged in via the SAML SP module](https://www.drupal.org/node/2649458)

```
curl --silent https://www.drupal.org/files/issues/2649458-saml_sp-is_user_authenticated_via_saml-2.patch | patch --strip=1
```

[EntityMetadataWrapperException: Unknown data property field_nameid](https://www.drupal.org/node/2572715)

```
curl --silent https://www.drupal.org/files/issues/nameid-correction.patch | patch --strip=1
```

[Validation of signed elements fails when namespaces are used](https://www.drupal.org/node/2649478)

```
curl --silent https://www.drupal.org/files/issues/2649478-saml_sp-validation_of_signed_elements_fails-2.patch | patch --strip=1
```

## Installing additional Loop modules

Finally, you should install some additional Loop modules to make the installation match the updated installation profile:

```
drush pm-enable loop_content loop_dashboard loop_friend_notification loop_instruction loop_profession_optional
```

Check out the notes on the [Loop saml](https://github.com/os2loop/profile/blob/release-v2.0.0/modules/loop_saml/README.md) module if it's installed.

## Cleaning up the menus

Go to /admin/structure/menu/manage/menu-loop-primary-menu and remove any manually added links to “admin/content/abuse”.
