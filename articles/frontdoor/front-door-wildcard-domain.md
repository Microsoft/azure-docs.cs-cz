---
title: Azure Front Door – podpora domén se zástupnými symboly
description: Tento článek vám pomůže pochopit, jak Azure Front Door podporuje mapování a správu domén se zástupnými kartami v seznamu vlastních domén
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2020
ms.author: sharadag
ms.openlocfilehash: c568c9cc5c57098385cc7399459ec656cdbfc305
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537437"
---
# <a name="wildcard-domains"></a>Domény se zástupnými symboly

Kromě domén a subdomén apex můžete také namapovat název domény se zástupnými symboly do seznamu hostitelů front-endu nebo vlastních domén profilu Front Door. S doménami se zástupnými kódy v konfiguraci front door zjednodušuje směrování provozu pro více subdomén pro rozhraní API, aplikace nebo web ze stejného pravidla směrování bez nutnosti měnit konfiguraci tak, aby přidávala nebo zadávala každou subdoménu zvlášť. Jako příklad můžete definovat směrování `customer1.contoso.com`pro `customer2.contoso.com`, `customerN.contoso.com` a pomocí stejného pravidla směrování `*.contoso.com`přidáním domény se zástupnými kódy .

Některé z klíčových scénářů, které jsou vyřešeny s podporou domén se zástupnými kódy, zahrnují:

- Již není nutné založit každou subdoménu na předních dveřích a poté povolit protokolu HTTPS vázat certifikát pro každou subdoménu.
- Pokud aplikace přidá novou subdoménu, pak již není nutné změnit konfiguraci produkčních dveří. Jinak dříve vyžadovalo přidání subdomény, vázání certifikátu, připojení zásad brány firewall webové aplikace (WAF) a přidání domény do různých pravidel směrování.

> [!NOTE]
> V současné době jsou domény se zástupnými kódy podporovány pouze prostřednictvím rozhraní API, prostředí PowerShell a CLI. Podpora pro přidávání domén se zástupnými symboly přes portál Azure portal není k dispozici.

## <a name="adding-wildcard-domains"></a>Přidání domén se zástupnými symboly

Můžete připojit doménu se zástupnými symboly v části Frontend Hosts nebo Domains. Podobně jako subdomény, Front Door ověří, že je mapování CNAME pro doménu se zástupnými symboly také. Toto mapování DNS může být `*.contoso.com` přímé mapování `contoso.azurefd.net` CNAME, jako je `afdverify.contoso.com` mapováno `afdverify.contoso.azurefd.net` na nebo prostřednictvím dočasného mapování afdverify, jako je mapováno na ověření mapy CNAME pro zástupné znakové) také (Azure DNS podporuje záznamy se zástupnými symboly).

Můžete také přidat tolik jednoúrovňových subdomén domény se zástupnými symboly v zástupcích front-endu, pokud nedosahuje maxima. limit front-endových hostitelů. Tato funkce může být vyžadována pro definování jiné trasy pro subdoménu než ostatní domény (z domény se zástupnými symboly) nebo pro jiné zásady WAF pro určitou subdoménu. Tak, `*.contoso.com` umožní `foo.contoso.com` přidání, aniž by museli `foo.bar.contoso.com` znovu prokázat vlastnictví domény, `*.contoso.com`ale ne, protože to není jedna úroveň subdomény . Chcete-li přidat `foo.bar.contoso.com` bez `*.bar.contosonews.com` dalšího ověření vlastnictví domény, bude nutné přidat.

### <a name="limitations"></a>Omezení

