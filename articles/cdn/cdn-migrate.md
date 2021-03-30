---
title: Migrace profilu Azure CDN z Verizon úrovně Standard na Verizon Premium
description: Přečtěte si o podrobnostech migrace profilu z Verizon Standard na Verizon Premium.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/21/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: e58c1177fb3c4d241a3efe1759b3900abbd04ca1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92778389"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Migrace profilu Azure CDN ze standardu Verizon na Premium Verizon

Když vytvoříte profil Azure Content Delivery Network (CDN) pro správu koncových bodů, Azure CDN nabízí čtyři různé produkty, ze kterých si můžete vybrat. Informace o různých produktech a jejich dostupných funkcích najdete v tématu [porovnání Azure CDNch funkcí produktu](cdn-features.md).

Pokud jste vytvořili **Azure CDN Standard z profilu Verizon** a používáte ho ke správě koncových bodů CDN, máte možnost upgradovat ho na **Azure CDN Premium z profilu Verizon** . Při upgradu se zachovají koncové body CDN a všechna vaše data. 

> [!IMPORTANT]
> Jakmile provedete upgrade na **Azure CDN Premium z profilu Verizon** , nemůžete ho později převést zpět na **Azure CDN Standard z profilu Verizon** .
> 

Pokud chcete upgradovat **Azure CDN Standard z profilu Verizon** , obraťte se na [Podpora Microsoftu](https://azure.microsoft.com/support/options/).

## <a name="profile-comparison"></a>Porovnání profilů
**Azure CDN Premium z profilů Verizon** mají následující klíčové rozdíly od **Azure CDN Standard od profilů Verizon** :
- Pro určité Azure CDN funkce, jako je [Komprese](cdn-improve-performance.md), [pravidla ukládání do mezipaměti](cdn-caching-rules.md)a [geografické filtrování](cdn-restrict-access-by-country.md), nemůžete použít rozhraní Azure CDN, musíte použít portál Verizon prostřednictvím tlačítka **Spravovat** .
- Rozhraní API: na rozdíl od standardních Verizon nemůžete použít rozhraní API k řízení těchto funkcí, ke kterým se dostanete z portálu Premium Verizon. Rozhraní API ale můžete použít k řízení dalších běžných funkcí, jako je například vytvoření nebo odstranění koncového bodu, vyprazdňování a načtení prostředků uložených v mezipaměti a povolení nebo zakázání vlastní domény.
- Ceny: Premium Verizon má odlišnou cenovou strukturu pro přenosy dat než standardní Verizon. Další informace najdete v tématu [Content Delivery Network ceny](https://azure.microsoft.com/pricing/details/cdn/).

**Azure CDN Premium z profilů Verizon** mají tyto další funkce:
- [Ověřování tokenu](cdn-token-auth.md): umožňuje uživatelům získat a použít token k načtení zabezpečených prostředků.
- [Modul pravidel](./cdn-verizon-premium-rules-engine.md): umožňuje přizpůsobit způsob zpracování požadavků HTTP.
- Nástroje pro pokročilou analýzu:
   - [Podrobné analýzy HTTP](cdn-advanced-http-reports.md)
   - [Analýza výkonu Edge](cdn-edge-performance.md)
   - [Analýza v reálném čase](cdn-real-time-alerts.md)


## <a name="next-steps"></a>Další kroky
Další informace o modulu pravidel najdete v tématu [Referenční dokumentace modulu Azure CDN Rules](./cdn-verizon-premium-rules-engine-reference.md).