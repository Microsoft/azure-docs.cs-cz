---
title: Pokročilé vytváření sestav s MobileApps Engagement univerzální pro Windows
description: Postup pro integraci Azure Mobile Engagement univerzálních aplikací pro Windows
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: ea5030bf-73ac-49b7-bc3e-c25fc10e945a
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 88d28bbc32179698147ab7516cc414fd23ed4bd6
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="advanced-reporting-with-the-windows-universal-apps-engagement-sdk"></a>Pokročilé vytváření sestav s Engagement univerzálních aplikací pro Windows SDK
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

> [!div class="op_single_selector"]
> * [Univerzální platforma Windows](mobile-engagement-windows-store-advanced-reporting.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-reporting.md)
> 
> 

Toto téma popisuje další scénáře vytváření sestav v aplikaci univerzální pro Windows. Mezi tyto scénáře patří možnosti, které můžete použít pro vytvořené v aplikaci [Začínáme](mobile-engagement-windows-store-dotnet-get-started.md) kurzu.

## <a name="prerequisites"></a>Požadavky
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

Před zahájením tohoto kurzu, je třeba nejprve provést [Začínáme](mobile-engagement-windows-store-dotnet-get-started.md) kurz, který je úmyslně přímé a jednoduché. Tento kurz se zaměřuje na další možnosti, které můžete vybrat z.

## <a name="specifying-engagement-configuration-at-runtime"></a>Určení konfigurace engagement za běhu
Konfigurace zapojení je centralizovaná v `Resources\EngagementConfiguration.xml` souboru projektu, který je tam, kde byl zadán v [Začínáme](mobile-engagement-windows-store-dotnet-get-started.md) tématu.

Ale můžete je také zadat v době běhu: můžete volat metodu před Engagement inicializaci agenta:

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);



## <a name="recommended-method-overload-your-page-classes"></a>Doporučená metoda: přetížení vaší `Page` třídy
Pokud chcete aktivovat reporting všechny protokoly, které vyžadují zapojení k výpočtu uživatelů, relací, aktivity, dojde k chybě a technické statistiky, zkontrolujte všechny vaše `Page` dílčí třídy dědí `EngagementPage` třídy.

Tady je příklad pro stránku vaší aplikace. Můžete to samé pro všechny stránky vaší aplikace.

### <a name="c-source-file"></a>C# zdrojový soubor
Upravit stránku `.xaml.cs` souboru:

* Přidat do vaší `using` příkazy:
  
      using Microsoft.Azure.Engagement;
* Nahraďte `Page` s `EngagementPage`:

**Bez Engagement:**

        namespace Example
        {
          public sealed partial class ExamplePage : Page
          {
            [...]
          }
        }

**S Engagement:**

        using Microsoft.Azure.Engagement;

        namespace Example
        {
          public sealed partial class ExamplePage : EngagementPage
          {
            [...]
          }
        }

> [!IMPORTANT]
> Pokud stránka přepíše metodu `OnNavigatedTo`, nezapomeňte volat `base.OnNavigatedTo(e)`. Jinak, není hlášena aktivity ( `EngagementPage` volání `StartActivity` uvnitř jeho `OnNavigatedTo` metoda).
> 
> 

### <a name="xaml-file"></a>Souboru XAML
Upravit stránku `.xaml` souboru:

* Přidejte do deklarací oborů názvů:
  
      xmlns:engagement="using:Microsoft.Azure.Engagement"
* Nahraďte `Page` s `engagement:EngagementPage`:

**Bez Engagement:**

        <Page>
            <!-- layout -->
            ...
        </Page>

**S Engagement:**

        <engagement:EngagementPage
            xmlns:engagement="using:Microsoft.Azure.Engagement">
            <!-- layout -->
            ...
        </engagement:EngagementPage >

### <a name="override-the-default-behaviour"></a>Přepsat výchozí chování
Ve výchozím nastavení je název třídy stránky uvedená jako název aktivity, s žádné další. Pokud třída používá příponou "Stránka", Engagement jeho odstranění.

Pokud chcete přepsat výchozí chování pro název, přidejte tento kód:

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

Sestavy doplňující informace s vaší aktivitou, přidejte tento kód:

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
          /* your code */
          return extra;
        }

Tyto metody jsou volány prostřednictvím `OnNavigatedTo` metoda stránky.

### <a name="alternate-method-call-startactivity-manually"></a>Alternativní metoda: volání `StartActivity()` ručně
Pokud nemůžete nebo nechcete přetížení vaše `Page` třídy, můžete místo toho spustit vaše aktivity voláním `EngagementAgent` metody přímo.

Doporučujeme, abyste volání `StartActivity` uvnitř vaší `OnNavigatedTo` metoda stránky.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [!IMPORTANT]
> Zajistěte, aby že správně ukončete svou relaci.
> 
> Sady Windows Universal SDK automaticky zavolá `EndActivity` metoda při ukončení aplikace. Z toho důvodu je **vysoce** doporučuje volat `StartActivity` metoda vždy, když aktivita uživatele změnit a **nikdy** volání `EndActivity` metoda. Tato metoda Engagement server oznámení, že má aktuální uživatel opustil aplikaci, které ovlivní všechny protokoly aplikací.
> 
> 

## <a name="advanced-reporting"></a>Rozšířená tvorba sestav
Volitelně můžete chtít sestavu, události specifické pro aplikace, chyb a úlohy, Uděláte to tak, použít jiné metody v nalezen `EngagementAgent` třídy. Rozhraní API Engagement umožňuje použití pokročilých funkcí pro všechny zapojení.

Další informace najdete v tématu [jak používat rozšířené Mobile Engagement označování rozhraní API v aplikaci Windows Universal](mobile-engagement-windows-store-use-engagement-api.md).

