---
layout: post
title: Disable Numlock Permanently through the Linux Kernel
date: 2018-06-05 18:23:00
description:
---

One of the things that annoy me include entering my alpha-numeric password while the numpad is locked. My cheap Dell laptop doesn't feature an LED light for the numlock key. So the only way to know whether it's locked or not is to hit one of the keys and check the consequences.

So I decided to do something about it, and came up with this permanent solution that would keep the numpad unlocked as soon as the kernel is loaded. Grab a copy of the latest Linux Kernel, comment out the code block shown below. Compile, install it and you're good to go.

{% highlight c++ %}
if (!vc_kbd_led(kbd, VC_NUMLOCK)) {

    switch (value) {
    case KVAL(K_PCOMMA):
    case KVAL(K_PDOT):
        k_fn(vc, KVAL(K_REMOVE), 0);
        return;
    case KVAL(K_P0):
        k_fn(vc, KVAL(K_INSERT), 0);
        return;
    case KVAL(K_P1):
        k_fn(vc, KVAL(K_SELECT), 0);
        return;
    case KVAL(K_P2):
        k_cur(vc, KVAL(K_DOWN), 0);
        return;
    case KVAL(K_P3):
        k_fn(vc, KVAL(K_PGDN), 0);
        return;
    case KVAL(K_P4):
        k_cur(vc, KVAL(K_LEFT), 0);
        return;
    case KVAL(K_P6):
        k_cur(vc, KVAL(K_RIGHT), 0);
        return;
    case KVAL(K_P7):
        k_fn(vc, KVAL(K_FIND), 0);
        return;
    case KVAL(K_P8):
        k_cur(vc, KVAL(K_UP), 0);
        return;
    case KVAL(K_P9):
        k_fn(vc, KVAL(K_PGUP), 0);
        return;
    case KVAL(K_P5):
        applkey(vc, 'G', vc_kbd_mode(kbd, VC_APPLIC));
        return;
    }
}
{% endhighlight %}


Or if you want a git patch to apply:

{% highlight c++ %}
--- a/drivers/tty/vt/keyboard.c
+++ b/drivers/tty/vt/keyboard.c
@@ -737,7 +737,7 @@ static void k_pad(struct vc_data *vc, unsigned char value, char up_flag)
                applkey(vc, app_map[value], 1);
                return;
        }
-
+       /*
        if (!vc_kbd_led(kbd, VC_NUMLOCK)) {
 
                switch (value) {
@@ -777,7 +777,7 @@ static void k_pad(struct vc_data *vc, unsigned char value, char up_flag)
                        return;
                }
        }
-
+       */
        put_queue(vc, pad_chars[value]);
        if (value == KVAL(K_PENTER) && vc_kbd_mode(kbd, VC_CRLF))
                put_queue(vc, 10);

{% endhighlight %}

There are other thousand ways to turn your numpad on forever like xorg trick but this one works right at the base (kernel). Plus, it works even before xorg is loaded.
