---
title: Windows Phone Silverlight SDK postupy upgradu
description: Windows Phone Silverlight SDK postupy upgradu pro Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 87130026-9759-4659-9184-788a3627a165
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 489b005c37ddb842a2501e89c07fb34b091346e5
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="windows-phone-silverlight-sdk-upgrade-procedures"></a>Windows Phone Silverlight SDK postupy upgradu
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

Pokud již máte integrovanou starší verze naše sady SDK do své aplikace, je nutné zvážit následující body při upgradu sady SDK.

Možná budete muset několik postupy použijte, pokud provedena několik verzí sady SDK. Například pokud migrujete z 0.10.1 0.11.0 budete muset nejdřív postupujte podle pokynů "od 0.9.0 k 0.10.1" pak postupu "od 0.10.1 k 0.11.0".

## <a name="from-200-to-330"></a>Z 2.0.0 k 3.3.0
### <a name="test-logs"></a>Protokolů testování
Protokoly konzoly vyprodukované sady SDK teď může být povolena nebo zakázána nebo filtrovat. Chcete-li přizpůsobit tím, aktualizujte vlastnost `EngagementAgent.Instance.TestLogEnabled` na jednu z hodnota dostupná z `EngagementTestLogLevel` výčtu pro instanci:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

## <a name="from-111-to-200"></a>Z 1.1.1 k 2.0.0
Následující část popisuje postup migrace integraci sady SDK z Capptain služby, které do aplikace používá technologii Azure Mobile Engagement nabízí Capptain SAS. 

> [!IMPORTANT]
> Capptain a Mobile Engagement nejsou stejné služby a postup níže uvedené jenom dozvíte, jak migrovat klientské aplikace. Migrace sady SDK v aplikaci není migrovat data ze serverů Capptain na servery Mobile Engagement
> 
> 

Pokud provádíte migraci ze starší verze, najdete na webu společnosti Capptain nejdřív přenést 1.1.1 pak použije následující postup

### <a name="nuget-package"></a>Balíček Nuget
Nahraďte **Capptain.WindowsPhone** podle **MicrosoftAzure.MobileEngagement** balíček Nuget.

### <a name="applying-mobile-engagement"></a>Použití Mobile Engagement
Sada SDK používá termín `Engagement`. Je potřeba aktualizovat projekt tak, aby odpovídaly tuto změnu.

Je potřeba odinstalovat váš aktuální Capptain balíček nuget. Zvažte, se odeberou všechny změny ve složce Capptain prostředky. Pokud chcete, aby tyto soubory pak proveďte jejich kopii.

Potom nainstalujte nový balíček nuget Microsoft Azure Engagement na projektu. Můžete najít přímo na [Nuget](http://www.nuget.org/packages/MicrosoftAzure.MobileEngagement). Tato akce nahradí všechny soubory prostředky používané Engagement a přidá nová knihovna DLL Engagement odkazy projektu.

Je nutné vyčistit odkazy projektu odstraněním Capptain DLL odkazy. Pokud to neuděláte, budou v konfliktu verze Capptain a dojde k chybám.

Pokud jste upravili Capptain prostředky, zkopírujte původní soubory obsahu a vložit do nové soubory zapojení. Upozorňujeme, že soubory xaml a cs muset aktualizovat.

Po dokončení těchto kroků stačí nahraďte staré odkazy Capptain pomocí nové odkazy zapojení.

1. Všechny obory názvů Capptain muset aktualizovat.
   
    Před migrací:
   
        using Capptain.Agent;
        using Capptain.Reach;
   
    Po dokončení migrace:
   
        using Microsoft.Azure.Engagement;
2. Všechny třídy Capptain, které obsahují "Capptain" by měly obsahovat "Engagement".
   
    Před migrací:
   
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
   
    Po dokončení migrace:
   
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }
3. Pro soubory xaml Capptain obor názvů a atributy také změnit.
   
    Před migrací:
   
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
   
    Po dokončení migrace:
   
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>
4. Pro jiné prostředky jako Capptain obrázky Upozorňujeme, že se také přejmenovaná používat "Engagement".

### <a name="application-id--sdk-key"></a>ID aplikace nebo klíč SDK
Zapojení používá připojovací řetězec. Nemáte a zadejte ID aplikace a klíč SDK s Mobile Engagement, stačí zadat připojovací řetězec. Můžete ho nastavit tak v souboru EngagementConfiguration.

Konfigurace Engagement může být nastavena v vaší `Resources\EngagementConfiguration.xml` souboru projektu.

Upravte tento soubor k určení:

* Připojovací řetězec aplikací mezi značky `<connectionString>` a `<\connectionString>`.

Pokud chcete zadat za běhu, můžete volat metodu před Engagement inicializaci agenta:

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Initialize Engagement angent with above configuration. */
        EngagementAgent.Instance.Init(engagementConfiguration);

Připojovací řetězec pro vaši aplikaci se zobrazí na portálu Azure.

### <a name="items-name-change"></a>Změna názvu položky
Všechny položky s názvem *capptain* byly pojmenovány *engagement*. Podobně jako pro *Capptain* k *Engagement*.

Příklady běžně používané Capptain položek:

* CapptainConfiguration nyní s názvem EngagementConfiguration
* Nyní s názvem EngagementAgent CapptainAgent
* Nyní s názvem EngagementReach CapptainReach
* Nyní s názvem EngagementHttpConfig CapptainHttpConfig
* Nyní s názvem GetEngagementPageName GetCapptainPageName

Všimněte si, že přejmenovat také ovlivňuje přepsat metody.

