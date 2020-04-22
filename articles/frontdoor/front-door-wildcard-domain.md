---
title: Azure Front Door – podpora domén se zástupnými symboly
description: Tento článek vám pomůže pochopit, jak Azure Front Door podporuje mapování a správu domén se zástupnými symboly v seznamu vlastních domén.
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2020
ms.author: sharadag
ms.openlocfilehash: 6d8a6d6f0b05b9b7fd0144959c82b6a2c9e659a3
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768318"
---
# <a name="wildcard-domains"></a>Domény se zástupnými symboly

Kromě domén a subdomén apex můžete namapovat název domény se zástupnými značkami na seznam front-endových hostitelů nebo vlastních domén v profilu Azure Front Door. S doménami se zástupnými kódy v konfiguraci Azure Front Door zjednodušuje směrování provozu chování pro více subdomén pro rozhraní API, aplikace nebo webu ze stejného pravidla směrování. Není nutné upravovat konfiguraci přidat nebo zadat každou subdoménu samostatně. Jako příklad můžete definovat směrování `customer1.contoso.com`pro `customer2.contoso.com`, `customerN.contoso.com` a pomocí stejného pravidla směrování `*.contoso.com`a přidáním domény se zástupnými kódy .

Mezi klíčové scénáře, které jsou vylepšeny s podporou domén se zástupnými kódy, patří:

- Nemusíte založit každou subdoménu v profilu Azure Front Door a pak povolit protokol HTTPS pro vazbu certifikátu pro každou subdoménu.
- Už nemusíte měnit produkční konfiguraci Azure Front Door, pokud aplikace přidá novou subdoménu. Dříve bylo nutné přidat subdoménu, svázat s ní certifikát, připojit zásady brány firewall webové aplikace (WAF) a poté přidat doménu do různých pravidel směrování.

> [!NOTE]
> V současné době jsou domény se zástupnými kódy podporovány jenom prostřednictvím rozhraní API, prostředí PowerShell a rozhraní příkazového příkazového příkazového příkazu k řešení Azure. Podpora pro přidávání a správu domén se zástupnými symboly na webu Azure Portal není dostupná.

## <a name="adding-wildcard-domains"></a>Přidání domén se zástupnými symboly

Do oddílu pro front-endové hostitele nebo domény můžete přidat doménu se zástupnými symboly. Podobně jako subdomény Azure Front Door ověří, že pro doménu se zástupnými kartami existuje mapování záznamů CNAME. Toto mapování DNS může být přímým `*.contoso.com` mapováním `contoso.azurefd.net`záznamů CNAME, jako je mapování na . Nebo můžete použít afdverify dočasné mapování. Například `afdverify.contoso.com` mapována `afdverify.contoso.azurefd.net` na ověření mapy záznamů CNAME pro zástupný znak.

> [!NOTE]
> Azure DNS podporuje záznamy se zástupným znakem.

Můžete přidat tolik jednoúrovňových subdomén domény se zástupnými symboly v front-endových hostitelích až do limitu front-endových hostitelů. Tato funkce může být vyžadována pro:

- Definování jiné trasy pro subdoménu než ostatní domény (z domény se zástupnými symboly).

- S jinou zásadou WAF pro konkrétní subdoménu. Například `*.contoso.com` umožňuje `foo.contoso.com` přidání bez nutnosti znovu prokázat vlastnictví domény. Ale to neumožňuje, `foo.bar.contoso.com` protože to není jedna úroveň `*.contoso.com`subdomény . Chcete-li přidat `foo.bar.contoso.com` bez `*.bar.contosonews.com` dalšího ověření vlastnictví domény, je třeba přidat.

S určitými omezeními můžete přidat domény se zástupnými symboly a jejich subdomény:

- Pokud je do profilu Azure Front Door přidána doména se zástupnými symboly:
  - Doménu se zástupnými symboly nelze přidat do žádného jiného profilu Azure Front Door.
  - Subdomény první úrovně domény se zástupnými symboly nelze přidat do jiného profilu Azure Front Door nebo profilu sítě doručování obsahu Azure.
- Pokud se subdoména domény se zástupnými symboly přidá do profilu Azure Front Door nebo profilu sítě doručování obsahu Azure, pak doménu se zástupnými kartami nelze přidat do jiných profilů Azure Front Door.
- Pokud dva profily (Azure Front Door nebo Azure Content Delivery Network) mají různé subdomény kořenové domény, pak domény se zástupnými symboly nelze přidat do některého z profilů.

## <a name="certificate-binding"></a>Vazba certifikátu

Pro přijetí přenosu HTTPS v doméně se zástupnými symboly je nutné povolit protokol HTTPS v doméně se zástupnými symboly. Vazba certifikátu pro doménu se zástupnými symboly vyžaduje certifikát se zástupnými symboly. To znamená, že název předmětu certifikátu by měl mít také doménu se zástupnými symboly.

> [!NOTE]
> V současné době pouze pomocí vlastní možnost ssl certifikát je k dispozici pro povolení HTTPS pro domény se zástupnými kódy. Spravované certifikáty Azure Front Door nelze použít pro domény se zástupnými symboly.

Můžete použít stejný certifikát se zástupnými kódy z Azure Key Vault nebo z certifikátů spravovaných Azure Front Door pro subdomény.

Pokud je subdoména přidána pro doménu se zástupnými symboly, ke které je již přidružen certifikát, nelze protokol HTTPS pro subdoménu zakázat. Subdoména používá vazbu certifikátu pro doménu se zástupnými symboly, pokud ji nepřepíše jiný trezor klíčů nebo spravovaný certifikát Azure Front Door.

## <a name="waf-policies"></a>Zásady WAF

Zásady WAF lze připojit k doménám se zástupnými symboly, podobně jako jiné domény. Pro subdoménu domény se zástupnými symboly lze použít jinou zásadu WAF. Pro subdomény je nutné zadat zásadu WAF, která se má použít i v případě, že se jedná o stejnou zásadu jako doména se zástupnými symboly. Subdomény *automaticky nedědí* zásadu WAF z domény se zástupnými symboly.

Pokud nechcete, aby zásady WAF spouštěny pro subdoménu, můžete vytvořit prázdnou zásadu WAF bez spravovaných nebo vlastních pravidel.

## <a name="routing-rules"></a>Pravidla směrování

Při konfiguraci pravidla směrování můžete jako front-endového hostitele vybrat doménu se zástupnými kódy. Můžete také mít různé chování trasy pro domény se zástupnými symboly a subdomény. Jak je popsáno v části [Jak Azure Front Door provádí porovnávání tras](front-door-route-matching.md), nejkonkrétnější shoda pro doménu mezi různými pravidly směrování je vybrána za běhu.

> [!IMPORTANT]
> Musíte mít odpovídající cesty vzory přes pravidla směrování, nebo vaši klienti uvidí selhání. Například máte dvě pravidla směrování,`*.foo.com/*` jako je trasa 1 (mapováno`bar.foo.com/somePath/*` do back-end ového fondu A) a Route 2 (mapováno do back-endového fondu B). Potom přijde požadavek `bar.foo.com/anotherPath/*`pro . Azure Front Door vybere route 2 na základě konkrétnější shody domény, jen najít žádné odpovídající cesty vzory přes trasy.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit profil Azure Front Door](quickstart-create-front-door.md).
- Přečtěte si, jak [přidat vlastní doménu na Azure Front Door](front-door-custom-domain.md).
- Přečtěte si, jak [povolit protokol HTTPS ve vlastní doméně](front-door-custom-domain-https.md).
