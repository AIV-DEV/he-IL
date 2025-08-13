---
layout: post
title: Reversing&Patching לאפליקציית הIR בQin F22Pro
---

במכשיר Qin F22Pro יש אפליקציית שלט IR (למשל, למזגנים או טלוויזיות חכמות) מובנית מראש. הבעיה היא, שאם עושים root ומוחקים חלק מהBloatware שהיצרן הכניס לקושחה – אפליקציית הIR לא עובדת יותר, בכניסה אליה מוצגת שגיאה "System environment error!" ומיד האפליקציה נסגרת.
אז החלטתי לפצח את ההגנה הזו ולערוך את האפליקציה ככה שתעבוד בכל מקרה.

אני אשתמש לשם כך באפליקציית MT Manager לעריכה והנדסה לאחור של אפליקציות, יש לי משתמש פרימיום באפליקציה שעולה כ20$ לכל החיים – שווה כל דולר, בעיני.

הטכניקה שחשבתי עליה היא כזו:

·        מציאת הסטרינג הרלוונטי של הודעת השגיאה בקובץ הAPK.

·        מציאת קוד הSmali הרלוונטי שגורם להצגת השגיאה.

·        פענוח Smali2Java כדי שאבין מה קורה בדיוק בקוד. בכל זאת, קוד Smali הוא לא כל כך קריא, לעומת קוד Java פשוט.

·        עריכת הSmali לביטול קריסת האפליקציה.

כדי לערוך את הSmali אחרי שאבין מה צריך לעשות בJava, חשבתי להשתמש בAI. זה אומנם לא ממש חובה, אך נוח בהרבה במקום לפענח את קוד הSmali הארוך, המורכב והמסובך.

שלב ראשון, פתחתי את קובץ הAPK, נכנסתי לקובץ הResources.arsc וחיפשתי את הסטרינג. מצאתי אותו, אז העתקתי את הid שלו, שבמקרה שלנו הוא 7f0a004a.

חיפשתי את הid הזה בקובץ הClasses.dex ומצאתי אותו בcom/douqin/remote/activity/RemoteActivity.

אז העתקתי את כל קוד הSmali של הקובץ הזה למחשב, למען הנוחות, ואז עשיתי לו גם Smali2Java (דרך MT Manager, אופציה הזמינה רק למשתמשי פרימיום. אם אינכם משתמשי פרימיום, אפשר לעשות את זה ידנית עם כלים כדוגמת JADX). והעתקתי את קובץ הJava.   הקבצים זמינים בRepo שפתחתי בGithub.

חיפשתי את הid הרלוונטי בקובץ הjava ומצאתי את המחלקה הבאה:

```
    private void h() {

        Toast.makeText((Context) this, 0x7f0a004a, 1).show();

        Log.e("RemoteActivity", getString(0x7f0a004a));

        finish();

    }
```

מה קורה כאן בעצם?

1.     מוצגת הודעה מסוג Toast למשתמש עם הסטרינג הזה.

2.     נשלחת שגיאה בLogcat עם הסטרינג הזה.

3.     "האקדח המעשן" – פקודת Finish שמובילה לסגירת האפליקציה.

שימו לב לשם המחלקה – h – שלא מלמד שום דבר על תוכן ותפקיד המחלקה. זה בדרך כלל אופייני לקוד שעבר ערבול (obfuscation) שמיועד להקשות על פענוחו. כנראה שלDouqin, יצרנית המכשיר, היה משום מה מאוד חשוב לשמור על האפליקציה הזו שתעבוד רק כשאפליקציות אחרות קיימות במכשיר, למרות שהיא חינמית לחלוטין.

עוד נקודה שחשוב לשים אליה לב היא, שבמחלקה כאן לא מתבצעת הבדיקה עצמה. רק מתבצעות פקודות מסויימות לאחר שהבדיקה כבר התבצעה. אז החלטתי לבטל את פקודת הFinish, ושאלתי את ChatGPT איך לעשות את זה בSmali (קוד הJava שמתקבל בתהליך הSmalii2Java לא ניתן לקימפול חוזר). הוא אמר למחוק את השורה הזו בתוך המחלקה h.

