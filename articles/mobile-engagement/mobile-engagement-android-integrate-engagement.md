---
title: Integraci sady Azure Mobile Engagement Android SDK
description: Nejnovější aktualizace a postupy pro Android SDK pro Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a5487793-1a12-4f6c-a1cf-587c5a671e6b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: fffff6de996b8295639b3d595c5f778de8a0f74f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-integrate-engagement-on-android"></a>Postup při integraci Engagement v systému Android
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

> [!div class="op_single_selector"]
> * [Univerzální pro Windows](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

Tento postup popisuje nejjednodušší způsob, jak aktivovat Engagement analýzy a monitorování funkce ve vaší aplikace Android.

> [!IMPORTANT]
> Vaše minimální úroveň rozhraní API systému Android SDK musí být 10 nebo vyšší (Android 2.3.3 nebo vyšší).
> 
> 

Následující kroky jsou dost aktivuje sestavy protokolů, které jsou potřebné k výpočtu všechny statistické údaje týkající se uživatelů, relací, aktivity, dojde k chybě a Technicals. Sestava protokolů, které jsou potřebné k výpočtu další statistiky, jako jsou události a chyby úlohy je třeba provést ručně pomocí rozhraní API Engagement (najdete v části [jak používat rozšířené Mobile Engagement označování rozhraní API v systémem Android](mobile-engagement-android-use-engagement-api.md) vzhledem k tomu, že tyto statistické údaje jsou závislé aplikace.

## <a name="embed-the-engagement-sdk-and-service-into-your-android-project"></a>Vložení Engagement SDK a služby do vašeho projektu Android
Stažení sady SDK pro Android z [sem](https://aka.ms/vq9mfn) získat `mobile-engagement-VERSION.jar` a umístí je do `libs` složky vašeho projektu Android (vytvořit složku knihovny, pokud neexistuje ještě).

> [!IMPORTANT]
> Pokud vytvoříte balíčku aplikace s ProGuard, budete muset zachovat některé třídy. Můžete použít následující fragment kódu konfigurace:
> 
> -Udržovat veřejná třída * rozšiřuje android.os.IInterface-zachovat {com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS – třída
> 
> <methods>; }
> 
> 

Zadejte připojovací řetězec Engagement voláním následující metodu v rámci Spouštěče aktivity:

            EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
            engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
            EngagementAgent.getInstance(this).init(engagementConfiguration);

Připojovací řetězec pro vaši aplikaci se zobrazí na portálu Azure.

* Pokud chybí, přidejte následující oprávnění Android (před `<application>` značka):
  
          <uses-permission android:name="android.permission.INTERNET"/>
          <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
* Přidejte následující část (mezi `<application>` a `</application>` značky):
  
          <service
            android:name="com.microsoft.azure.engagement.service.EngagementService"
            android:exported="false"
            android:label="<Your application name>Service"
            android:process=":Engagement"/>
* Změna `<Your application name>` podle názvu aplikace.

> [!TIP]
> `android:label` Atribut umožňuje zvolit název služby Engagement, jak ho uvidí koncoví uživatelé na obrazovce "Spuštění služby" svůj telefon. Doporučuje se nastavit tento atribut `"<Your application name>Service"` (například `"AcmeFunGameService"`).
> 
> 

Určení `android:process` atribut zajistí, že Engagement služba bude spuštěna ve svém vlastním procesu (systémem Engagement v rámci jednoho procesu jako vaše aplikace bude vaše vlákna main/uživatelského rozhraní potenciálně pomaleji).

> [!NOTE]
> Kód, umístěte do `Application.onCreate()` a dalších zpětná volání aplikace bude spuštěna pro procesy všechny aplikace, včetně služby zapojení. Může mít nežádoucí vedlejší efekty (jako přiřazení nepotřebné paměti a vláken v procesu zapojení, služby nebo duplicitní všesměrového vysílání příjemci).
> 
> 

Pokud přepíšete `Application.onCreate()`, se doporučuje přidejte následující fragment kódu na začátku vaší `Application.onCreate()` funkce:

             public void onCreate()
             {
               if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
                 return;

               ... Your code...
             }

Může stejnou věc udělat `Application.onTerminate()`, `Application.onLowMemory()` a `Application.onConfigurationChanged(...)`.

Můžete také rozšířit `EngagementApplication` místo rozšíření `Application`: zpětné volání `Application.onCreate()` nemá procesu kontroly a volání `Application.onApplicationProcessCreate()` jen pokud aktuální proces není ta, který je hostitelem služby zapojení, stejná pravidla platit pro ostatní zpětných volání.

## <a name="basic-reporting"></a>Vytváření základních sestav
### <a name="recommended-method-overload-your-activity-classes"></a>Doporučená metoda: přetížení vaší `Activity` třídy
Chcete-li aktivovat sestavu všechny protokoly, které vyžadují zapojení k výpočtu uživatelů, relací, aktivity, dojde k chybě a technické statistiky, stejně musíte provádět všechny vaše `*Activity` dílčí třídy dědí odpovídající `Engagement*Activity` třídy (například pokud vaše aktivita starší verze rozšiřuje `ListActivity`, zkontrolujte ji rozšiřuje `EngagementListActivity`).

**Bez Engagement:**

            package com.company.myapp;

            import android.app.Activity;
            import android.os.Bundle;

            public class MyApp extends Activity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

**S Engagement:**

            package com.company.myapp;

            import com.microsoft.azure.engagement.activity.EngagementActivity;
            import android.os.Bundle;

            public class MyApp extends EngagementActivity
            {
              @Override
              public void onCreate(Bundle savedInstanceState)
              {
                super.onCreate(savedInstanceState);
                setContentView(R.layout.main);
              }
            }

> [!IMPORTANT]
> Při použití `EngagementListActivity` nebo `EngagementExpandableListActivity`, ujistěte se, že všechny volat na `requestWindowFeature(...);` je provedena před volání `super.onCreate(...);`, jinak dojde k chybě.
> 
> 

Můžete najít v těchto tříd `src` složku a můžete je zkopírovat do projektu. Třídy jsou i ve **JavaDoc**.

### <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Alternativní metoda: volání `startActivity()` a `endActivity()` ručně
Pokud nemůžete nebo nechcete přetížení vaše `Activity` třídy, můžete místo toho začínat a končit vaše aktivity voláním `EngagementAgent`na metody přímo.

> [!IMPORTANT]
> Nikdy nevolá SDK pro Android `endActivity()` metoda, i když ukončení aplikace (v systému Android, jsou ve skutečnosti nikdy ukončit aplikace). Z toho důvodu je *vysoce* doporučuje volat `startActivity()` metoda v `onResume` zpětného volání z *všechny* vaše aktivity a `endActivity()` metoda v `onPause()` zpětného volání z *všechny* vaše aktivity. Toto je jediný způsob, jak Ujistěte se, nebude úniku relací. Pokud relace došlo k úniku, službu Engagement nikdy odpojí z back-end Engagement (protože službu zůstane připojen tak dlouho, dokud relace čeká na vyřízení).
> 
> 

Zde naleznete příklad:

            public class MyActivity extends Some3rdPartyActivity
            {
              @Override
              protected void onResume()
              {
                super.onResume();
                String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
                EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
              }

              @Override
              protected void onPause()
              {
                super.onPause();
                EngagementAgent.getInstance(this).endActivity();
              }
            }

Tento příklad velmi podobné `EngagementActivity` třídy a jeho variant, jejichž zdrojový kód je k dispozici v `src` složky.

## <a name="test"></a>Test
Nyní Ověřte prosím svoji integraci spuštěním mobilní aplikaci v emulátoru nebo zařízení a ověření, že registruje relaci na kartě monitorování.

V dalších oddílech jsou volitelné.

## <a name="location-reporting"></a>Rozšířené ohlašování polohy
Pokud chcete umístění třeba ohlásit, je nutné přidat pár řádků konfigurace (mezi `<application>` a `</application>` značek).

### <a name="lazy-area-location-reporting"></a>Opožděné hlášení umístění oblasti
Opožděné hlášení umístění oblasti umožňuje podat zprávu zemi, oblast a polohu přidružené k zařízení. Tento typ hlášení umístění používá jenom síťová umístění (na základě ID buňky nebo Wi-Fi). Oblasti zařízení se hlásí nejvíce jednou za relace. GPS se nikdy nepoužívá, a proto tento typ umístění sestavy má jen v několika (není k vyslovení ne) dopad na baterii.

Hlášené oblasti se používají k výpočtu geografické statistické údaje o uživatelích, relacích, události a chyby. Může být také používány jako kritérium v kampaně Reach.

Pokud chcete povolit opožděné hlášení umístění oblasti, můžete provést pomocí výše v tomto postupu konfigurace:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Musíte taky Pokud chybí, přidejte následující oprávnění:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Nebo můžete používat ``ACCESS_FINE_LOCATION`` pokud ji už používáte ve vaší aplikaci.

### <a name="real-time-location-reporting"></a>Hlášení polohy reálném čase.
Hlášení polohy reálném čase umožňuje podat zprávu pro zeměpisnou šířku a zeměpisnou délku přidružené k zařízení. Ve výchozím nastavení tento typ hlášení umístění používá jenom síťová umístění (na základě ID buňky nebo Wi-Fi) a generování sestav je aktivní pouze když je aplikace spuštěná v popředí (tj. během relace).

Umístění v reálném čase, jsou *není* slouží k výpočtu statistiky. Jejich jediným účelem je umožnit použití geografického vymezení reálném čase \<monitorování geografických Reach-cílové skupiny zón\> kritérium v kampaně Reach.

Pokud chcete povolit generování sestav umístění reálném čase, můžete provést pomocí výše v tomto postupu konfigurace:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Musíte taky Pokud chybí, přidejte následující oprávnění:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Nebo můžete používat ``ACCESS_FINE_LOCATION`` pokud ji už používáte ve vaší aplikaci.

#### <a name="gps-based-reporting"></a>Na základě GPS při vytváření sestav
Ve výchozím nastavení hlášení polohy reálném čase pouze používá síťové umístění. Chcete-li povolit použití GPS na základě umístění (což je mnohem přesnější), použijte objekt konfigurace:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Musíte taky Pokud chybí, přidejte následující oprávnění:

            <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Vytváření sestav pozadí
Ve výchozím nastavení hlášení polohy reálném čase je aktivní pouze když je aplikace spuštěná v popředí (tj. během relace). Pokud chcete povolit také sestav v pozadí, použijte objekt konfigurace:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [!NOTE]
> Při spuštění aplikace v pozadí, pouze sítě na základě umístění nahlásí, i když je povolená GPS.
> 
> 

Sestava umístění pozadí se zastaví, pokud uživatel restartuje jeho zařízení, můžete přidat to, aby se automaticky restartuje při spuštění:

            <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
               android:exported="false">
               <intent-filter>
                  <action android:name="android.intent.action.BOOT_COMPLETED" />
               </intent-filter>
            </receiver>

Musíte taky Pokud chybí, přidejte následující oprávnění:

            <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

### <a name="android-m-permissions"></a>Android M oprávnění
Od verze Android M, některá oprávnění jsou spravovány v době běhu a vyžaduje schválení uživatelem.

Pokud cílíte na úrovni rozhraní API systému Android 23, bude oprávnění runtime vypnuta ve výchozím nastavení pro nové instalace aplikace. V opačném případě se bude se ve výchozím nastavení zapnuta.

Uživatele můžete povolit nebo zakázat tato oprávnění z nabídky nastavení zařízení. Vypnutí oprávnění z nabídky systému ukončí procesy na pozadí aplikace, to je chování systému a nemá žádný vliv na schopnost přijímat nabízená v pozadí.

V kontextu Mobile Engagement oprávnění, která vyžadují schválení za běhu jsou:

* `ACCESS_COARSE_LOCATION`
* `ACCESS_FINE_LOCATION`
* `WRITE_EXTERNAL_STORAGE` (jenom při cílení na úrovni rozhraní API systému Android 23 pro tato)

Externího úložiště se používá pouze pro funkce velký obrázek Reach. Zjistíte-li toto oprávnění být rušivý požádat uživatele, můžete jej odebrat Pokud se používá pouze pro Mobile Engagement, ale za cenu zakázáním funkce velký obrázek.

Pro funkce, umístění měli byste požádat o oprávnění pro uživatele s využitím systému standardní dialogové okno. Pokud uživatel schválí, budete muset říct ``EngagementAgent`` má provést tato změna v úvahu v reálném čase (jinak změny budou zpracovány při příštím uživatel spustí aplikaci).

Zde je ukázka kódu pro použití v aktivitě aplikace žádostí o oprávnění a předání výsledek, pokud je kladné k ``EngagementAgent``:

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this won't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

        /* Only if you want to keep features using external storage */
        if (checkSelfPermission(android.Manifest.permission.WRITE_EXTERNAL_STORAGE) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.WRITE_EXTERNAL_STORAGE }, 1);
      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }

## <a name="advanced-reporting"></a>Rozšířená tvorba sestav
Případně, pokud chcete k hlášení konkrétních událostí aplikace, chyb a úlohy, budete muset použít rozhraní API Engagement prostřednictvím metody `EngagementAgent` třídy. Tato třída objektu může být retreived voláním `EngagementAgent.getInstance()` statickou metodu.

Rozhraní API Engagement umožňuje používat všechny rozšířené možnosti Engagement a podrobně pokynů používat rozhraní API Engagement v systému Android (i jako v technické dokumentaci `EngagementAgent` třídy).

## <a name="advanced-configuration-in-androidmanifestxml"></a>Pokročilá konfigurace (v AndroidManifest.xml)
### <a name="wake-locks"></a>Zámky probuzení
Pokud chcete zajistit, že statistiky se odesílají v reálném čase, při použití Wi-Fi nebo obrazovky je vypnutý, přidejte následující volitelné oprávnění:

            <uses-permission android:name="android.permission.WAKE_LOCK"/>

### <a name="crash-report"></a>Hlášení o selhání
Pokud chcete zakázat hlášení selhání, přidejte tuto (mezi `<application>` a `</application>` značky):

            <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Prahová hodnota shluku
Ve výchozím nastavení sestavy služby zapojení protokolů v reálném čase. Pokud vaše aplikace hlásí protokoly velmi často, je lepší ukládat do vyrovnávací paměti protokoly a sestavy je všechny najednou pravidelné základní časové (to se nazývá "shluků režim"). Uděláte to tak, přidejte tuto (mezi `<application>` a `</application>` značky):

            <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

Režim shluků mírně zvýšit výdrž baterie, ale má vliv na zapojení monitorování: všechny dobu trvání relace a úlohy zaokrouhlen shluků prahovou hodnotu (tedy relací a úlohy kratší, než je prahová hodnota shluků nemusí být zobrazeny). Doporučujeme používat práh shluků než 30000 (30s).

### <a name="session-timeout"></a>Časový limit relace
Ve výchozím nastavení relace je zakončeno 10s po uplynutí jeho poslední aktivita (který obvykle probíhá po stisknutí klávesy Home nebo zpět, nastavení telefonu nečinnosti nebo přechod do jiné aplikace). Tím se vyhnete rozdělení relace každý čas ukončení uživatele a vrátit se do aplikace velmi rychle (který může dojít při mu vyzvednutí bitovou kopii, zkontrolujte, zda oznámení atd.). Můžete upravit tento parametr. Uděláte to tak, přidejte tuto (mezi `<application>` a `</application>` značky):

            <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## <a name="disable-log-reporting"></a>Zakázání zasílání zpráv protokolu
### <a name="using-a-method-call"></a>Pomocí volání metody
Pokud chcete Engagement zastavit odesílání protokolů, můžete volat:

            EngagementAgent.getInstance(context).setEnabled(false);

Toto volání je trvalé: používá soubor sdílet předvolby.

Pokud je při volání této funkce aktivní zapojení, může trvat 1 minuta pro zastavení služby. Ale se nespustí službu vůbec při příštím spuštění aplikace.

Můžete povolit protokol reporting znovu voláním stejnou funkci s `true`.

### <a name="integration-in-your-own-preferenceactivity"></a>Integrace ve vašem vlastním `PreferenceActivity`
Namísto volání této funkce, můžete také integrovat toto nastavení přímo do existující `PreferenceActivity`.

Zapojení používat vaše předvolby soubor (s požadovanou režim) můžete nakonfigurovat `AndroidManifest.xml` soubor s `application meta-data`:

* `engagement:agent:settings:name` Klíč se používá k definování názvu souboru sdílet předvolby.
* `engagement:agent:settings:mode` Klíč se používá k definování režim souboru sdílet předvolby, měli byste použít stejný režim jako ve vašem `PreferenceActivity`. Režim musí být předán jako číslo: Pokud používáte kombinaci konstantní příznaky ve vašem kódu, zkontrolujte celkovou hodnotu.

Zapojení vždy použít `engagement:key` boolean klíč v souboru předvoleb pro správu tohoto nastavení.

Následující příklad `AndroidManifest.xml` zobrazí výchozí hodnoty:

            <application>
                [...]
                <meta-data
                  android:name="engagement:agent:settings:name"
                  android:value="engagement.agent" />
                <meta-data
                  android:name="engagement:agent:settings:mode"
                  android:value="0" />

Potom můžete přidat `CheckBoxPreference` v vaše předvolby rozložení stejný, jako je následující:

            <CheckBoxPreference
              android:key="engagement:enabled"
              android:defaultValue="true"
              android:title="Use Engagement"
              android:summaryOn="Engagement is enabled."
              android:summaryOff="Engagement is disabled." />

<!-- URLs. -->
[Device API]: http://go.microsoft.com/?linkid=9876094
