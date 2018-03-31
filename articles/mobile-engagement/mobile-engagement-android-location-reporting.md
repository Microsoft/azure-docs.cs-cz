---
title: Umístění vytváření sestav pro Azure Mobile Engagement Android SDK
description: Popisuje, jak nakonfigurovat umístění vytváření sestav pro Azure Mobile Engagement Android SDK
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 6cab5ed1-b767-46ac-9f0b-48a4e249d88c
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 7978010bd92af18e3ab7c8ccab8dc682d7ef18df
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="location-reporting-for-azure-mobile-engagement-android-sdk"></a>Umístění vytváření sestav pro Azure Mobile Engagement Android SDK
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

> [!div class="op_single_selector"]
> * [Android](mobile-engagement-android-integrate-engagement.md)
> 
> 

Toto téma popisuje postup tvorby sestav pro aplikace Android umístění.

## <a name="prerequisites"></a>Požadavky
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="location-reporting"></a>Rozšířené ohlašování polohy
Pokud chcete umístění třeba ohlásit, je nutné přidat pár řádků konfigurace (mezi `<application>` a `</application>` značek).

### <a name="lazy-area-location-reporting"></a>Opožděné hlášení umístění oblasti
Opožděné hlášení umístění oblasti umožňuje vytváření sestav zemi, oblast a polohu přidružené k zařízení. Tento typ hlášení umístění používá jenom síťová umístění (na základě ID buňky nebo Wi-Fi). Oblasti zařízení se hlásí nejvíce jednou za relace. GPS se nikdy nepoužívá, a proto tento typ umístění sestavy má nízkou dopad na baterii.

Hlášené oblasti se používají k výpočtu geografické statistické údaje o uživatelích, relacích, události a chyby. Může být také používány jako kritérium v kampaně Reach.

Umístění opožděné oblasti vytváření sestav pomocí nástroje Konfigurace výše v tomto postupu povolíte:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Budete taky muset zadat umístění oprávnění. Tento kód používá ``COARSE`` oprávnění:

    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

Pokud vaše aplikace vyžaduje, můžete použít ``ACCESS_FINE_LOCATION`` místo.

### <a name="real-time-location-reporting"></a>Hlášení polohy v reálném čase
Hlášení polohy v reálném čase umožňuje vytváření sestav pro zeměpisnou šířku a zeměpisnou délku přidružené k zařízení. Ve výchozím nastavení používá tento typ hlášení polohy jenom umístění v síti, na základě ID buňky nebo Wi-Fi. Generování sestav je aktivní pouze v případě, že je aplikace spuštěná v popředí (například během relace).

V reálném čase umístění jsou *není* slouží k výpočtu statistiky. Jejich jediným účelem je umožnit použití v reálném čase geografického vymezení \<monitorování geografických Reach-cílové skupiny zón\> kritérium v kampaně Reach.

Povolit v reálném čase umístění vytváření sestav, přidejte řádek kódu kde nastavit připojovací řetězec Engagement v aktivitě Spouštěče. Výsledek vypadá takto:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

        You also need to specify a location permission. This code uses ``COARSE`` permission:

            <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

        If your app requires it, you can use ``ACCESS_FINE_LOCATION`` instead.

#### <a name="gps-based-reporting"></a>Na základě GPS při vytváření sestav
Ve výchozím nastavení hlášení polohy v reálném čase pouze používá síťové umístění. Pokud chcete povolit používání na základě GPS umístění, které jsou mnohem přesnější, použijte objekt konfigurace:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

Musíte taky Pokud chybí, přidejte následující oprávnění:

    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### <a name="background-reporting"></a>Vytváření sestav pozadí
Ve výchozím nastavení hlášení polohy v reálném čase je aktivní pouze když je aplikace spuštěná v popředí (například během relace). Pokud chcete povolit generování sestav také v pozadí, použijte tento objekt konfigurace:

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [!NOTE]
> Když je aplikace spuštěná v pozadí, nahlásí pouze na základě síťové umístění, i když je povolená GPS.
> 
> 

Pokud uživatel restartuje jejich zařízení, je zastavena sestavu umístění pozadí. Chcete-li restartovat automaticky při spuštění, přidejte tento kód.

    <receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
           android:exported="false">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
        </intent-filter>
    </receiver>

Musíte taky Pokud chybí, přidejte následující oprávnění:

    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

## <a name="android-m-permissions"></a>Android M oprávnění
Od verze Android M, některá oprávnění jsou spravovány v době běhu a vyžadují schválení uživatele.

Pokud cílíte na úrovni rozhraní API systému Android 23, oprávnění runtime jsou ve výchozím nastavení vypnuté pro nové instalace aplikace. Jinak jsou zapnuty ve výchozím nastavení.

Vám může povolit nebo zakázat tato oprávnění z nabídky nastavení zařízení. Vypnutí oprávnění z nabídky systém ukončuje procesy na pozadí aplikace, kterou je chování systému a nemá žádný vliv na schopnost přijímat nabízená v pozadí.

V rámci umístění Mobile Engagement vytváření sestav oprávnění, která vyžadují schválení za běhu jsou:

* `ACCESS_COARSE_LOCATION`
* `ACCESS_FINE_LOCATION`

Požádat o oprávnění z uživatele s využitím systému standardní dialogové okno. Pokud uživatel schválí, řekněte ``EngagementAgent`` má provést tato změna v úvahu při v reálném čase. V opačném případě změna zpracování při příštím uživatel spustí aplikaci.

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
         * Request location permission, but this doesn't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }
