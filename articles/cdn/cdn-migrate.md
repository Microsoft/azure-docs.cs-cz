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
ms.openlocfilehash: 615f35c602f9a086bdc5c32b04a97efa368411a3
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36308121"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Migrace profilem Azure CDN společnosti Standard Verizon do Premium Verizon

Azure Content Delivery Network (CDN) nabízí čtyři různých produktů, z nichž každý nabízí jiné funkce: 
- **Azure CDN Standard od společnosti Microsoft**
- **Standardní Azure CDN společnosti Akamai**
- **Azure CDN Standard od společnosti Verizon**
- **Azure CDN Premium od společnosti Verizon**.

Pokud používáte **Azure CDN Standard od společnosti Verizon** profilu spravovat koncové body CDN, máte možnost ho upgradovat na **Azure CDN Premium od společnosti Verizon** profilu a zároveň zachovat všechna vaše data.

K upgradu **Azure CDN Standard od společnosti Verizon** profilu, kontaktujte [Microsoft Support](https://azure.microsoft.com/support/options/).

> [!IMPORTANT]
> Jakmile jste jste upgradovali na **Azure CDN Premium od společnosti Verizon** profilu, není možné později ho převést zpět **Azure CDN Standard od společnosti Verizon** profilu.
> 

## <a name="profile-comparison"></a>Porovnání profilu
**Azure CDN Premium od společnosti Verizon** profily mají následující klíčové rozdíly proti **Azure CDN Standard od společnosti Verizon** profily:
- Pro určité funkce Azure CDN, jako [komprese](cdn-improve-performance.md), pravidla pro ukládání do mezipaměti a [geograficky filtrování](cdn-restrict-access-by-country.md), nemůžete použít rozhraní Azure CDN, musíte použít portálu Verizon prostřednictvím **spravovat**tlačítko.
- Rozhraní API: Na rozdíl od Standard Verizon, nemůžete použít rozhraní API k ovládání těchto funkcí, které jsou přístupné z portálu Premium Verizon. Můžete však použít rozhraní API pro řízení další běžné funkce, například vytvoření nebo odstranění koncový bod, vyprazdňování/načítání prostředků v mezipaměti a povolení nebo zákaz vlastní doménu.
- Ceny: Premium Verizon má jinou cenovou strukturu pro přenosy dat než Standard Verizon. Další informace najdete v tématu [Content Delivery Network ceny](https://azure.microsoft.com/pricing/details/cdn/).

**Azure CDN Premium od společnosti Verizon** profily mají následující funkce:
- [Ověření pomocí tokenu](cdn-token-auth.md): umožňuje uživatelům získat a použít token načíst zabezpečeným prostředkům.
- [Stroj pravidel](cdn-rules-engine.md): umožňuje přizpůsobit způsob zpracování požadavků HTTP.
- Nástroje pro pokročilou analýzu:
   - [Podrobné analýzy HTTP](cdn-advanced-http-reports.md)
   - [Analýza výkonu Edge](cdn-edge-performance.md)
   - [Analýzy v reálném čase](cdn-real-time-alerts.md)


## <a name="next-steps"></a>Další postup
Podrobné porovnání funkcí produktu Azure CDN najdete v tématu [funkce Azure CDN produktu](Compare Azure CDN product features).

Další informace o stroj pravidel najdete v tématu [odkaz na modul Azure CDN pravidla](cdn-rules-engine-reference.md).

