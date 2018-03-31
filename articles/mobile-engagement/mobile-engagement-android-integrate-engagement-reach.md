---
title: Integraci sady Azure Mobile Engagement Android SDK
description: Nejnovější aktualizace a postupy pro Android SDK pro Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 9ec3fab3-35ec-458e-bf41-6cdd69e3fa44
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 06/27/2016
ms.author: piyushjo
ms.openlocfilehash: 15e71d8aeb8c8060161ade4262e0be0a093b5650
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-engagement-reach-on-android"></a>Postup při integraci Engagement Reach pro Android
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

> [!IMPORTANT]
> Postupujte podle integrace postup popsaný v tom, jak integrovat Engagement Android dokumentu před těchto pokynů.
> 
> 

## <a name="standard-integration"></a>Standardní integrace

Zkopírujte soubory prostředků Reach ze sady SDK do projektu:

* Kopírování souborů `res/layout` složky doručit pomocí sady SDK do `res/layout` složky vaší aplikace.
* Kopírování souborů `res/drawable` složky doručit pomocí sady SDK do `res/drawable` složky vaší aplikace.

Upravit vaše `AndroidManifest.xml` souboru:

* Přidejte následující část (mezi `<application>` a `</application>` značky):
  
          <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
              <category android:name="android.intent.category.DEFAULT" />
              <data android:mimeType="text/plain" />
            </intent-filter>
          </activity>
          <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
              <category android:name="android.intent.category.DEFAULT" />
              <data android:mimeType="text/html" />
            </intent-filter>
          </activity>
          <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light" android:exported="false">
            <intent-filter>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
              <category android:name="android.intent.category.DEFAULT" />
            </intent-filter>
          </activity>
          <activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog" android:exported="false">
            <intent-filter>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
              <category android:name="android.intent.category.DEFAULT"/>
            </intent-filter>
          </activity>
          <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
            <intent-filter>
              <action android:name="android.intent.action.BOOT_COMPLETED"/>
              <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
              <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
              <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
            </intent-filter>
          </receiver>
          <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
            <intent-filter>
              <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
            </intent-filter>
          </receiver>
* Potřebujete tato oprávnění opakování systémová oznámení, které nebyly použity při spuštění (jinak zůstanou na disku, ale už se nezobrazí, je opravdu nutné zahrnout to).
  
          <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
* Určení ikony pro oznámení (v aplikaci a systému ty, které jsou i) používané kopírování a úpravy v následující části (mezi `<application>` a `</application>` značky):
  
          <meta-data android:name="engagement:reach:notification:icon" android:value="<name_of_icon_WITHOUT_file_extension_and_WITHOUT_'@drawable/'>" />

> [!IMPORTANT]
> Tato část se **povinné** Pokud máte v úmyslu používat systémová oznámení při vytváření kampaně Reach. Android zabrání systémová oznámení bez ikony se zobrazí. Proto pokud vynecháte v této části, koncoví uživatelé nebudou moci přijímat.
> 
> 

