---
title: Přední dvířka Azure – podpora pro domény se zástupnými znaky
description: Tento článek vám pomůže porozumět tomu, jak přední dvířka Azure podporuje mapování a správu domén se zástupnými znaky v seznamu vlastních domén.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: 18504f1ed4200889b20c9608c9c0ad2c13c9aaa5
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425758"
---
# <a name="wildcard-domains"></a>Domény se zástupnými znaky

Kromě domén Apex a subdomén můžete také namapovat zástupnou doménu na front-end hostitele nebo vlastní domény pro profil front-endu Azure. Pokud máte v konfiguraci front-end serverů ve službě Azure frontu se zástupnými znaky, zjednoduší se chování směrování provozu pro více subdomén pro rozhraní API, aplikace nebo weby ze stejného pravidla směrování. Nemusíte měnit konfiguraci, aby bylo možné přidat nebo zadat každou subdoménu samostatně. Můžete například definovat směrování pro `customer1.contoso.com` , `customer2.contoso.com` a `customerN.contoso.com` pomocí stejného pravidla směrování a přidat doménu se zástupnými znaky `*.contoso.com` .

Mezi klíčové scénáře, které jsou vylepšené podporou zástupných domén, patří:

- Nemusíte začlenit každou subdoménu do profilu front-dveří Azure a pak povolit protokol HTTPS pro vytvoření vazby certifikátu pro každou subdoménu.
- Pokud aplikace přidá novou subdoménu, nebudete už muset měnit provozní konfiguraci front-dveří Azure. Dříve jste museli přidat subdoménu, vytvořit k ní certifikát, připojit zásadu firewallu webových aplikací (WAF) a pak přidat doménu do různých pravidel směrování.

> [!NOTE]
> V současné době se k přidávání domén se zástupnými znaky prostřednictvím Azure DNS podporuje jenom přes rozhraní API, PowerShell a rozhraní příkazového řádku Azure CLI. Podpora pro přidávání a správu domén se zástupnými znaky není v Azure Portal k dispozici.

## <a name="adding-wildcard-domains"></a>Přidání domén se zástupnými znaky

Můžete přidat doménu se zástupnými znaky do části pro front-end hostitele nebo domény. Podobně jako u subdomén se v případě front-end Azure ověří, že pro vaši doménu se zástupnými znaky existuje mapování záznamů CNAME. Toto mapování DNS může být přímé mapování záznamů CNAME, jako je `*.contoso.com` namapované na `contoso.azurefd.net` . Nebo můžete použít dočasné mapování afdverify. Například `afdverify.contoso.com` namapována tak, aby `afdverify.contoso.azurefd.net` ověřovala mapování záznamů CNAME pro zástupný znak.

> [!NOTE]
> Azure DNS podporuje záznamy se zástupným znakem.

Do front-end hostitelů můžete přidat tolik subdomén v doméně se zástupnými znaky, a to až do limitu front-endu hostitelů. Tato funkce může být vyžadována pro:

- Definování jiné trasy pro subdoménu, než je zbytek domén (z domény se zástupnými znaky).

- Má jiné zásady WAF pro konkrétní subdoménu. Například `*.contoso.com` umožňuje přidat `foo.contoso.com` bez nutnosti znovu prokázat vlastnictví domény. Ale neumožňuje, `foo.bar.contoso.com` protože se nejedná o subdoménu jedné úrovně `*.contoso.com` . Chcete-li přidat `foo.bar.contoso.com` bez dalšího ověření vlastnictví domény, je `*.bar.contosonews.com` nutné přidat.

Můžete přidat domény se zástupnými znaky a jejich subdomény s určitými omezeními:

- Pokud se do profilu front-dveří Azure přidá doména se zástupnými znaky:
  - Zástupná doména se nedá přidat do žádného jiného profilu front-dveří Azure.
  - Domény se zástupnými znaky první úrovně v doméně se zástupnými znaky se nedají přidat do jiného profilu služby Azure front-dveří ani do profilu Azure Content Delivery Network.
- Pokud je subdoménou domény se zástupným znakem již přidána do profilu služby Azure front-dveří nebo do profilu služby Azure Content Delivery Network, nelze použít doménu se zástupnými znaky pro jiný profil front-end Azure.
- Pokud mají dva profily (přední vrátka Azure nebo Azure Content Delivery Network) různé subdomény kořenové domény, pak se do těchto profilů nedají přidat zástupné domény.

## <a name="certificate-binding"></a>Vazba certifikátu

Pro přijetí provozu HTTPS v doméně se zástupnými znaky musíte povolit protokol HTTPS v doméně se zástupnými znaky. Vazba certifikátu pro zástupnou doménu vyžaduje certifikát se zástupným znakem. To znamená, že název subjektu certifikátu musí mít také zástupnou doménu.

> [!NOTE]
> V současné době je k dispozici pouze možnost použít vlastní certifikát SSL pro povolení HTTPS pro domény se zástupnými znaky. Spravované certifikáty pro službu Azure front-dveří nelze použít pro zástupné domény.

Můžete použít stejný certifikát se zástupným znakem z Azure Key Vault nebo ze služby Azure front-dveří spravované certifikáty pro subdomény.

Pokud se k doméně se zástupným znakem přidá subdoménou, která už má přidružený certifikát, nemůžete pro subdoménu zakázat HTTPS. Subdoména používá vazbu certifikátu pro doménu se zástupnými znaky, pokud se nepoužije jiný Key Vault nebo certifikát spravovaný přes frontu Azure.

## <a name="waf-policies"></a>Zásady WAF

Zásady WAF se dají připojit k doménám se zástupnými znaky, podobně jako ostatní domény. Pro subdoménu domény se zástupnými znaky se dají použít jiné zásady WAF. Pro subdomény musíte zadat zásadu WAF, která se má použít, i když se jedná o stejné zásady jako doména se zástupnými znaky. Subdomény nedědí *automaticky zásady* WAF ze zástupné domény.

Pokud nechcete, aby se pro subdoménu spouštěla zásada WAF, můžete vytvořit prázdnou zásadu WAF bez spravovaného nebo vlastního RuleSets.

## <a name="routing-rules"></a>Pravidla směrování

Při konfiguraci pravidla směrování můžete jako front-end hostitele Vybrat doménu se zástupnými znaky. Pro domény se zástupnými znaky a subdomény můžete také použít jiné chování směrování. Jak je popsáno v tématu Jak se na [frontách Azure nachází párování směrování](front-door-route-matching.md), je za běhu zvolena nejvíce specifická shoda pro doménu napříč různými pravidly směrování.

> [!IMPORTANT]
> Musíte mít stejné vzory cest napříč pravidly směrování, jinak se zobrazí selhání. Máte například dvě pravidla směrování, jako je třeba trasa 1 ( `*.foo.com/*` namapovaná na fond back-end a) a trasa 2 ( `/bar.foo.com/somePath/*` namapovaná na back-end fond B). Pak přijde žádost o `bar.foo.com/anotherPath/*` . Přední dvířka Azure vybírá trasu 2 na základě přesnější shody domény, a to jenom v případě, že v cestách neodpovídají vzory cest.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit profil front-dveří Azure](quickstart-create-front-door.md).
- Přečtěte si, jak [Přidat vlastní doménu na přední dveře Azure](front-door-custom-domain.md).
- Naučte se, jak [Povolit protokol HTTPS na vlastní doméně](front-door-custom-domain-https.md).