```
.line 347

invoke-virtual {p0}, Lcom/duoqin/remote/activity/RemoteActivity;->finish()V
```

אז מחקתי את השורה, שמרתי את השינויים והכנסתי את הקובץ הערוך למכשיר.

פה חיכתה לי הפתעה מתסכלת – האפליקציה אומנם לא נסגרה – אך הגיעה למסך לבן ולא עבדה. זה אומר, שבעצם המחלקה h היא מה שקורה אחרי שהאפליקציה החליטה לא לעבוד,  אך היא לא זו שמשביתה את האפליקציה.

הייתי יכול לנתח את הקוד יותר לעומק אך שלחתי אותו לChatGPT. הוא הסביר, שהבדיקה, כמו שזיהינו, אכן לא מתבצעת במחלקה h אלא במשתנה בוליאני בשם i. (שוב, לא שם מובן. אלא לפי סדר הabc, כמו חלק גדול מהמחלקות והמשתנים בקוד הזה).

ומיד אחרי הOnCreate, מתבצעת בדיקה מה הערך של i. אם הוא False, כל הLayouts לא נטענים בכלל.

```
        if (i()) {

            if (RemoteApp.b()) {

                setContentView(0x7f070001);

            } else {

                setContentView(0x7f070000);

            }
```

(אין אחרי זה כלל else שמסביר מה לעשות אם i הוא False.)

אפשר היה כמובן לבטל את הבדיקה הזו, אבל אני העדפתי שהבדיקה פשוט תמיד תחזיר True. אז נסתכל על קוד הJava של המשתנה i.

```
    private boolean i() {

        int i;

        String a = aa.a(this, "ro.product.manufacturer");

        if (TextUtils.isEmpty(a) || !"DuoQin".equalsIgnoreCase(a)) {

            Log.i("RemoteActivity", "check error 1");

            h();

            return false;

        }

        String a2 = aa.a(this, "ro.duoqin.ota.tag");

        if (TextUtils.isEmpty(a2)) {

            Log.i("RemoteActivity", "check error 11");

            h();

            return false;

        }

        if (a2.toLowerCase().contains("f22pro_oversea")) {

            try {

                i = Settings.Secure.getInt(getContentResolver(), "DUOQIN_PRODUCT");

            } catch (Settings.SettingNotFoundException e) {

                e.printStackTrace();

                i = 0;

            }

            if (i == 1) {

                return true;

            }

            Log.i("RemoteActivity", "check error 22");

            h();

            return false;

        }

        if (TextUtils.isEmpty(a2) || (!a2.toLowerCase().contains("f22") && !a2.toLowerCase().contains("qin3"))) {

            Log.i("RemoteActivity", "check error 2");

            h();

            return false;

        }

        PackageManager packageManager = getPackageManager();

        new Intent("android.intent.action.MAIN", (Uri) null).addCategory("android.intent.category.HOME");

        Intent intent = new Intent("android.intent.action.MAIN", (Uri) null);

        intent.addCategory("android.intent.category.LAUNCHER");

        List<ResolveInfo> queryIntentActivities = packageManager.queryIntentActivities(intent, 512);

        Intent intent2 = new Intent("com.duoqin.payservice.pay");

        intent2.addCategory("android.intent.category.DEFAULT");

        List<ResolveInfo> queryIntentActivities2 = packageManager.queryIntentActivities(intent2, 512);

        String packageName = getPackageName();

        String substring = packageName.substring(0, packageName.lastIndexOf(".") + 1);

        ArrayList arrayList = new ArrayList();

        for (ResolveInfo resolveInfo : queryIntentActivities2) {

            if (resolveInfo.activityInfo.name != null) {

                arrayList.add(resolveInfo.activityInfo.name);

            }

        }

        if (arrayList.isEmpty()) {

            Log.i("RemoteActivity", "check error 3");

            h();

            return false;

        }

        ArrayList arrayList2 = new ArrayList();

        arrayList2.add(substring + "payservice.activity.PaymentActivity");

        for (int i2 = 0; i2 < arrayList2.size(); i2++) {

            if (!arrayList.contains(arrayList2.get(i2))) {

                Log.i("RemoteActivity", "check error 4");

                h();

                return false;

            }

        }

        ArrayList arrayList3 = new ArrayList();

        for (ResolveInfo resolveInfo2 : queryIntentActivities) {

            if (resolveInfo2.activityInfo.name != null) {

                arrayList3.add(resolveInfo2.activityInfo.name);

            }

        }

        if (arrayList3.isEmpty()) {

            Log.i("RemoteActivity", "check error 5");

            h();

            return false;

        }

        ArrayList arrayList4 = new ArrayList();

        arrayList4.add(substring + "qweather.activity.WeatherActivity");

        arrayList4.add(substring + "translator.ui.TranslatorMainActivity");

        arrayList4.add(substring + "syncassistant.ui.MainActivity");

        arrayList4.add("com.marsqin.activity.MarsQinSplashActivity");

        arrayList4.add(substring + "guardapp.activity.ActivityQinGuard");

        for (int i3 = 0; i3 < arrayList4.size(); i3++) {

            if (!arrayList3.contains(arrayList4.get(i3))) {

                Log.i("RemoteActivity", "check error 6");

                h();

                return false;

            }

        }

        return true;

    }
```

