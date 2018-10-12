---
title: Migrovat profil Azure CDN od společnosti Verizon Standard na úroveň Verizon Premium | Dokumentace Microsoftu
description: Další informace o podrobnosti migrace profilu z Verizon úrovně Standard na úroveň Verizon úrovně Premium.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: 7768dde424aedc295b53512db50c9dfc9db9ab8c
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091466"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Migrace profil Azure CDN od společnosti Verizon Standard do Premium Verizon

Když vytvoříte profil Azure Content Delivery Network (CDN) ke správě vašich koncových bodů, nabízí Azure CDN můžete zvolit ze čtyř různých produktů. Informace o různých produktů a jejich funkce. k dispozici, najdete v tématu [funkce produktu Azure CDN porovnání](cdn-features.md).

Pokud jste vytvořili **Azure CDN Standard od Verizonu** profilu a používáte ke správě koncových bodů CDN, budete mít možnost upgradovat na **Azure CDN Premium od Verizonu** profilu. Když upgradujete, koncové body CDN a všechna vaše data se zachovají. 

> [!IMPORTANT]
> Jakmile jste upgradovali na **Azure CDN Premium od Verizonu** profilu, můžete později ji nemůže převést zpět do **Azure CDN Standard od Verizonu** profilu.
> 

Upgrade **Azure CDN Standard od Verizonu** profilu, kontaktujte [Microsoft Support](https://azure.microsoft.com/support/options/).

## <a name="profile-comparison"></a>Porovnání profilu
**Azure CDN Premium od Verizonu** profily mají následující klíčové rozdíly proti **Azure CDN Standard od Verizonu** profily:
- Pro některé funkce Azure CDN, jako [komprese](cdn-improve-performance.md), [pravidla ukládání do mezipaměti](cdn-caching-rules.md), a [geografického filtrování](cdn-restrict-access-by-country.md), nemůžete použít rozhraní Azure CDN, je nutné použít portál společnosti Verizon prostřednictvím **Spravovat** tlačítko.
- Rozhraní API: Na rozdíl od s Verizon úrovně Standard, nelze použít rozhraní API k řízení těchto funkcí, které jsou přístupné z portálu společnosti Verizon úrovně Premium. Můžete však použít rozhraní API provádět další běžné funkce, jako je například vytvoření nebo odstranění koncového bodu, odstraňování/načítání prostředků z mezipaměti a povolení nebo zákaz vlastní doménu.
- Ceny: Premium Verizon má jinou cenovou strukturu pro přenosy dat než Verizon úrovně Standard. Další informace najdete v tématu [ceny Content Delivery Network](https://azure.microsoft.com/pricing/details/cdn/).

**Azure CDN Premium od Verizonu** profily mají následující funkce:
- [Ověřování pomocí tokenu](cdn-token-auth.md): umožňuje uživatelům získat a použít token k načtení zabezpečeným prostředkům.
- [Stroj pravidel](cdn-rules-engine.md): umožňuje přizpůsobit zpracování požadavků HTTP.
- Pokročilé analytické nástroje:
   - [Podrobné analýzy protokolu HTTP](cdn-advanced-http-reports.md)
   - [Analýza výkonu hranice](cdn-edge-performance.md)
   - [Analýzy v reálném čase](cdn-real-time-alerts.md)


## <a name="next-steps"></a>Další postup
Další informace o stroj pravidel najdete v tématu [referenční informace ke stroji pravidel Azure CDN](cdn-rules-engine-reference.md).

