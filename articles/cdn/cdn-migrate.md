---
title: Migrace profilu Azure CDN z Verizon Standard na Verizon Premium
description: Přečtěte si podrobnosti o migraci profilu z Verizon Standard na Verizon Premium.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: magattus
ms.custom: ''
ms.openlocfilehash: 8ab66117be4f05550b00defafc883108646be283
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083060"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Migrace profilu Azure CDN ze standardu Verizon na Premium Verizon

Když vytvoříte profil sítě pro doručování obsahu Azure (CDN) pro správu koncových bodů, Azure CDN nabízí čtyři různé produkty, ze kterých si můžete vybrat. Informace o různých produktech a jejich dostupných funkcích najdete [v tématu Porovnání funkcí produktu Azure CDN](cdn-features.md).

Pokud jste vytvořili **Azure CDN Standard z** profilu Verizon a používáte jej ke správě koncových bodů CDN, máte možnost upgradovat na Azure CDN Premium z profilu **Verizon.** Při upgradu budou zachovány koncové body CDN a všechna data. 

> [!IMPORTANT]
> Po upgradu na **Azure CDN Premium z** profilu Verizon, nelze později převést zpět na Azure CDN Standard z profilu **Verizon.**
> 

Chcete-li upgradovat **standard Azure CDN standard z** profilu Verizon, obraťte se na podporu společnosti [Microsoft](https://azure.microsoft.com/support/options/).

## <a name="profile-comparison"></a>Porovnání profilů
**Profily Azure CDN Premium od společnosti Verizon** mají následující klíčové rozdíly od azure **cdn standardu od** profilů Verizonu:
- Pro některé funkce Azure CDN, jako je [komprese](cdn-improve-performance.md), [ukládání do mezipaměti pravidla](cdn-caching-rules.md)a [geografické filtrování](cdn-restrict-access-by-country.md), nelze použít rozhraní Azure CDN, musíte použít portál Verizon prostřednictvím tlačítka **Spravovat.**
- ROZHRANÍ API: Na rozdíl od standardu Verizon nelze rozhraní API použít k řízení funkcí, ke kterým se přistupuje z portálu Premium Verizon. Rozhraní API však můžete použít k řízení dalších běžných funkcí, jako je například vytvoření nebo odstranění koncového bodu, vymazání/načítání prostředků v mezipaměti a povolení/zakázání vlastní domény.
- Ceny: Společnost Premium Verizon má pro přenosy dat odlišnou cenovou strukturu než standard Verizon. Další informace naleznete v [tématu Content Delivery Network pricing](https://azure.microsoft.com/pricing/details/cdn/).

**Profily Azure CDN Premium od společnosti Verizon** mají následující další funkce:
- [Ověřování tokenu](cdn-token-auth.md): Umožňuje uživatelům získat a použít token k načtení zabezpečených prostředků.
- [Pravidla motoru](cdn-rules-engine.md): Umožňuje přizpůsobit způsob zpracování požadavků HTTP.
- Pokročilé analytické nástroje:
   - [Podrobná analýza HTTP](cdn-advanced-http-reports.md)
   - [Analýza výkonu na hraničních zařízeních](cdn-edge-performance.md)
   - [Analytika v reálném čase](cdn-real-time-alerts.md)


## <a name="next-steps"></a>Další kroky
Další informace o modulu pravidel najdete v [tématu Azure CDN pravidla motoru odkaz](cdn-rules-engine-reference.md).

