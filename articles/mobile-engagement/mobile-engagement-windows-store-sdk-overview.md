---
title: Integraci sady Azure Mobile Engagement Windows Universal SDK | Microsoft Docs
description: Windows Universal integraci sady SDK pro Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 9ded187d-5c07-4377-a41c-ce205dd38b50
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 11/03/2016
ms.author: piyushjo
ms.openlocfilehash: 8c513d5b05afaee4c49d0d2e96a0dcda78761421
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="windows-universal-sdk-integration-for-azure-mobile-engagement"></a>Integrace Windows Universal SDK pro Azure Mobile Engagement
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

Tento dokument popisuje všech integrace a konfigurace dostupných možnostech pro Azure Mobile Engagement Windows Universal SDK.

## <a name="prerequisites"></a>Požadavky
Před zahájením tohoto kurzu, musíte nejdřív dokončit naše [15 minut kurzu](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="advanced-features"></a>Pokročilé funkce
### <a name="reporting-features"></a>Funkce vytváření sestav
Můžete přidat tyto funkce:

1. [Rozšířené možnosti vytváření sestav](mobile-engagement-windows-store-advanced-reporting.md)
2. [Upřesnit možnosti konfigurace](mobile-engagement-windows-store-advanced-configuration.md)

### <a name="notifications"></a>Oznámení
[Postup při integraci Reach (oznámení) v aplikaci univerzální pro Windows](mobile-engagement-windows-store-integrate-engagement-reach.md)

### <a name="tag-plan-implementation"></a>Implementace plánu značky:
[Jak používat rozšířené Mobile Engagement označování rozhraní API v aplikaci univerzální pro Windows](mobile-engagement-windows-store-use-engagement-api.md)

## <a name="release-notes"></a>Poznámky k verzi
### <a name="341-11032016"></a>3.4.1 (11/03/2016)

* Zlepšení stability.

Starší verze najdete v tématu [dokončení poznámky k verzi](mobile-engagement-windows-store-release-notes.md)

## <a name="upgrade-procedures"></a>Postupy upgradu
Pokud již jste spojili starší verze zapojení do své aplikace, je nutné zvážit následující body při upgradu sady SDK.

Pokud je provedena několik verzí sady SDK, budete muset několik postupu. Zobrazit úplnou [postupy upgradu](mobile-engagement-windows-store-upgrade-procedure.md). Například pokud migrujete z 0.10.1 0.11.0 budete muset nejdřív postupujte podle pokynů "od 0.9.0 k 0.10.1" pak postupu "od 0.10.1 k 0.11.0".

### <a name="from-330-to-340"></a>Z 3.3.0 k 3.4.0
#### <a name="test-logs"></a>Protokolů testování
Protokoly konzoly vyprodukované sady SDK teď může být povolena nebo zakázána nebo filtrovat. Pokud chcete přizpůsobit, aktualizujte vlastnost `EngagementAgent.Instance.TestLogEnabled` na jednu z hodnot, které jsou k dispozici z `EngagementTestLogLevel` výčtu, například:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

#### <a name="resources"></a>Zdroje a prostředky
Bylo vylepšeno Reach překrytí. Je součástí zdroje balíčku NuGet sady SDK.

Při upgradu na novou verzi sady SDK, můžete zvolit, zda chcete zachovat existující soubory ve složce překrytí vašich prostředků, nebo není:

* Pokud předchozí překrytí pracuje pro vás, nebo jsou integrací `WebView` elementy ručně, pak se můžete rozhodnout zachovat vaše ukončení soubory, ji budou i nadále fungovat.
* Pokud chcete aktualizovat nové překrytí, nahradit všechny `overlay` složku z vašich prostředků s novým z balíčku SDK (aplikace UWP: Po dokončení upgradu, můžete získat novou složku překrytí % USERPROFILE %\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [!WARNING]
> Pomocí nové překrytí přepíše všechny úpravy probíhají v předchozí verzi.
> 
> 

### <a name="upgrade-from-older-versions"></a>Upgrade ze starších verzí
V tématu [Upgrade procedury](mobile-engagement-windows-store-upgrade-procedure.md)

