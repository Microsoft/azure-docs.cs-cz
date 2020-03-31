---
title: Zabezpečení sítě pro prostředky Sítě událostí Azure
description: Tento článek popisuje, jak nakonfigurovat přístup z privátní koncové body
services: event-grid
author: VidyaKukke
ms.service: event-grid
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: vkukke
ms.openlocfilehash: ed3b70ad267252981110e7970bc5c5fad6cf4b4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79300151"
---
# <a name="network-security-for-azure-event-grid-resources"></a>Zabezpečení sítě pro prostředky Sítě událostí Azure
Tento článek popisuje, jak používat následující funkce zabezpečení s Azure Event Grid: 

- Značky služeb pro odchozí přenos (náhled)
- Pravidla brány IP firewall pro příchozí přenos dat (náhled)
- Privátní koncové body pro příchozí přenos dat (náhled)


## <a name="service-tags"></a>Značky služeb
Značka služby představuje skupinu předpon IP adres z dané služby Azure. Společnost Microsoft spravuje předpony adres zahrnuté v servisním štítku a automaticky aktualizuje výrobní číslo, jak se adresy mění, čímž minimalizuje složitost častých aktualizací pravidel zabezpečení sítě. Další informace o značkách služeb naleznete v tématu [Service tags overview](../virtual-network/service-tags-overview.md).