וואוו. יש פה המון בדיקות!

לא ניתחתי את כל הקטע הזה לעומק. בקצרה, האפליקציה בודקת כמה ערכים בbuild.prop וכמו כן בודקת אם כמה אפליקציות מותקנות, ולפי זה מחליטה אם להחזיר true או false. כעת יש לנו כמה דרכים להתמודד:

1.     הכנסת אפליקציות ריקות עם שמות החבילה הספציפיים למכשיר (בBuild.prop גם ככה לא התעסקתי). פיתרון מייגע ולא יעיל.

2.     באופן ידני, לעבור בדיקה בדיקה ולוודא שתמיד יחזיר True.

3.     מה שעשיתי בסוף – פשוט למחוק את כל הבדיקות ולדאוג שתמיד תמיד הערך יהיה True.

קוד הSmali הרלוונטי ארוך מאוד, אז רק אציין כאן מה בדיוק הייתי צריך לעשות לפי ChatGPT.

הייתי צריך למחוק את כל הקוד שבין התחלת המשתנה - .method private i()Z עד לסופו - .end method, ובמקומו להכניס את הקוד הבא שיחזיר תמיד True.

```
.registers 2

    const/4 v0, 0x1

    return v0
```

סגרתי את הקובץ, שמרתי את השינויים, ו... זה עבד!

שני קבצי הAPK, וקבצי הקוד שהעתקתי, זמינים בRepo הרלוונטי בגיטהאב שלי.

[https://github.com/AshiVered/DouqinRemote_Hack](https://github.com/AshiVered/DouqinRemote_Hack)

הערה קטנה לסיום:

אנחנו רואים כאן אפליקציה מוגנת לא רע בכלל. עם קצת נחישות עקפתי את ההגנות, כן, אבל א"א להתעלם מההגנה שהיצרן ניסה לנקוט כאן, שפשוט הפכה ללא רלוונטית בעידן הAI - שיטת ערבול הקוד. עלי, כבן אדם, מקשה מאוד העובדה שלמשתנים ומחלקות אין שמות מסודרים ומבונים. לAI זה לא משנה דבר... הוא קורא ברגע את כל הקוד ומבין מה כל מחלקה עושה בלי קשר לשם.

האם עוד הגנה, שפעם נחשבה די טובה (אם כי גם היא, לא הייתה בלתי ניתנת לפריצה על ידי אדם נחוש) נפלה קורבן לAI?
