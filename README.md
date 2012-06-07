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
"mBatteryLight" or "mAttentionLight", haven't tested this as there are easier ways to fix charging LED).
      
So, we must change the code where it uses "mDualLedLight" to yield notifications to use "mNotificationLight" instead.

Besides that, as Sense uses the same LED for battery status and notifications, it doesn't flash the notifications light
when the phone is charging (the LED is supposed to be on already). So, we have to make it always verify for notifications.

The last thing is that Sense doesn't allow apps to create notifications with custom colors: it doesn't even verify
a notification color before flashing it. So, we must change that in order to get the notification properties before
flashing the lights. If the app that created the notification haven't set custom properties, we must use the default ones.


After those change, you must compile your changes:

[CODE]smali classout -o classes.dex[/CODE]

You must then use 7zip in order to place the new classes.dex inside the services.jar.

Done! If you did everything right, tb alert should work now! :)
