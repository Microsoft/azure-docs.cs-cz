---
title: Jak používat Engagement rozhraní API v systému Android
description: Nejnovější Android SDK – jak používat Engagement rozhraní API v systému Android
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 09b62659-82ae-4a55-8784-fca0b6b22eaf
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: na
ms.topic: article
ms.date: 07/25/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 79d0652be227dd6703a35d31409cf8e0d9c59519
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-use-the-engagement-api-on-android"></a>Jak používat Engagement rozhraní API v systému Android
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

Tento dokument je doplněk k dokumentu [Reporting rozšířené možnosti pro Android Mobile Engagement SDK](mobile-engagement-android-advanced-reporting.md). Poskytuje hloubka podrobnosti o tom, jak použít rozhraní API Engagement sestavy statistik vaší aplikace.

Mějte na paměti, že pokud chcete pouze Engagement ohlásí aplikace relací, aktivity, dojde k chybě a technické informace, pak nejjednodušší způsob, jak se zajistit všechny vaše `Activity` dílčí třídy dědí odpovídající `EngagementActivity` třídy.

Pokud chcete informace, například pokud je třeba ohlásit určité události aplikace, chyb a úlohy, nebo pokud máte k hlášení aktivitami aplikace jiným způsobem než ten, implementované v `EngagementActivity` třídy, pak budete muset použít rozhraní API zapojení.

Rozhraní API Engagement poskytuje `EngagementAgent` třídy. Instance této třídy může načíst volání `EngagementAgent.getInstance(Context)` statickou metodu (Všimněte si, že `EngagementAgent` objekt vrácený je typu singleton).

## <a name="engagement-concepts"></a>Koncepty engagementu
Následující části Upřesnit nejběžnější [koncepty Mobile Engagementu](mobile-engagement-concepts.md), pro platformu Android.

### <a name="session-and-activity"></a>`Session` a `Activity`
Je-li uživatel více než pár sekundách nečinnosti mezi dvěma *aktivity*, potom jeho posloupnost *aktivity* je rozdělit na dvě odlišné *relací*. Tyto několik sekund, se nazývají "časový limit relace".

*Aktivity* je obvykle spojovány s jednu obrazovku aplikace, to znamená *aktivity* spustí, když se zobrazí na obrazovce a zastaví, když je uzavřený obrazovky: to je případ, kdy je sady Engagement SDK integrovaná pomocí `EngagementActivity` třídy.

Ale *aktivity* můžete ručně kontrolovat také pomocí rozhraní API zapojení. To umožňuje rozdělit dané obrazovky v několik částí Sub – Chcete-li získat další podrobnosti o použití této obrazovky (například jak často známé a jak dlouho se používají dialogová okna v rámci této obrazovce).

## <a name="reporting-activities"></a>Sestavy aktivit
> [!IMPORTANT]
> Nemusíte aktivity, například sestavy popsané v této části, pokud používáte `EngagementActivity` třídy a jeho variant, jak je popsáno v tom, jak integrovat Engagement Android dokumentu.
> 
> 

### <a name="user-starts-a-new-activity"></a>Uživatel spustí novou aktivitu
            EngagementAgent.getInstance(this).startActivity(this, "MyUserActivity", null);
            // Passing the current activity is required for Reach to display in-app notifications, passing null will postpone such announcements and polls.

Je třeba volat `startActivity()` pokaždé, když změny aktivity uživatelů. První volání této funkce spustí novou relaci uživatele.

Nejlepší místo k volání této funkce je v každé aktivity `onResume` zpětného volání.

### <a name="user-ends-his-current-activity"></a>Uživatel končí jeho aktuální aktivita
            EngagementAgent.getInstance(this).endActivity();

Je třeba volat `endActivity()` alespoň jednou po dokončení uživatele poslední aktivita. Tento příkaz informuje sady Engagement SDK, že je uživatel aktuálně nečinnosti, a že relaci uživatele, který je třeba ukončit jednou časový limit relace vyprší (při volání `startActivity()` předtím, než vyprší časový limit relace, relaci jednoduše obnovit).

Nejlepší místo k volání této funkce je v každé aktivity `onPause` zpětného volání.

## <a name="reporting-events"></a>Události vytváření sestav
### <a name="session-events"></a>Události relací
Relace události se obvykle používají k hlášení akcí prováděná uživatelem během jeho relace.

**Příklad bez doplňující data:**

            public MyActivity extends EngagementActivity {
               [...]
               @Override
               public boolean onPrepareOptionsMenu(Menu menu) {
                  getEngagementAgent().sendSessionEvent("menu_shown", null);
               }
               [...]
            }

**Příklad s doplňující data:**

            public MyActivity extends EngagementActivity {
              [...]
              @Override
              public boolean onMenuItemSelected(int featureId, MenuItem item) {
                Bundle extras = new Bundle();
                extras.putInt("id", item.getItemId());
                getEngagementAgent().sendSessionEvent("menu_selected", extras);
              }
              [...]
            }

### <a name="standalone-events"></a>Samostatné události
Rozporu s touto relace události může dojít k událostem samostatné mimo kontext relace.

**Příklad:**

Předpokládejme, že chcete do sestavy událostí, ke kterým dochází při aktivaci všesměrového vysílání příjemce:

            /** Triggered by Intent.ACTION_BATTERY_LOW */
            public BatteryLowReceiver extends BroadcastReceiver {
              [...]
              @Override
              public void onReceive(Context context, Intent intent) {
                EngagementAgent.getInstance(context).sendEvent("battery_low", null);
              }
              [...]
            }

## <a name="reporting-errors"></a>Zasílání zpráv o chybách
### <a name="session-errors"></a>Chyby relace
Relace chyby jsou obvykle používají k hlášení chyb během jeho relace, které mají vliv uživatele.

**Příklad:**

            /** The user has entered invalid data in a form */
            public MyActivity extends EngagementActivity {
              [...]
              public void onMyFormSubmitted(MyForm form) {
                [...]
                /* The user has entered an invalid email address */
                getEngagementAgent().sendSessionError("sign_up_email", null);
                [...]
              }
              [...]
            }

### <a name="standalone-errors"></a>Samostatné chyby
Rozporu s touto relací chyb může dojít k chybám samostatné mimo kontext relace.

**Příklad:**

Následující příklad ukazuje, jak chcete sestavu chybu vždy, když velikost paměti je nedostatek v telefonu během procesu vaše aplikace běží.

            public MyApplication extends EngagementApplication {

              @Override
              protected void onApplicationProcessLowMemory() {
                EngagementAgent.getInstance(this).sendError("low_memory", null);
              }
            }

