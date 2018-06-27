---
title: Migrovat profilem Azure CDN z Verizon Standard Verizon Premium | Microsoft Docs
description: Další informace o podrobnosti o migraci profil z Verizon Standard Verizon Premium.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: v-deasim
ms.custom: ''
ms.openlocfilehash: 074dbb094e7ae2cd1f1719016928bd7348da3949
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2018
ms.locfileid: "36958050"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Migrace profilem Azure CDN společnosti Standard Verizon do Premium Verizon

Když vytvoříte profil aplikace Azure Content Delivery Network (CDN) ke správě koncových bodů, Azure CDN nabízí čtyři různé produkty můžete vybírat. Informace o různých produktů a jejich funkce. k dispozici, najdete v článku [funkce produktu porovnat Azure CDN](cdn-features.md).

Pokud jste vytvoříte **Azure CDN Standard od společnosti Verizon** profilu a používáte ke správě koncové body CDN, máte možnost ho upgradovat na **Azure CDN Premium od společnosti Verizon** profilu. Při upgradu, koncové body CDN a všechna vaše data se zachovají. 

> [!IMPORTANT]
> Jakmile jste jste upgradovali na **Azure CDN Premium od společnosti Verizon** profilu, není možné později ho převést zpět **Azure CDN Standard od společnosti Verizon** profilu.
> 

K upgradu **Azure CDN Standard od společnosti Verizon** profilu, kontaktujte [Microsoft Support](https://azure.microsoft.com/support/options/).

## <a name="profile-comparison"></a>Porovnání profilu
**Azure CDN Premium od společnosti Verizon** profily mají následující klíčové rozdíly proti **Azure CDN Standard od společnosti Verizon** profily:
- Pro určité funkce Azure CDN, jako [komprese](cdn-improve-performance.md), [ukládání do mezipaměti pravidla](cdn-caching-rules.md), a [geograficky filtrování](cdn-restrict-access-by-country.md), nemůžete použít rozhraní Azure CDN, musíte použít portálu Verizon prostřednictvím **Spravovat** tlačítko.
- Rozhraní API: Na rozdíl od s Standard Verizon, nemůžete použít rozhraní API k ovládání těchto funkcí, které jsou přístupné z portálu Premium Verizon. Můžete však použít rozhraní API pro řízení další běžné funkce, například vytvoření nebo odstranění koncový bod, vyprazdňování/načítání prostředků v mezipaměti a povolení nebo zákaz vlastní doménu.
- Ceny: Premium Verizon má jinou cenovou strukturu pro přenosy dat než Standard Verizon. Další informace najdete v tématu [Content Delivery Network ceny](https://azure.microsoft.com/pricing/details/cdn/).

**Azure CDN Premium od společnosti Verizon** profily mají následující funkce:
- [Ověření pomocí tokenu](cdn-token-auth.md): umožňuje uživatelům získat a použít token načíst zabezpečeným prostředkům.
- [Stroj pravidel](cdn-rules-engine.md): umožňuje přizpůsobit způsob zpracování požadavků HTTP.
- Nástroje pro pokročilou analýzu:
   - [Podrobné analýzy HTTP](cdn-advanced-http-reports.md)
   - [Analýza výkonu Edge](cdn-edge-performance.md)
   - [Analýzy v reálném čase](cdn-real-time-alerts.md)


## <a name="next-steps"></a>Další postup
Další informace o stroj pravidel najdete v tématu [odkaz na modul Azure CDN pravidla](cdn-rules-engine-reference.md).