* Pokud vytvoříte kampaně s systémová oznámení pomocí velký obrázek, je nutné přidat následující oprávnění (po `</application>` značka) Pokud chybí:
  
          <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
          <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>
  
  * V systému Android M a pokud aplikace cílí úroveň rozhraní API systému Android 23 nebo vyšší ``WRITE_EXTERNAL_STORAGE`` oprávnění vyžaduje schválení uživatelem. Přečtěte si prosím [v této části](mobile-engagement-android-integrate-engagement.md#android-m-permissions).
* Pro systémová oznámení je můžete také zadat v kampaně Reach Pokud by se prstence nebo zavibrovat zařízení. U ho na spolupráci, budete muset zajistěte, aby deklarovaný následující oprávnění (po `</application>` značka):
  
          <uses-permission android:name="android.permission.VIBRATE" />
  
  Bez tohoto oprávnění zabrání Android systémových oznámení se zobrazí, pokud zkontroluje okruhu nebo možnost vibrate ve Správci kampaně Reach.

## <a name="native-push"></a>Nativní oznámení
Teď, když jste nakonfigurovali modul Reach, budete muset nakonfigurovat nativního nabízení být schopný přijímat kampaně v zařízení.

V systému Android podporujeme dvě služby:

* Google Play zařízení: použití [Google Cloud Messaging] podle [jak integrovat GCM s Engagement průvodce](mobile-engagement-android-gcm-integrate.md) průvodce.
* Zařízení Amazon: použití [Amazon Device Messaging] podle [jak integrovat ADM s Engagement průvodce](mobile-engagement-android-adm-integrate.md) průvodce.

Pokud chcete zacílit Amazon a webu Google Play zařízení, jeho možné mít všechno uvnitř 1 AndroidManifest.xml/APK pro vývoj. Ale při odesílání do Amazon, pokud se najdou GCM kódu může zamítnutí vaší aplikace.

V takovém případě byste měli používat více APKs.

**Aplikace je nyní připraven přijmout a zobrazit kampaně reach!**

## <a name="how-to-handle-data-push"></a>Postupy: zpracování datová oznámení
### <a name="integration"></a>Integrace
Pokud chcete aplikace nebudou moct přijímat Reach datová oznámení, je nutné vytvořit podtřídou třídy `com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver` a odkazovat na jeho `AndroidManifest.xml` souboru (mezi `<application>` nebo `</application>` značky):

            <receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
              </intent-filter>
            </receiver>

Pak můžete přepsat `onDataPushStringReceived` a `onDataPushBase64Received` zpětných volání. Zde naleznete příklad:

            public class MyDataPushReceiver extends EngagementReachDataPushReceiver
            {
              @Override
              protected Boolean onDataPushStringReceived(Context context, String category, String body)
              {
                Log.d("tmp", "String data push message received: " + body);
                return true;
              }

              @Override
              protected Boolean onDataPushBase64Received(Context context, String category, byte[] decodedBody, String encodedBody)
              {
                Log.d("tmp", "Base64 data push message received: " + encodedBody);
                // Do something useful with decodedBody like updating an image view
                return true;
              }
            }

### <a name="category"></a>Kategorie
Kategorie parametr je volitelný při vytváření kampaň nabízených Data a umožňuje že vám data filtru nabízených oznámení. To je užitečné, pokud máte několik všesměrového vysílání příjemci zpracování různé typy dat nabízených oznámení, nebo pokud chcete push různých druhů z `Base64` dat a chcete určit jejich typů před analýzou je.

### <a name="callbacks-return-parameter"></a>Návratový parametr zpětných volání.
Zde je několik doporučení správně zpracovat návratový parametr `onDataPushStringReceived` a `onDataPushBase64Received`:

* Všesměrového vysílání příjemce by měla vrátit `null` v zpětného volání, pokud ho nebude vědět, jak pro zpracování dat push. Kategorie používejte k určení, zda má váš všesměrového vysílání přijímač zpracovávat datová oznámení nebo ne.
* Jeden z všesměrového vysílání příjemce by měla vrátit `true` v zpětného volání, pokud ji přijímá datová oznámení.
* Jeden z všesměrového vysílání příjemce by měla vrátit `false` v zpětného volání, pokud rozpozná datová oznámení, ale zahodí z jakéhokoliv důvodu. Například vrátit `false` při přijatá data jsou neplatná.
* Pokud jeden vysílání příjemce vrátí `true` při jiné jeden vrátí `false` pro stejné datová oznámení chování není definován, měli byste nikdy udělat.

Návratový typ se používá pouze pro Reach statistiky:

* `Replied` se zvýší, pokud jeden z všesměrového vysílání příjemci vrácen buď `true` nebo `false`.
* `Actioned` se zvýší, pouze v případě, že jeden všesměrového vysílání příjemci vrátil `true`.

## <a name="how-to-customize-campaigns"></a>Postup přizpůsobení kampaně
Chcete-li přizpůsobit kampaní, můžete upravit rozložení poskytovaných v sadě SDK dosáhnout.

Měli byste zachovat všechny identifikátory použité v rozložení a typy zobrazení, která použijte identifikátor, hlavně pro zobrazení textu zobrazení bitové kopie a zachovat. Některá zobrazení se právě používají pro skrytí nebo zobrazení oblasti, tak jejich typ může být změněn. Pokud chcete změnit typ zobrazení v zadané rozložení Zkontrolujte zdrojový kód.

### <a name="notifications"></a>Oznámení
Existují dva typy oznámení: systém a v aplikaci oznámení, které používají různé rozložení soubory.

#### <a name="system-notifications"></a>Systémová oznámení
Chcete-li přizpůsobit systémová oznámení, budete muset použít **kategorie**. Můžete přejít na [kategorie](#categories).

#### <a name="in-app-notifications"></a>Oznámení v aplikaci
Ve výchozím nastavení, oznámení v aplikaci je zobrazení, která se dynamicky přidá do uživatelského rozhraní aktuální aktivity díky Android metoda `addContentView()`. Tomu se říká překrytí oznámení. Překryvy oznámení jsou skvělý pro rychlé integraci, protože nevyžadují, abyste upravili žádné rozložení v aplikaci.

Pokud chcete upravit vzhled vaší překryvy oznámení, můžete jednoduše upravit soubor `engagement_notification_area.xml` vašim potřebám.

> [!NOTE]
> Soubor `engagement_notification_overlay.xml` je ten, který se používá k vytvoření oznámení překrytí, obsahuje soubor `engagement_notification_area.xml`. Můžete také přizpůsobit ho tak, aby vyhovovala vašim potřebám (například pro umístění oznamovací oblasti v rámci překrytí).
> 
> 

##### <a name="include-notification-layout-as-part-of-an-activity-layout"></a>Zahrnout rozložení oznámení jako součást rozložení aktivity
Překryvy se výborně hodí pro rychlé integrace, ale můžete bylo nepraktické nebo mít vedlejší účinky ve zvláštních případech. Systém překrytí lze přizpůsobit na úrovni aktivity, a usnadňuje tak zabránit vedlejší účinky pro speciální aktivity.

Můžete se rozhodnout zahrnout naše rozložení oznámení do vaší stávající rozložení díky Android **zahrnují** příkaz. Tady je příklad upravené `ListActivity` rozložení obsahující jenom `ListView`.

**Před integraci sady Engagement:**

            <?xml version="1.0" encoding="utf-8"?>
            <ListView
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@android:id/list"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent" />

**Po integraci sady Engagement:**

            <?xml version="1.0" encoding="utf-8"?>
            <LinearLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:orientation="vertical"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <ListView
                android:id="@android:id/list"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:layout_weight="1" />

              <include layout="@layout/engagement_notification_area" />

            </LinearLayout>

V tomto příkladu jsme přidali nadřazený kontejner, protože původní rozložení použity zobrazení seznamu jako element nejvyšší úrovně. Jsme přidali i `android:layout_weight="1"` moct přidat zobrazení níže zobrazení seznamu nakonfigurované `android:layout_height="fill_parent"`.

Engagement Reach SDK automaticky zjistí, že rozložení oznámení je zahrnutá v této aktivitě a nepřidá překrytí pro tuto aktivitu.

> [!TIP]
> Pokud používáte ListActivity ve vaší aplikaci, bude reagovat na kliknutí na položky v zobrazení seznamu už zabránit viditelné překrytí Reach. Jedná se o známý problém. Chcete-li vyřešit tento problém, doporučujeme vám vložení rozložení oznámení v rozložení vlastní seznam aktivitu jako v předchozí ukázce aplikací.
> 
> 

##### <a name="disabling-application-notification-per-activity"></a>Zakázat oznámení aplikace na aktivitu
Pokud nechcete, aby překrytí přidávaného do vaší aktivity, a pokud nezadáte rozložení oznámení ve vaší vlastní rozložení, můžete zakázat v překrytí pro tuto aktivitu v `AndroidManifest.xml` přidáním `meta-data` oddílu, jako v následujícím příkladu:

            <activity android:name="SplashScreenActivity">
              <meta-data android:name="engagement:notification:overlay" android:value="false"/>
            </activity>

#### <a name="categories"></a> Kategorie
Při úpravě zadané rozložení upravíte vzhledu všechna oznámení. Kategorie umožňují definovat různé cílové vypadá (pravděpodobně chování) pro oznámení. Kategorie lze při vytváření kampaně Reach. Mějte na paměti, že kategorie vám také umožní přizpůsobit oznámení a hlasování, který je popsán dále v tomto dokumentu.

Chcete-li zaregistrovat kategorie obslužnou rutinu pro oznámení, přidejte volání při inicializaci aplikace.

> [!IMPORTANT]
> Přečtěte si upozornění o atribut android: proces \<android-sdk-engagement-process\> v tom, jak integrovat Engagement na Android tématu než budete pokračovat.
> 
> 

Následující příklad předpokládá můžete potvrdí předchozí upozornění a použít podtřídou třídy `EngagementApplication`:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), "myCategory");
              }
            }

