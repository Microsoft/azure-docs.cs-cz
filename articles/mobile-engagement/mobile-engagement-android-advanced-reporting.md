---
title: Rozšířené možnosti vytváření sestav pro Azure Mobile Engagement Android SDK
description: Popisuje, jak provést pokročilé vytváření sestav pro zachycení analytics pro Azure Mobile Engagement Android SDK
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 7da7abd5-19d6-4892-94d8-818e5424b2cd
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/10/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 419b65250d2b65e184e6e36349b17b5ac9e7a6ba
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="advanced-reporting-with-engagement-on-android"></a>Pokročilé vytváření sestav s Engagement v systému Android
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

> [!div class="op_single_selector"]
> * [Univerzální platforma Windows](mobile-engagement-windows-store-integrate-engagement.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-reporting.md)
> 
> 

Toto téma popisuje další scénáře vytváření sestav v aplikaci pro Android. Tyto možnosti můžete použít pro aplikace vytvořená v [Začínáme](mobile-engagement-android-get-started.md) kurzu.

## <a name="prerequisites"></a>Požadavky
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

Které jste dokončili kurz byl záměrně přímé a jednoduchý, ale jsou rozšířené možnosti, můžete si zvolit.

## <a name="modifying-your-activity-classes"></a>Úprava vaší `Activity` třídy
V [kurzu Začínáme](mobile-engagement-android-get-started.md), všechny jste museli provést aby byla vaše `*Activity` podtřídy dědí odpovídající `Engagement*Activity` třídy. Například, pokud vaše aktivita starší verze rozšířené `ListActivity`, by ho rozšířit provedete `EngagementListActivity`.

> [!IMPORTANT]
> Při použití `EngagementListActivity` nebo `EngagementExpandableListActivity`, ujistěte se, že všechny volat na `requestWindowFeature(...);` je provedena před volání `super.onCreate(...);`, jinak dojde k chybě.
> 
> 

Můžete najít v těchto tříd `src` složku a můžete je zkopírovat do projektu. Třídy jsou i ve **JavaDoc**.

## <a name="alternate-method-call-startactivity-and-endactivity-manually"></a>Alternativní metoda: volání `startActivity()` a `endActivity()` ručně
Pokud nemůžete nebo nechcete přetížení vaše `Activity` třídy, můžete místo toho začínat a končit vaše aktivity voláním `EngagementAgent`na metody přímo.

> [!IMPORTANT]
> Nikdy nevolá SDK pro Android `endActivity()` metoda, i když ukončení aplikace (v systému Android, se nikdy ukončit aplikace). Z toho důvodu je *vysoce* doporučuje volat `startActivity()` metoda v `onResume` zpětného volání z *všechny* vaše aktivity a `endActivity()` metoda v `onPause()` zpětného volání z *všechny* vaše aktivity. Toto je jediný způsob, jak Ujistěte se, že nejsou neuniknou relací. Pokud relace došlo k úniku, Engagement služba nikdy odpojí z back-end Engagement (protože službu zůstane připojen tak dlouho, dokud relace čeká na vyřízení).
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

V tomto příkladu je podobná `EngagementActivity` třídy a jeho variant, jejichž zdrojový kód je k dispozici v `src` složky.

## <a name="using-applicationoncreate"></a>Pomocí Application.onCreate()
Kód, umístěte do `Application.onCreate()` a v jiné aplikaci zpětná volání spuštění pro procesy na všechny aplikace, včetně služby zapojení. Může mít nežádoucí vedlejší účinky, jako je přidělování nepotřebné paměti a vláken v procesu zapojení, nebo duplicitní všesměrového vysílání příjemci nebo službám.

Pokud přepíšete `Application.onCreate()`, doporučujeme, abyste přidáním následující fragment kódu na začátku vaší `Application.onCreate()` funkce:

     public void onCreate()
     {
       if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
         return;

       ... Your code...
     }

Může stejnou věc udělat `Application.onTerminate()`, `Application.onLowMemory()`, a `Application.onConfigurationChanged(...)`.

Můžete také rozšířit `EngagementApplication` místo rozšíření `Application`: zpětné volání `Application.onCreate()` nemá procesu kontroly a volání `Application.onApplicationProcessCreate()` jen pokud aktuální proces není ta, který je hostitelem služby zapojení, stejná pravidla platit pro ostatní zpětných volání.

## <a name="tags-in-the-androidmanifestxml-file"></a>Značky v souboru AndroidManifest.xml
Ve značce služby v souboru AndroidManifest.xml `android:label` atribut umožňuje zvolit název služby Engagement, jak se objevuje pro koncové uživatele na obrazovce "Spuštění služby" svůj telefon. Je vhodné nastavení tohoto atributu na `"<Your application name>Service"` (například `"AcmeFunGameService"`).

Určení `android:process` atribut zajišťuje, že služba Engagement běží ve svém vlastním procesu (systémem Engagement v rámci jednoho procesu jako vaše aplikace provede potenciálně pomaleji vlákna vaší hlavní nebo uživatelského rozhraní).

## <a name="building-with-proguard"></a>Sestavování s použitím ProGuard
Pokud vytvoříte balíčku aplikace s ProGuard, budete muset zachovat některé třídy. Můžete použít následující fragment kódu konfigurace:

    -keep public class * extends android.os.IInterface
    -keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
    <methods>;
     }
