# uploads:clear
Deletes old chunks from the chunks folder. Uses the config to detect which files can be deleted via the last edit time `clear.timestamp`.

The scheduler is running automatically and can be disabled by a config `clear.schedule.enabled` or the cron time can be changed in `clear.schedule.cron` (don't forget to setup your scheduler in the cron)

## Command usage

````
php artisan uploads:clear
````