`MyNotifier` Objektu je implementace obslužné rutiny kategorie oznámení. Jedná se buď implementaci `EngagementNotifier` rozhraní nebo dílčí třídu výchozí implementace: `EngagementDefaultNotifier`.

Všimněte si, že téhož oznamovatele dokáže zpracovat několika kategorií je můžete zaregistrovat takto:

            reachAgent.registerNotifier(new MyNotifier(this), "myCategory", "myAnotherCategory");

Pokud chcete nahradit výchozí implementace kategorie, můžete zaregistrovat implementaci jako v následujícím příkladu:

            public class MyApplication extends EngagementApplication
            {
              @Override
              protected void onApplicationProcessCreate()
              {
                // [...] other init
                EngagementReachAgent reachAgent = EngagementReachAgent.getInstance(this);
                reachAgent.registerNotifier(new MyNotifier(this), Intent.CATEGORY_DEFAULT); // "android.intent.category.DEFAULT"
              }
            }

Aktuální kategorie použitá v obslužné rutině se předá jako parametr v většinu metod, můžete přepsat v `EngagementDefaultNotifier`.

Je předán buď jako `String` parametr nebo nepřímo v `EngagementReachContent` objekt, který má `getCategory()` metoda.

Můžete změnit většinu oznámení procesu vytváření opětovná definice metody na `EngagementDefaultNotifier`, pro pokročilejší přizpůsobení klidně si prohlédněte v technické dokumentaci a zdrojový kód.

