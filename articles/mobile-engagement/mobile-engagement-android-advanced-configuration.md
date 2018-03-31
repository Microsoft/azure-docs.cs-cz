---
title: Upřesňující konfigurace pro Azure Mobile Engagement Android SDK
description: Popisuje možnosti pokročilé konfigurace, včetně Android Manifest s Azure Mobile Engagement Android SDK
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 37d2c09a-86fa-473d-8987-c7e35a0eb3e8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 10/04/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 29a7bb7daae59e2034504ce27c9ba66755b11e4b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="advanced-configuration-for-azure-mobile-engagement-android-sdk"></a>Upřesňující konfigurace pro Azure Mobile Engagement Android SDK
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

> [!div class="op_single_selector"]
> * [Univerzální platforma Windows](mobile-engagement-windows-store-advanced-configuration.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-configuration.md)
>
>

Tento postup popisuje, jak nakonfigurovat různé možnosti konfigurace aplikací pro Azure Mobile Engagement Android.

## <a name="prerequisites"></a>Požadavky
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="permission-requirements"></a>Požadavky na oprávnění
Některé možnosti vyžadují specifické oprávnění, které jsou zde uvedeny pro referenční a v řádku v příslušné funkce. Přidejte oprávnění do souboru AndroidManifest.xml vašeho projektu těsně před nebo po `<application>` značky.

Kód oprávnění vyžaduje, aby vypadala jako následující, kde vyplňte příslušná oprávnění z tabulky, který následuje.

    <uses-permission android:name="android.permission.[specific permission]"/>


| Oprávnění | Při použití |
| --- | --- |
| INTERNET |Povinná hodnota. Pro základní vytváření sestav |
| ACCESS_NETWORK_STATE |Povinná hodnota. Pro základní vytváření sestav |
| RECEIVE_BOOT_COMPLETED |Povinná hodnota. Objeví centru oznámení po restartování zařízení |
| WAKE_LOCK |Nedoporučuje. Umožňuje shromažďování dat při použití Wi-Fi nebo při vypnuté obrazovky |
| VIBRATE |Volitelné. Umožňuje vibracím při přijetí oznámení |
| DOWNLOAD_WITHOUT_NOTIFICATION |Volitelné. Umožňuje oznámení Android velký obrázek |
| WRITE_EXTERNAL_STORAGE |Volitelné. Umožňuje oznámení Android velký obrázek |
| ACCESS_COARSE_LOCATION |Volitelné. Umožňuje hlášení polohy v reálném čase |
| ACCESS_FINE_LOCATION |Volitelné. Umožňuje vytváření sestav na základě GPS umístění |

Od verze Android M [některá oprávnění jsou spravovány v době běhu](mobile-engagement-android-location-reporting.md#android-m-permissions).

Pokud už používáte ``ACCESS_FINE_LOCATION``, pak nemusíte použít také ``ACCESS_COARSE_LOCATION``.

## <a name="android-manifest-configuration-options"></a>Možnosti konfigurace pro Android manifestu
### <a name="crash-report"></a>Hlášení o selhání
Chcete-li zakázat hlášení selhání, přidejte tento kód mezi `<application>` a `</application>` značky:

    <meta-data android:name="engagement:reportCrash" android:value="false"/>

### <a name="burst-threshold"></a>Prahová hodnota shluku
Ve výchozím nastavení sestavy služby zapojení protokolů v reálném čase. Pokud se vaše protokoly sestavy aplikací často liší, je lepší ukládat do vyrovnávací paměti protokoly a sestavy je všechny najednou na základní pravidelného času (nazývané "burst režim"). Uděláte to tak, přidejte tento kód mezi `<application>` a `</application>` značky:

    <meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

Režim shluků mírně zvyšuje výdrž baterie, ale má vliv na zapojení monitorování: všechny dobu trvání relace a úlohy jsou zaokrouhleny na shluků prahovou hodnotu (tedy relací a úlohy kratší, než je prahová hodnota shluků nemusí být zobrazeny). Vaše shluků prahová hodnota musí být delší než 30000 (30s).

### <a name="session-timeout"></a>Časový limit relace
 Aktivitu můžete ukončit stisknutím kombinace kláves **Domů** nebo **zpět** klíče, nastavení telefonu nečinnosti nebo přechod do jiné aplikace. Ve výchozím nastavení je relace skončila deseti sekund po uplynutí jeho poslední aktivita. Tím je zabráněno rozdělení relace pokaždé, když uživatel ukončí a vrátí do aplikace rychle, což může dojít při uživatel převezme bitovou kopii, zkontroluje, oznámení atd. Můžete upravit tento parametr. Uděláte to tak, přidejte tento kód mezi `<application>` a `</application>` značky:

    <meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## <a name="disable-log-reporting"></a>Zakázání zasílání zpráv protokolu
### <a name="using-a-method-call"></a>Pomocí volání metody
Pokud chcete Engagement zastavit odesílání protokolů, můžete volat:

    EngagementAgent.getInstance(context).setEnabled(false);

Toto volání je trvalé: používá soubor sdílet předvolby.

Pokud je při volání této funkce aktivní zapojení, může trvat jednu minutu pro zastavení služby. Ale se nespustí službu vůbec při příštím spuštění aplikace.

Můžete povolit protokol reporting znovu voláním stejnou funkci s `true`.

### <a name="integration-in-your-own-preferenceactivity"></a>Integrace ve vašem vlastním `PreferenceActivity`
Namísto volání této funkce, můžete také integrovat toto nastavení přímo do existující `PreferenceActivity`.

Zapojení používat vaše předvolby soubor (s požadovanou režim) můžete nakonfigurovat `AndroidManifest.xml` soubor s `application meta-data`:

* `engagement:agent:settings:name` Klíč se používá k definování názvu souboru sdílet předvolby.
* `engagement:agent:settings:mode` Klíč se používá k definování režim souboru sdílet předvolby. Používat stejný režim jako ve vašem `PreferenceActivity`. Režim musí být předán jako číslo: Pokud používáte kombinaci konstantní příznaky ve vašem kódu, zkontrolujte celkovou hodnotu.

Zapojení vždy používá `engagement:key` boolean klíč v souboru předvoleb pro správu tohoto nastavení.

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