## <a name="reporting-jobs"></a>Úlohy sestav
### <a name="example"></a>Příklad:
Předpokládejme, že chcete nahlásit trvání přihlašovací proces:

            [...]
            public void signIn(Context context, ...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has started */
              engagementAgent.startJob("sign_in", null);

              [... sign in ...]

              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="report-errors-during-a-job"></a>Sestava chyb během úlohy
Chyby může souviset s probíhající úlohou místo má vztah k aktuální uživatelskou relaci.

**Příklad:**

Předpokládejme, že chcete proces přihlášení k chybě během můžete sestavy:

[...] veřejné přihlášení void (kontextu kontextu,...) {

              /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
              EngagementAgent engagementAgent = EngagementAgent.getInstance(context);

              /* Report sign in job has been started */
              engagementAgent.startJob("sign_in", null);

              /* Try to sign in */
              while(true)
                try {
                  trySignin();
                  break;
                }
                catch(Exception e) {
                  /* Report the error to Engagement */
                  engagementAgent.sendJobError("sign_in_error", "sign_in", null);

                  /* Retry after a moment */
                  sleep(2000);
                }
              [...]
              /* Report sign in job is now ended */
              engagementAgent.endJob("sign_in");
            }
            [...]

### <a name="reporting-events-during-a-job"></a>Události vytváření sestav během úlohy
Události může souviset s probíhající úlohou místo má vztah k aktuální uživatelskou relaci.

**Příklad:**

Předpokládejme, že máme sociálních sítí, a úlohu do sestavy používáme celkovou dobu, během kterého uživatel se připojí k serveru. Uživatele můžete zůstat připojeni pozadí i když mu používá jiná aplikace nebo když je v režimu spánku telefonu, takže není žádná relace.

Uživatel může přijímat zprávy z jeho přátel, jedná se o událost úlohy.

            [...]
            public void signin(Context context, ...) {
              [...Sign in code...]
              EngagementAgent.getInstance(context).startJob("connection", null);
            }
            [...]
            public void signout(Context context) {
              [...Sign out code...]
              EngagementAgent.getInstance(context).endJob("connection");
            }
            [...]
            public void onMessageReceived(Context context) {
              [...Notify in status bar...]
              EngagementAgent.getInstance(context).sendJobEvent("message_received", "connection", null);
            }
            [...]

## <a name="extra-parameters"></a>Další parametry
Libovolná data lze připojit k události, chyb, aktivity a úlohy.

Tato data mohou být strukturovaná, používá třída sady pro Android (ve skutečnosti, funguje jako další parametry v Android záměry). Všimněte si, že sady může obsahovat pole nebo jiné sady instancí.

> [!IMPORTANT]
> Ujistěte se, když vložíte v parametrech parcelable nebo serializable, jejich `toString()` je implementována metoda vrátit čitelná pro člověka řetězec. Serializovatelné třídy, které obsahovat jiné přechodné pole, které nejsou serializovatelný provede Android havárií, když budete volat `bundle.putSerializable("key",value);`
> 
> [!WARNING]
> Zhuštěný pole v další parametry nejsou podporovány, to znamená, nebude možné serializovat jako pole. Je třeba je převést na standardní pole před použitím v další parametry.
> 
> 

### <a name="example"></a>Příklad:
            Bundle extras = new Bundle();
            extras.putString("video_id", 123);
            extras.putString("ref_click", "http://foobar.com/blog");
            EngagementAgent.getInstance(context).sendEvent("video_clicked", extras);

### <a name="limits"></a>Omezení
#### <a name="keys"></a>Klíče
Každý klíč v `Bundle` musí odpovídat následujícímu regulárnímu výrazu:

`^[a-zA-Z][a-zA-Z_0-9]*`

Znamená to, že klíče musí začínat aspoň jedním písmenem, za nímž následuje písmena, číslice nebo podtržítka (\_).

#### <a name="size"></a>Velikost
Funkce jsou omezeny na **1024** znaků na jednu volání (po zakódování ve formátu JSON pomocí služby zapojení).

V předchozím příkladu je JSON odeslat na server 58 znaků:

            {"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

## <a name="reporting-application-information"></a>Informace o vytváření sestav aplikace
Můžete ručně sestavy sledování informace (nebo všechny ostatní aplikace konkrétní informace) pomocí `sendAppInfo()` funkce.

Všimněte si, že tyto údaje lze odeslat přírůstkově: pouze nejnovější hodnotu pro daný klíč budou zachovány pro dané zařízení.

Jako událostí funkce sady třída slouží k abstraktní informace o aplikaci, Všimněte si, že pole nebo dílčí sady bude považována za plochý řetězce (pomocí serializace JSON).

### <a name="example"></a>Příklad:
Zde je ukázka kódu odesílání pohlaví uživatele a datum narození:

            Bundle appInfo = new Bundle();
            appInfo.putString("status", "premium");
            appInfo.putString("expiration", "2016-12-07"); // December 7th 2016
            EngagementAgent.getInstance(context).sendAppInfo(appInfo);

### <a name="limits"></a>Omezení
#### <a name="keys"></a>Klíče
Každý klíč v `Bundle` musí odpovídat následujícímu regulárnímu výrazu:

`^[a-zA-Z][a-zA-Z_0-9]*`

Znamená to, že klíče musí začínat aspoň jedním písmenem, za nímž následuje písmena, číslice nebo podtržítka (\_).

#### <a name="size"></a>Velikost
Informace o aplikaci jsou omezeny na **1024** znaků na jednu volání (po zakódování ve formátu JSON pomocí služby zapojení).

V předchozím příkladu je JSON odeslat na server 44 znaků:

            {"expiration":"2016-12-07","status":"premium"}