1. Pokud je do daného profilu předních dveří přidána doména se zástupnými symboly, nelze je přidat do žádného jiného profilu předních dveří. 
2. Pokud je do daného profilu Front Door přidána doména se zástupnými symboly, nelze přidat žádné subdomény této domény se zástupnými symboly do jiných front door nebo azure cdn z profilu microsoftu.
3. Pokud je subdoména domény se zástupnými symboly přidána do profilu Front Door nebo Azure CDN z profilu microsoftu, nelze doménu se zástupnými kartami přidat do žádného jiného profilu Front Door. 
4. Pokud dva profily (Front Door nebo Azure CDN od Microsoftu) mají různé subdomény kořenové domény, pak domény se zástupnými symboly nelze přidat na některý z profilů.

## <a name="certificate-binding-for-wildcard-domains-and-its-subdomains"></a>Vazba certifikátu pro domény se zástupnými symboly a její subdomény

Pro přijetí přenosu HTTPS v doméně se zástupnými symboly je nutné povolit protokol HTTPS v doméně se zástupnými symboly. Vazba certifikátu pro doménu se zástupnými symboly vyžaduje zástupný certifikát, to znamená, že název předmětu certifikátu by měl mít také doménu se zástupnými symboly.

> [!NOTE]
> V současné době pouze pomocí vlastní možnost ssl certifikát je k dispozici pro povolení HTTPS pro domény se zástupnými kódy. Spravované certifikáty Front Door nelze použít pro domény se zástupnými symboly. 

Můžete použít stejný certifikát se zástupnými kódy z trezoru klíčů pro subdomény nebo je podporováno také použití certifikátů spravovaných předními dveřmi pro subdomény.
Pokud je subdoména přidána pro doménu se zástupnými symboly a doména se zástupnými kartami již měla přidružený certifikát, nelze protokol HTTPS pro tuto subdoménu zakázat. Subdoména bude ve výchozím nastavení používat vazbu certifikátu domény se zástupnými symboly, pokud není přepsána jiným certifikátem trezoru klíčů nebo certifikátem spravovaného předními dveřmi.

## <a name="web-application-firewall-for-wildcard-domains-and-its-subdomains"></a>Brána firewall webové aplikace pro domény se zástupnými kódy a její subdomény

Zásady WAF lze připojit k doméně se zástupnými symboly podobnými jiným doménám. Pro subdoménu domény se zástupnými symboly lze použít jinou zásadu WAF. Pro subdomény je nutné explicitně zadat zásadu WAF, která má být použita, a to i v případě, že se jedná o stejnou zásadu jako doména se zástupnými symboly. Subdomény **automaticky nedědí** zásadu WAF z domény se zástupnými symboly.

Pokud máte scénář, ve kterém nechcete, aby waf spouštět pro subdoménu, pak můžete vytvořit prázdnou zásadu WAF bez spravovaných nebo vlastních pravidel.

## <a name="routing-rules-for-wildcard-domains-and-its-subdomains"></a>Pravidla směrování pro domény se zástupnými kódy a jejich subdomény

Při konfiguraci pravidla směrování můžete jako front-endového hostitele vybrat doménu se zástupnými kódy. Můžete také mít různé chování trasy pro doménu se zástupnými symboly vs. subdomény. Jak je popsáno v postupu [front door,](front-door-route-matching.md)bude za běhu vybrána nejkonkrétnější shoda pro doménu mezi různými pravidly směrování.

> [!WARNING]
> Pokud máte dvě pravidla směrování, `*.foo.com/*` jako **je trasa 1**: `bar.foo.com/somePath/*` mapována na back-end ový `bar.foo.com/anotherPath/*`fond A a **route 2**: mapována na back-end fond B a pokud požadavek přijde pro , pak vaši klienti uvidí selhání jako přední dveře nenajde žádnou shodu v obou trasách. Je to proto, že podle našeho [algoritmu párování tras](front-door-route-matching.md), Front Door vybere Route 2 na základě konkrétnější shody domény, ale pouze k zjištění, že neexistují žádné odpovídající cesty vzory. 


## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, jak [přidat vlastní doménu na přední dveře](front-door-custom-domain.md).
- Přečtěte si, jak [povolit protokol HTTPS ve vlastní doméně](front-door-custom-domain-https.md).
