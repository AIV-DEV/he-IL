---
layout: post
title: פריצת אמולטור Google Play games
---
Google Play games for PC הוא אמולטור אנדרואיד למחשב, שפותח על ידי Google.


![_config.yml]({{ site.baseurl }}/assets/images/playgames1.png)

### 1 - התקנה

כמו שאפשר לראות משם האמולטור, האמולטור הזה מיועד רשמית רק למשחקים.

אבל אנחנו רוצים להתקין עליו יותר מזה, נכון?

אז נוריד את גרסת המפתחים.

[https://developer.android.com/games/playgames/emulator](https://developer.android.com/games/playgames/emulator)

אחרי ההתקנה, תפתח כרטיסיה חדשה בדפדפן, לחיבור לחשבון הגוגל שבו יעשה שימוש באמולטור. תתחברו. לאחר מכן, תקפוץ חלונית באמולטור לשימוש בUSB Debuging.

תאשרו.

### 2 - התקנת אפליקציות

דורש טיפ טיפה הבנה בתוכנת ADB, שלה יש מדריך [כאן](https://mitmachim.top/topic/23696/%D7%9E%D7%93%D7%A8%D7%99%D7%9A-%D7%9C%D7%AA%D7%95%D7%9B%D7%A0%D7%AA-adb).
הADB של האמולטור נמצא בנתיב הזה במחשב

`C:\Program Files\Google\Play Games Developer Emulator\current\emulator`

(יתכן שגם ADB רגיל יעבוד, לא בדקתי.)
וכדי להתקין, נריץ את הפקודה

`adb install /path/to/your.apk`

### 3 - חנות אפליקציות

מכיוון שהאמולטור מיועד למשחקים, ב-Google Play המובנה יש רק משחקים. אז נתקין (באמצעות ADB) את חנות האפליקציות [Aurora Store](https://auroraoss.com/). בעיה ראשונה - רק משחקים עדיין מוצגים. כדי לפתור את זה, נכנס בArura store לSpoof manager ונבחר מכשיר אחר

![_config.yml]({{ site.baseurl }}/assets/images/playgames2.png)

ועכשיו נלך ללשונית Accounts, נתנתק ונתחבר בחזרה

![_config.yml]({{ site.baseurl }}/assets/images/playgames3.png)

אבל עדיין נקבל שגיאה בהתקנת אפליקציות

![_config.yml]({{ site.baseurl }}/assets/images/playgames4.png)

זה אומר שמתקין האפליקציות הרגיל נעול להתקנות לא רשמיות, ונצטרך להשתמש בshell (שורת הפקודה) של המכשיר (כלומר האפליקציה תצטרך. לא אנחנו). אך הרצת פקודת התקנה בshell דורשת רוט, ואין לנו רוט.

לכן נשתמש באפליקציית [Shizuku](https://shizuku.rikka.app/), המשמשת להסלמת הרשאות חלקית ללא רוט. תוכלו לקרוא הסבר ברשת.
נתקין את האפליקציה ונריץ את הפקודה הבאה בADB

`adb shell sh /sdcard/Android/data/moe.shizuku.privileged.api/start.sh`

אנחנו צריכים לקבל פלט כזה

![_config.yml]({{ site.baseurl }}/assets/images/playgames5.png)

נכנס לאפליקציית Shizuku ונראה שהכל עובד

![_config.yml]({{ site.baseurl }}/assets/images/playgames6.png)

ועכשיו חנות Aurora store תעבוד לנו חלק!

(הערה קטנה - בכל פעם שנפעיל את האמולטור, נצטרך להריץ את פקודת ההפעלה של Shizuku).

### 4 - Rooting reaserch

לא הצלחתי כרגע לעשות root לאמולטור.

מצורף המידע שיש לי כרגע:

הboot.img נמצא בתוך הקובץ aggregate.img בנתיב

`C:\Program Files\Google\Play Games Developer Emulator\current\emulator\avd`

ניסיתי להשריש את כל הקובץ עם מגיסק 27 וזו השגיאה

![_config.yml]({{ site.baseurl }}/assets/images/playgames7.png)

ניסיתי גם להוציא ממנו את הboot.img ולהשריש רק אותו. הצלחתי, אבל כרגע אין לי רעיון איך לארוז מחדש את ה aggregate.img.

וכמובן, תודה ל[Musicode](https://github.com/musicode1)...

[*הפוסט שלי על זה, בפורום XDA.*](https://xdaforums.com/t/google-play-games-on-pc-hacking-reaserch-theard.4656397/)