##### <a name="in-app-notifications"></a>Oznámení v aplikaci
Pokud chcete použít alternativní rozložení pro určitou kategorii, můžete to implementovat jako v následujícím příkladu:

            public class MyNotifier extends EngagementDefaultNotifier
            {
              public MyNotifier(Context context)
              {
                super(context);
              }

              @Override
              protected int getOverlayLayoutId(String category)
              {
                return R.layout.my_notification_overlay;
              }


              @Override
              public Integer getOverlayViewId(String category)
              {
                return R.id.my_notification_overlay;
              }

              @Override
              public Integer getInAppAreaId(String category)
              {
                return R.id.my_notification_area;
              }
            }

**Příklad `my_notification_overlay.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <RelativeLayout
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:id="@+id/my_notification_overlay"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <include layout="@layout/my_notification_area" />

            </RelativeLayout>

Jak vidíte, se liší od standardní jeden identifikátor zobrazit překrytí. Je důležité, aby každé rozložení použít jedinečný identifikátor pro překryvy.

**Příklad `my_notification_area.xml` :**

            <?xml version="1.0" encoding="utf-8"?>
            <merge
              xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="fill_parent"
              android:layout_height="fill_parent">

              <RelativeLayout
                android:id="@+id/my_notification_area"
                android:layout_width="fill_parent"
                android:layout_height="64dp"
                android:layout_alignParentTop="true"
                android:background="#B000">

                <LinearLayout
                  android:orientation="horizontal"
                  android:layout_width="fill_parent"
                  android:layout_height="fill_parent"
                  android:gravity="center_vertical">

                  <ImageView
                    android:id="@+id/engagement_notification_icon"
                    android:layout_width="48dp"
                    android:layout_height="48dp" />

                  <LinearLayout
                    android:id="@+id/engagement_notification_text"
                    android:orientation="vertical"
                    android:layout_width="fill_parent"
                    android:layout_height="fill_parent"
                    android:layout_weight="1"
                    android:gravity="center_vertical">

                    <TextView
                      android:id="@+id/engagement_notification_title"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:singleLine="true"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Medium" />

                    <TextView
                      android:id="@+id/engagement_notification_message"
                      android:layout_width="fill_parent"
                      android:layout_height="wrap_content"
                      android:maxLines="2"
                      android:ellipsize="end"
                      android:textAppearance="@android:style/TextAppearance.Small" />

                  </LinearLayout>

                  <ImageView
                    android:id="@+id/engagement_notification_image"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:adjustViewBounds="true" />

                  <ImageButton
                    android:id="@+id/engagement_notification_close_area"
                    android:visibility="invisible"
                    android:layout_width="wrap_content"
                    android:layout_height="fill_parent"
                    android:src="@android:drawable/btn_dialog"
                    android:background="#0F00" />

                </LinearLayout>

                <ImageButton
                  android:id="@+id/engagement_notification_close"
                  android:layout_width="wrap_content"
                  android:layout_height="fill_parent"
                  android:layout_alignParentRight="true"
                  android:src="@android:drawable/btn_dialog"
                  android:background="#0F00" />

              </RelativeLayout>

            </merge>

Jak vidíte, se liší od standardní jeden identifikátoru oznámení oblasti zobrazení. Je důležité, že každé rozložení používá jedinečný identifikátor pro oznamovací oblasti.

Tento jednoduchý příklad kategorie umožňuje aplikace (nebo v aplikaci) oznámení zobrazí v horní části obrazovky. Standardní identifikátory používané v oznamovací oblasti samotné jsme nezměnila.

Pokud chcete změnit, který, budete muset znovu definovat `EngagementDefaultNotifier.prepareInAppArea` metoda. Podívejte se na technickou dokumentaci a na zdrojový kód se doporučuje `EngagementNotifier` a `EngagementDefaultNotifier` Pokud chcete tato úroveň rozšířené úpravy.

##### <a name="system-notifications"></a>Systémová oznámení
Tím, že rozšíří `EngagementDefaultNotifier`, můžete přepsat `onNotificationPrepared` ke změně oznámení, že byl připraven výchozí implementace.

Příklad:

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content)
              throws RuntimeException
            {
              if ("ongoing".equals(content.getCategory()))
                notification.flags |= Notification.FLAG_ONGOING_EVENT;
              return true;
            }

Tento příklad vytvoří systémové oznámení pro obsah, se zobrazuje jako probíhající událost v případě, že se používá "probíhající" kategorie.

Pokud chcete vytvořit `Notification` objektu od začátku, můžete se vrátit `false` metoda a volání `notify` sami na `NotificationManager`. V takovém případě je důležité, aby byl `contentIntent`, `deleteIntent` a identifikátor oznámení používá `EngagementReachReceiver`.

Tady je správný příklad takových implementace:

            @Override
            protected boolean onNotificationPrepared(Notification notification, EngagementReachInteractiveContent content) throws RuntimeException
            {
              /* Required fields */
              NotificationCompat.Builder builder = new NotificationCompat.Builder(mContext)
                .setSmallIcon(notification.icon)              // icon is mandatory
                .setContentIntent(notification.contentIntent) // keep content intent
                .setDeleteIntent(notification.deleteIntent);  // keep delete intent

              /* Your customization */
              // builder.set...

              /* Dismiss option can be managed only after build */
              Notification myNotification = builder.build();
              if (!content.isNotificationCloseable())
                myNotification.flags |= Notification.FLAG_NO_CLEAR;

              /* Notify here instead of super class */
              NotificationManager manager = (NotificationManager) mContext.getSystemService(Context.NOTIFICATION_SERVICE);
              manager.notify(getNotificationId(content), myNotification); // notice the call to get the right identifier

              /* Return false, we notify ourselves */
              return false;
            }

##### <a name="notification-only-announcements"></a>Oznámení pouze oznámení
Správu kliknutím na oznámení pouze oznámení lze přizpůsobit přepsáním `EngagementDefaultNotifier.onNotifAnnouncementIntentPrepared` k úpravě připraveného `Intent`. Pomocí této metody můžete snadno ladit příznaků.

Chcete-li například přidat `SINGLE_TOP` příznak:

            @Override
            protected Intent onNotifAnnouncementIntentPrepared(EngagementNotifAnnouncement notifAnnouncement,
              Intent intent)
            {
              intent.addFlags(Intent.FLAG_ACTIVITY_SINGLE_TOP);
              return intent;
            }

Pro starší verze zapojení uživatelů Pamatujte, že systémová oznámení bez akce URL teď spustí aplikaci pokud byl v pozadí, takže tato metoda může být volána s hlášení bez adresa URL akce. Měli byste zvážit, když přizpůsobení záměr.

Můžete taky implementovat `EngagementNotifier.executeNotifAnnouncementAction` od začátku.

##### <a name="notification-life-cycle"></a>Oznámení životního cyklu
Při použití výchozí kategorie, se nazývají některé metody životní cyklus na `EngagementReachInteractiveContent` do sestavy statistik objektu a aktualizovat stav kampaně:

* Když se zobrazí v aplikaci nebo put ve stavovém řádku oznámení `displayNotification` metoda je volána (který sestavy statistik) podle `EngagementReachAgent` Pokud `handleNotification` vrátí `true`.
* Pokud se zavře oznámení, `exitNotification` metoda je volána, statistiky se použije v hlášení a další kampaně lze nyní zpracovat.
* Po kliknutí na oznámení `actionNotification` je volána, statistiky se použije v hlášení a spuštění přidružené záměr.

Pokud vaši implementaci `EngagementNotifier` obchází výchozí chování, budete muset pro volání těchto metod životní cyklus sami. Následující příklady ilustrují některých případech, kde přeskočí výchozí chování:

* Nerozšíříte `EngagementDefaultNotifier`, například implementována kategorie zpracování od začátku.
* Pro systémová oznámení overrode `onNotificationPrepared` a můžete změnit `contentIntent` nebo `deleteIntent` v `Notification` objektu.
* Oznámení v aplikaci, overrode `prepareInAppArea`, je nutné namapovat minimálně `actionNotification` na jednu z vaší U.I ovládací prvky.

> [!NOTE]
> Pokud `handleNotification` vyvolá výjimku, obsah se odstraní a `dropContent` je volána. To je uvedený v statistiky a další kampaně lze nyní zpracovat.
> 
> 

### <a name="announcements-and-polls"></a>Oznámení a hlasování
#### <a name="layouts"></a>Rozložení
Můžete upravit `engagement_text_announcement.xml`, `engagement_web_announcement.xml` a `engagement_poll.xml` soubory pro přizpůsobení textu oznámení, oznámení webové a hlasování.

Tyto soubory sdílet dvě běžné rozložení pro oblast nadpisu a oblasti tlačítka. Rozložení pro nadpis `engagement_content_title.xml` a použije eponymous drawable soubor pozadí. Rozložení pro tlačítek akce a ukončení `engagement_button_bar.xml` a použije eponymous drawable soubor pozadí.

V hlasování, rozložení otázku a jejich možnosti jsou dynamicky zvětšený pomocí několikrát `engagement_question.xml` soubor rozložení pro otázky a `engagement_choice.xml` soubor pro výběr.

#### <a name="categories"></a>Kategorie
##### <a name="alternate-layouts"></a>Alternativní rozložení
Jako oznámení kategorie kampaně umožňuje mít alternativní rozložení pro oznámení a hlasování.

Pokud chcete vytvořit kategorii pro text oznámení, například můžete rozšířit `EngagementTextAnnouncementActivity` a na něj odkazovat `AndroidManifest.xml` souboru:

            <activity android:name="com.your_company.MyCustomTextAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/plain" />
              </intent-filter>
            </activity>

Všimněte si, že kategorie ve záměrné filtru se používá k zajištění rozdíl oproti výchozí aktivita oznámení.

Sady Reach SDK používá záměrné systému přeložit vpravo aktivity pro určitou kategorii a jeho spadne zpět na výchozí kategorie Pokud řešení se nezdařilo.

Je třeba implementovat `MyCustomTextAnnouncementActivity`, pokud chcete změnit rozložení (ale zachovat stejné identifikátory zobrazení), musíte se definice třídy, jako v následujícím příkladu:

            public class MyCustomTextAnnouncementActivity extends EngagementTextAnnouncementActivity
            {
              @Override
              protected String getLayoutName()
              {
                return "my_text_announcement";  // tell super class to use R.layout.my_text_announcement
              }
            }

Pokud chcete nahradit kategorii výchozí text oznámení, jednoduše nahradit `android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"` podle vaší implementace.

