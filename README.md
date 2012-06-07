Trackballl alert on Sense
==============
[Howto] Trackball alert on sense gingerbread roms

Copy /system/lib/hw/lights-mahimahi.so from some AOSP rom (like CyanogenMod).

Get /system/framework/services.jar from the sense rom.

Decompile it using baksmali:

[CODE]baksmali -x services.jar -o classout[/CODE]

Go to folder classout/com/android/server and search for the file named NotificationManagerService.smali.

Edit the file following the commits here:

https://github.com/matheusvilela/tb_alert_sense/commits/master

Explaining what the changes do: Sense framework seems to use a LED called DualLed for notifications and battery status.

Nexus One doesn't have this LED. The ones we have are "mNotificationLight" (trackball) and the battery light (one of
"mBatteryLight").
      
So, we must change the code where it uses "mDualLedLight" to yield notifications to use "mNotificationLight" instead:

https://github.com/matheusvilela/tb_alert_sense/commit/97b9326714e07f7d0a3308c8ac684049a37c80e5

Besides that, as Sense uses the same LED for battery status and notifications, it doesn't flash the notifications light
when the phone is charging (the LED is supposed to be on already). So, we have to make it always verify for notifications:

https://github.com/matheusvilela/tb_alert_sense/commit/f758b4123b2b60ce5c3c92e34e703db3a2ee2f18

The last thing is that Sense doesn't allow apps to create notifications with custom colors: it doesn't even verify
a notification color before flashing it. So, we must change that in order to get the notification properties before
flashing the lights. If the app that created the notification haven't set custom properties, we must use the default ones:

https://github.com/matheusvilela/tb_alert_sense/commit/e8fa1b6cb34ec01fd2494f2f46b216a9f90aa472

After those change, you must compile your changes:

[CODE]smali classout -o classes.dex[/CODE]

You must then use 7zip in order to place the new classes.dex inside the services.jar.

Done! If you did everything right, tb alert should work now! :)

To fix the charging LED, refer to this commit:

https://github.com/matheusvilela/tb_alert_sense/commit/b4d8986de78e19dde099b1e235aaa7d1aa4ec4f9
