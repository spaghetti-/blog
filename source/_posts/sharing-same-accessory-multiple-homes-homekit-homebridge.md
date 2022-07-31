---
title: Sharing the same accessory (device) across multiple Homekit homes
date: 2022-07-31 12:30:04
tags:
  - homekit
  - homebridge
  - home-automation
---

Apple does not let you share individual devices in your Homekit home with
multiple people. This is kind of a problem for me because I want to be able to
share common accessories (garage door opener, the shared door) with others but
not everything else (thermostats, cameras, lights, etc.).

You can do this if you are using Homebridge however. First change your devices
to use
[child-bridges](https://github.com/homebridge/homebridge/wiki/Child-Bridges)
instead of a main bridge. Then go to the json configuration option (`Config`
button on the main UI, or wherever you've stored it) and duplicate your device.
For example, in my case:

```diff
 "platforms": [
     {
         "email": "",
         "password": "",
         "name": "MyQ",
         "_bridge": {
             "username": "xx:xx:xx:xx:xx:xx",
             "port": 11111
         },
         "platform": "myQ"
     },
+    {
+        "email": "",
+        "password": "",
+        "name": "MyQ-Shared",
+        "_bridge": {
+            "username": "xy:xx:xx:xx:xx:xx",
+            "port": 11112
+        },
+        "platform": "myQ"
+    },
     ...
     ]
```

just make sure that the `_bridge.username` and `_bridge.port` settings are
unique.

Then go to Apple Home, add a new home, and add the bridge as you would regularly
(Add Accessory -> Scan the bridge config). You can now invite others to your
shared home without giving them access to everything else.