Podobně lze přizpůsobit web oznámení a hlasování.

Pro web oznámení můžete rozšířit `EngagementWebAnnouncementActivity` a deklarovat si v aktivitu `AndroidManifest.xml` jako v následujícím příkladu:

            <activity android:name="com.your_company.MyCustomWebAnnouncementActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
                <category android:name="my_category" />
                <data android:mimeType="text/html" />    <!-- only difference with text announcements in the intent is the data mime type -->
              </intent-filter>
            </activity>

Pro dotazování můžete rozšířit `EngagementPollActivity` a deklarovat vaší v `AndroidManifest.xml` jako v následujícím příkladu:

            <activity android:name="com.your_company.MyCustomPollActivity">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
                <category android:name="my_category" />
              </intent-filter>
            </activity>

##### <a name="implementation-from-scratch"></a>Implementace od začátku
Kategorie můžete implementovat vaše aktivity oznámení (a dotazování) bez jeden z rozšíření `Engagement*Activity` třídy poskytované Reach SDK. To je užitečné, například pokud chcete definovat rozložení, který nepoužívá stejnou zobrazení jako standardní rozložení.

Jako pro přizpůsobení pokročilé oznámení, doporučujeme prohlédnout si zdrojový kód standardní implementace.

Tady jsou některé věci, třeba vzít v úvahu: Reach spustí aktivita konkrétní záměrem (odpovídající záměrné filtr) plus další parametr, který je identifikátor obsahu.

