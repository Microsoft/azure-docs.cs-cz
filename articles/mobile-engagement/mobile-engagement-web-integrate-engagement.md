---
title: Integrace se službou Azure Mobile Engagement Web SDK | Microsoft Docs
description: Nejnovější aktualizace a postupy pro Web Azure Mobile Engagement SDK
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: b5daa2a2-942b-489d-aa1d-568c3b25e56f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 02/29/2016
ms.author: piyushjo
ms.openlocfilehash: bccfbdfe9d99900f58d5dbfa44183146c79b9c88
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="integrate-azure-mobile-engagement-in-a-web-application"></a>Integrovat Azure Mobile Engagement ve webové aplikaci
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

V tomto článku jsou popsány nejjednodušší způsob, jak aktivovat analýzy a monitorování funkcí v Azure Mobile Engagement ve webové aplikaci.

Postupujte podle kroků k aktivaci protokolu sestavy, které jsou potřebné k výpočtu všechny statistické údaje o uživatelích, relacích, aktivity, dojde k chybě a technicals. Statistics závislé aplikace, jako jsou události a chyby, úlohy je nutné aktivovat sestavy protokolu ručně pomocí rozhraní API služby Azure Mobile Engagement. Další informace najdete další [jak používat rozšířené Mobile Engagement označování rozhraní API ve webové aplikaci](mobile-engagement-web-use-engagement-api.md).

## <a name="introduction"></a>Úvod
[Stažení na webu Azure Mobile Engagement SDK](http://aka.ms/P7b453).
Mobile Engagement Web SDK je dodávána jako jeden soubor JavaScript, azure-engagement.js, které je nutné zahrnout v každé stránce webu či webové aplikace.

> [!IMPORTANT]
> Před spuštěním tohoto skriptu, je nutné spustit fragment skriptu nebo kód, který zapisovat konfigurace Mobile Engagementu pro vaši aplikaci.
> 
> 

## <a name="browser-compatibility"></a>Kompatibilita s prohlížeči
Mobile Engagement Web SDK používá nativní JSON kódování a dekódování kromě požadavky AJAX mezi doménami (spoléhat na specifikace W3C CORS). Je kompatibilní s následujících prohlížečů:

* Microsoft Edge 12+
* Internet Explorer 10+
* Firefox 3.5 +
* Chrome 4 +
* Safari 6+
* Opera 12 +

## <a name="configure-mobile-engagement"></a>Konfigurace Mobile Engagement
Napsat skript, který vytvoří globální konfiguraci `azureEngagement` objekt jazyka JavaScript, jako v následujícím příkladu. Vzhledem k tomu, že váš web může mít násobky stránky, tento příklad předpokládá, že tento skript je zahrnuta v každé stránce. V tomto příkladu je objekt jazyka JavaScript s názvem `azure-engagement-conf.js`.

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
    };

`connectionString` Hodnotu pro vaše aplikace se zobrazí na portálu Azure.

> [!NOTE]
> `appVersionName` a `appVersionCode` jsou volitelné. Doporučujeme však, že jste je nakonfigurovat tak, aby analytics může zpracovat informace o verzi.
> 
> 

## <a name="include-mobile-engagement-scripts-in-your-pages"></a>Zahrnout Mobile Engagement skripty na svých stránkách
Přidáte skripty Mobile Engagement na stránky v jednom z následujících způsobů:

    <head>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </head>

Nebo to:

    <body>
      ...
      <script type="text/javascript" src="azure-engagement-conf.js"></script>
      <script type="text/javascript" src="azure-engagement.js"></script>
      ...
    </body>

## <a name="alias"></a>Alias
Po načtení skriptu SDK webové služby Mobile Engagement, vytvoří **engagement** alias pro přístup k rozhraní API sady SDK. Tento alias nelze použít k definování konfigurace sady SDK. Tento alias se používá jako odkaz v této dokumentaci.

Všimněte si, že pokud výchozí alias je v konfliktu s jinou – globální proměnná ze stránky, můžete upravit ji v konfiguraci takto před načtením Mobile Engagement SDK webové:

    window.azureEngagement = {
      connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
      appVersionName: '1.0.0',
      appVersionCode: 1
      alias:'anotherAlias'
    };

## <a name="basic-reporting"></a>Vytváření základních sestav
Základní vytváření sestav v Mobile Engagementu popisuje statistické údaje, relace úrovni, třeba o uživatelích, relacích, aktivity a dojde k chybě.

### <a name="session-tracking"></a>Sledování relace
Mobile Engagement relace je rozdělené do posloupnost aktivit, identifikována názvem.

V klasického webu doporučujeme je deklarovat jiné aktivitě na každé stránce vašeho webu. Pro web nebo webovou aplikaci, ve kterém se aktuální stránku nikdy změní můžete chtít sledovat aktivity v menším měřítku, jako třeba v rámci dané stránky.

V obou případech spustit nebo změnit aktuální aktivity uživatelů, volání `engagement.agent.startActivity` funkce. Příklad:

    <body onload="yourOnload()">

    <!-- -->

    yourOnload = function() {
      [...]
      engagement.agent.startActivity('welcome');
    };

Mobile Engagement server automaticky ukončí otevřít relaci do tří minut po zavření stránky aplikace.

Alternativně můžete ukončit relaci ručně voláním `engagement.agent.endActivity`. Toto nastaví aktuální aktivity uživatelů na "Nečinnosti."  Relace se ukončí 10 sekund později, pokud nové volání na `engagement.agent.startActivity` obnoví relace.

Prodlevu 10 sekund do objektu globální zapojení, můžete nakonfigurovat následujícím způsobem:

    engagement.sessionTimeout = 2000; // 2 seconds
    // or
    engagement.sessionTimeout = 0; // end the session as soon as endActivity is called

> [!NOTE]
> Nemůžete použít `engagement.agent.endActivity` v `onunload` zpětného volání vzhledem k tomu, že nemůžete provádět volání AJAX v této fázi.
> 
> 

## <a name="advanced-reporting"></a>Rozšířená tvorba sestav
Případně pokud chcete ohlásit události specifické pro aplikace, chyb a úlohy, budete muset použít rozhraní API služby Mobile Engagement. Získat přístup k rozhraní API Mobile Engagement prostřednictvím `engagement.agent` objektu.

Přístup ke všem rozšířené možnosti v Mobile Engagementu v rozhraní API Mobile Engagement. Rozhraní API je podrobně popsaná v článku [jak používat rozšířené Mobile Engagement označování rozhraní API ve webové aplikaci](mobile-engagement-web-use-engagement-api.md).

## <a name="customize-the-urls-used-for-ajax-calls"></a>Přizpůsobení adresy URL použít pro volání AJAX
Můžete přizpůsobit adresy URL, které používá Mobile Engagement Web SDK. Například se znovu definovat adresu URL protokolu (SDK koncový bod pro protokolování), může potlačit konfiguraci takto:

    window.azureEngagement = {
      ...
      urls: {
        ...        
        getLoggerUrl: function() {
        return 'someProxy/log';
        }
      }
    };

Pokud adresa URL funkce vrátí řetězec, který začíná `/`, `//`, `http://`, nebo `https://`, výchozí schéma se nepoužívá. Ve výchozím nastavení `https://` schéma se používá pro tyto adresy URL. Pokud chcete přizpůsobit výchozí schéma, přepište konfiguraci, například takto:

    window.azureEngagement = {
      ...
      urls: {
        ...         
        scheme: '//'
      }
    };
