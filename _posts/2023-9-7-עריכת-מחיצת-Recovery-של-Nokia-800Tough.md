---
layout: post
title: עריכת מחיצת Recovery של Nokia 800Tough
---

כל מה שרציתי לעשות זה לפרוץ recovery.img, ויש אפילו מדריך לזה. אבל הכל הסתבך, כרגיל בדברים האלו.

ככה זה כשמתעסקים בקושחה בצורה לא רשמית 😉.

![SkyOSlogo](https://raw.githubusercontent.com/AshiVered/SkyOS/main/res/logo.png)

[SkyOS](https://github.com/AshiVered/SkyOS) הוא ה-ROM המותאם אישית שלי עבור Nokia 800Tough. לפני כמה חודשים נטשתי אותו ו[הכרזתי עליו כמוקפא](https://github.com/AshiVered/SkyOS/issues/6).

לפני כמה ימים התחלתי לפתח אותו שוב. אבל, לגבי הפרויקט הראשי - ה-ROM
 (system.img) יש לי כמה רעיונות, ועדיין לא החלטתי לאיזה [כיוון לקחת את הפרוייקט.](https://github.com/AshiVered/SkyOS/discussions/7)

אז החלטתי בינתיים לנסות לערוך את מחיצת הrecovery של המכשיר, כדי שיהיה לנו Custom recovery למכשיר הזה.

המטרה של הפרוייקט היא להיות דומה ל[GerdaOS recovery](https://gitlab.com/project-pris/recovery): גישת ADB, ותמיכה בצריבת קבצי zip לא חתומים.

חשבתי שזה יהיה קל: יש  [מדריך באתר BananaHackers](https://wiki.bananahackers.net/root/recovery-mode) ליצור Custom recovery עבור KaiOS וAndroid.
אבל אחרי שביצעתי את כל השלבים במדריך…

המכשיר נכנס לbootloop.


אז צרבתי מחדש את מחיצת הrecovery המקורית, וניסיתי לחשוב. זכרתי שבעבר גם עבור מכשירים אחרים (למשל Xiaomi Qin1s+) המדריך הביא לתוצאה דומה. וגם אם לא עורכים את המחיצה, רק פוחתים אותה עם, אלא רק פותחים וסוגרים עם abootimg, זו התוצאה. למה? אני לא יודע.

אז ניסיתי לעבוד עם Android Image Kitchen (AIK), שהוזכר ב[מדריך הזה](https://github.com/minhduc-bui1/nokia-leo/wiki/6.-ROOT:-Boot-partition-modifying).
לא פעלתי לפי המדריך הזה. עבדתי לפי מדריך BananaHackers, אבל פירקתי וארזתי מחדש את ה- recovery.img עם AIK, ולא עם abootimg.

יופי.

הטלפון לא נכנס לbootloop. נהדר!

אבל יש חדשות רעות:

הrecovery לא נפרץ אין גישת adb ואי אפשר לצרוב קבצי ZIP לא חתומים. למה???

אז החלטתי לעקוב אחר המדריך הזה, שמיועד ל-boot.img ולא ל-recovery.img, אבל מסביר מה צריך לערוך ב-default.prop כדי לפתוח גישה ל-adb.

נהדר! זה עובד! 

אבל רק חלקית 😢.

כלומר, לפקודות ADB קלאסיות (pull, push, וכדומה), זה עובד, אפילו עם הרשאות root.

אבל הshell לא עובד:

![_config.yml]({{ site.baseurl }}/assets/images/shell_eror.png)

מה זה?! אני מפתח ROM מותאמים אישית כבר כמה שנים, ומעולם לא שמעתי על קובץ *adbdsh*.

מה לא ניסיתי? השוויתי את תיקיית *ramdisk/sbin* בrecovery שלי עם GerdaOS recovery,  וראיתי ש-BusyBox נוסף שם. אז הוספתי את זה לשלי. לא עזר. 

אותה שגיאה.

אולי ה-*adbd* שסופק ב-BananaHackers פגום? אז העתקתי את ה-adbd מGerdaOS recovery.

אותה שגיאה.

אולי זה קובץ שלא קיים במחיצת recovery, אבל נמצא ב*system/bin*? לא.

אבל העתקתי את *system/bin/sh* תחת השם adbdsh למחיצת recovery, ונראה שיש התקדמות.

הפעם הקובץ החסר הוא *sh*, לא *adbdsh*. אז העתקתי שוב את *sh*, והפעם שמרתי על השם שלו, אז היו לי גם *adbdsh* וגם *sh*(וגם *adbd*, כמובן) ב-*/ramdisk*. לא עזר, עדיין חסר *sh*. מוזר... אז ויתרתי על *adbdsh*- וכמובן חזרנו לשגיאה הראשונה...

לא היו לי עוד רעיונות.

וזה עוד לפני שטיפלנו בבעיה של קבצי ZIP לא חתומים...

נראה שיש עוד הרבה עבודה לעשות...הערה: תודה ל-[@Men770](https://github.com/men770) על כמה רעיונות (למרות שבסופו של דבר זה לא עזר...) ועל שהסביר לי איך לשנות את המילים "KaiOS recovery" למילים "SkyOS recovery" (זה די פשוט; לפתוח את הקובץ */ramdisk/sbin/recovery* עם hex editor , לחפש את המילים "KaiOS recovery" ולהחליף אותן ב"SkyOS recovery").

עכשיו אני חושב לנסות להעביר את Philz Recovery שהוכן עבור Nokia 8110, גם ל - Nokia 800Tough. יש מדריך ב.xda


האם זה יהיה באמת פשוט? נעדכן.

אפשר למצוא את SkyOS recovery [כאן](https://github.com/AshiVered/SkyOS-Recovery/).