Chcete-li načíst objekt obsahu, který obsahovat pole, která jste zadali při vytváření kampaň na webu můžete postupujte takto:

            public class MyCustomTextAnnouncement extends EngagementActivity
            {
              private EngagementAnnouncement mContent;

              @Override
              protected void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);

                /* Get content */
                mContent = EngagementReachAgent.getInstance(this).getContent(getIntent());
                if (mContent == null)
                {
                  /* If problem with content, exit */
                  finish();
                  return;
                }

                setContentView(R.layout.my_text_announcement);

                /* Configure views by querying fields on mContent */
                // ...
              }
            }

Pro statistiku, byste měli hlásit obsah se zobrazí v `onResume` událostí:

            @Override
            protected void onResume()
            {
             /* Mark the content displayed */
             mContent.displayContent(this);
             super.onResume();
            }

Potom, nezapomeňte volat buď `actionContent(this)` nebo `exitContent(this)` v obsahu objektu před aktivity přejde do pozadí.

Pokud nemůžete volat buď `actionContent` nebo `exitContent`, statistiky se neodešlou (tj. žádné analýzy kampaň) a je důležité, nebude další kampaně upozorněni, až po restartování procesu aplikace.

Orientace nebo jiné změny konfigurace můžete provést kód složité určit, zda aktivity přejde do pozadí nebo Ne, standardní implementace zajišťuje obsah hlášení jako byl ukončen, pokud uživatel odejde aktivity (buď stisknutím `HOME`nebo `BACK`), ale ne, pokud se změní orientaci.

Tady je zajímavé součástí implementace:

            @Override
            protected void onUserLeaveHint()
            {
              finish();
            }

            @Override
            protected void onPause()
            {
              if (isFinishing() && mContent != null)
              {
                /*
                 * Exit content on exit, this is has no effect if another process method has already been
                 * called so we don't have to check anything here.
                 */
                mContent.exitContent(this);
              }
              super.onPause();
            }

Jak můžete vidět, pokud jste volali metodu `actionContent(this)` pak dokončení aktivity, `exitContent(this)` lze bezpečně volat bez nutnosti nijak neprojeví.

[here]:http://developer.android.com/tools/extras/support-library.html#Downloading
[Google Cloud Messaging]:http://developer.android.com/guide/google/gcm/index.html
[Amazon Device Messaging]:https://developer.amazon.com/sdk/adm.html