Pomocí značek služeb můžete definovat ovládací prvky přístupu k síti ve [skupinách](../virtual-network/security-overview.md#security-rules) zabezpečení sítě nebo [azure firewall](../firewall/service-tags.md). Při vytváření pravidel zabezpečení používejte značky služeb místo konkrétních adres IP. Zadáním názvu značky služby (například **AzureEventGrid**) v příslušném *zdrojovém* nebo *cílovém* poli pravidla můžete povolit nebo odepřít provoz pro odpovídající službu.

| Značka služby | Účel | Můžete použít příchozí nebo odchozí? | Může být regionální? | Můžete použít s Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| Síť AzureEventGrid | Azure Event Grid. <br/><br/>*Poznámka:* Tato značka zahrnuje koncové body Azure Event Grid pouze v USA – – – střed, USA – východ, USA – východ 2, USA – západ 2 a US Central. | Obojí | Ne | Ne |


## <a name="ip-firewall"></a>Brána firewall protokolu IP 
Azure Event Grid podporuje ovládací prvky přístupu založené na PROTOKOLU IP pro publikování na témata a domény. Pomocí ovládacích prvků založených na protokolu IP můžete omezit vydavatele na téma nebo doménu pouze na sadu schválených počítačů a cloudových služeb. Tato funkce doplňuje [mechanismy ověřování](security-authentication.md) podporované event grid.

Ve výchozím nastavení jsou téma a doména přístupné z internetu, pokud je požadavek dodáván s platným ověřováním a autorizací. S IP firewallem ji můžete dále omezit pouze na sadu IP adres nebo rozsahy IP adres v [zápisu CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) Vydavatelé pocházející z jakékoli jiné IP adresy budou odmítnuti a obdrží odpověď 403 (Forbidden).


## <a name="private-endpoints"></a>Soukromé koncové body
[Privátní koncové body](../private-link/private-endpoint-overview.md) můžete povolit příchozí události přímo z vaší virtuální sítě do témat a domén bezpečně přes [privátní odkaz](../private-link/private-link-overview.md) bez nutnosti prostřednictvím veřejného internetu. Privátní koncový bod je speciální síťové rozhraní pro službu Azure ve vaší virtuální síti. Když vytvoříte soukromý koncový bod pro vaše téma nebo doménu, poskytuje zabezpečené připojení mezi klienty ve vaší virtuální síti a prostředkem gridu událostí. Privátnímu koncovému bodu je přiřazena IP adresa z rozsahu IP adres vaší virtuální sítě. Připojení mezi privátním koncovým bodem a službou Event Grid používá zabezpečené soukromé propojení.

![Diagram architektury](./media/network-security/architecture-diagram.png)

Použití soukromých koncových bodů pro prostředek event gridu umožňuje:

- Zabezpečte přístup k tématu nebo doméně z virtuální sítě přes páteřní síť Microsoftu na rozdíl od veřejného internetu.
- Bezpečně se připojujte z místních sítí, které se připojují k virtuální síti pomocí VPN nebo ExpressRoutes s privátním partnerského vztahu.

Když vytvoříte soukromý koncový bod pro téma nebo doménu ve virtuální síti, žádost o souhlas se odešle ke schválení vlastníkovi prostředku. Pokud je uživatel požadující vytvoření privátního koncového bodu také vlastníkem prostředku, bude tato žádost o souhlas automaticky schválena. V opačném případě je připojení v **čekajícím** stavu, dokud nebude schváleno. Aplikace ve virtuální síti se mohou bez problémů připojit ke službě Event Grid přes privátní koncový bod pomocí stejných připojovacích řetězců a autorizačních mechanismů, které by jinak používaly. Vlastníci prostředků můžete spravovat žádosti o souhlas a soukromé koncové body, a to prostřednictvím karty **Soukromé koncové body** pro prostředek na webu Azure Portal.

### <a name="connect-to-private-endpoints"></a>Připojení k privátním koncovým bodům
Vydavatelé ve virtuální síti používající privátní koncový bod by měli používat stejný připojovací řetězec pro téma nebo doménu jako klienti, kteří se připojují k veřejnému koncovému bodu. Překlad DNS automaticky směruje připojení z virtuální sítě k tématu nebo doméně přes privátní propojení. Event Grid vytvoří [soukromou zónu DNS](../dns/private-dns-overview.md) připojenou k virtuální síti s nezbytnou aktualizací pro soukromé koncové body, ve výchozím nastavení. Pokud však používáte vlastní server DNS, bude pravděpodobně nutné provést další změny konfigurace DNS.

### <a name="dns-changes-for-private-endpoints"></a>Změny DNS pro soukromé koncové body
Při vytváření soukromého koncového bodu se záznam Dns CNAME pro daný prostředek aktualizuje `privatelink`na alias v subdoméně s předponou . Ve výchozím nastavení je vytvořena soukromá zóna DNS, která odpovídá subdoméně privátního propojení. 

Když vyřešíte adresu URL tématu nebo koncového bodu domény z mimo virtuální síť s privátním koncovým bodem, vyřeší se na veřejný koncový bod služby. Záznamy o prostředcích DNS pro 'topicA', když vyřešen z **mimo virtuální sítě** hostování privátní koncový bod, bude:

| Name (Název)                                          | Typ      | Hodnota                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | CNAME     | \<profil správce provozu azure\>

Můžete odepřít nebo řídit přístup pro klienta mimo virtuální síť prostřednictvím veřejného koncového bodu pomocí [brány firewall IP](#ip-firewall). 

Když se vyřeší z virtuální sítě hostující privátní koncový bod, téma nebo doména koncového bodu URL překládá na ip adresu privátního koncového bodu. Záznamy o prostředcích DNS pro téma 'topicA', když vyřešen z **uvnitř virtuální sítě** hostování privátní koncový bod, bude:

| Name (Název)                                          | Typ      | Hodnota                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | A         | 10.0.0.5

Tento přístup umožňuje přístup k tématu nebo doméně pomocí stejného připojovacího řetězce pro klienty na virtuální síti hostující privátní koncové body a klienty mimo virtuální síť.

Pokud v síti používáte vlastní server DNS, mohou klienti přeložit hlavní název domény pro koncový bod tématu nebo domény na privátní IP adresu koncového bodu. Nakonfigurujte server DNS tak, aby delegoval subdoménu privátního propojení do privátní zóny DNS pro virtuální síť, nebo nakonfigurujte záznamy A s `topicOrDomainName.regionName.privatelink.eventgrid.azure.net` privátní IP adresou koncového bodu.

Doporučený název zóny `privatelink.eventgrid.azure.net`DNS je .

### <a name="private-endpoints-and-publishing"></a>Privátní koncové body a publikování

Následující tabulka popisuje různé stavy privátního koncového bodu připojení a účinky na publikování:

| Stav připojení   |  Úspěšně publikovat (Ano/Ne) |
| ------------------ | -------------------------------|
| Schválené           | Ano                            |
| Rejected           | Ne                             |
| Čekající na vyřízení            | Ne                             |
| Odpojen       | Ne                             |

Aby bylo publikování úspěšné, měl by být **schválen**stav připojení soukromého koncového bodu . Pokud je připojení odmítnuto, nelze ho schválit pomocí portálu Azure. Jedinou možností je odstranit připojení a místo toho vytvořit nové.

## <a name="pricing-and-quotas"></a>Ceny a kvóty
**Soukromé koncové body** jsou k dispozici pouze s tématy a doménami úrovně premium. Event Grid umožňuje vytvořit až 64 privátních koncových bodů pro jedno téma nebo doménu. Pokud chcete upgradovat ze základní úrovně na úroveň Premium, přečtěte si článek [Aktualizovat cenovou úroveň.](update-tier.md)

**Funkce IP Firewall** je k dispozici v základní i prémiové úrovni Event Grid. Umožňujeme vytvořit až 16 pravidel ip firewallu pro jedno téma nebo doménu.


## <a name="next-steps"></a>Další kroky
Bránu firewall IP pro prostředek služby Event Grid můžete nakonfigurovat tak, aby omezovala přístup přes veřejný Internet pouze z vybrané sady adres IP nebo rozsahů IP adres. Podrobné pokyny naleznete v tématu [Konfigurace brány firewall IP](configure-firewall.md).

Soukromé koncové body můžete nakonfigurovat tak, aby omezovaly přístup pouze z vybraných virtuálních sítí. Podrobné pokyny naleznete v tématu [Konfigurace soukromých koncových bodů](configure-private-endpoints.md).
