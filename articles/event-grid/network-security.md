---
title: Zabezpečení sítě pro prostředky Azure Event Grid
description: Tento článek popisuje, jak používat značky služeb pro odchozí přenosy, pravidla brány firewall protokolu IP pro příchozí komunikaci a privátní koncové body pro příchozí komunikaci s Azure Event Grid.
author: VidyaKukke
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: vkukke
ms.openlocfilehash: 10c9b165041f0a4a1f09511f17bef3629353c3b2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94917524"
---
# <a name="network-security-for-azure-event-grid-resources"></a>Zabezpečení sítě pro prostředky Azure Event Grid
Tento článek popisuje, jak používat následující funkce zabezpečení pro Azure Event Grid: 

- Značky služeb pro odchozí přenosy
- Pravidla brány firewall protokolu IP pro příchozí přenosy
- Soukromé koncové body pro příchozí přenosy dat


## <a name="service-tags"></a>Značky služeb
Značka služby představuje skupinu předpon IP adres z dané služby Azure. Společnost Microsoft spravuje předpony adres, které jsou zahrnuté ve značce služby, a automaticky aktualizuje značku služby, protože se mění adresy. tím se minimalizuje složitost častých aktualizací pravidel zabezpečení sítě. Další informace o značkách služby najdete v tématu [Přehled značek služeb](../virtual-network/service-tags-overview.md).

Pomocí značek služeb můžete definovat řízení přístupu k síti pro [skupiny zabezpečení sítě](../virtual-network/network-security-groups-overview.md#security-rules) nebo [Azure firewall](../firewall/service-tags.md). Při vytváření pravidel zabezpečení používejte značky služby místo konkrétních IP adres. Zadáním názvu značky služby (například **AzureEventGrid**) v příslušném *zdrojovém* nebo *cílovém* poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu.

| Značka služby | Účel | Dá se použít příchozí nebo odchozí? | Je možné je rozregionovat? | Lze použít s Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|
| AzureEventGrid | Azure Event Grid. | Obojí | No | No |


## <a name="ip-firewall"></a>Brána firewall protokolu IP 
Azure Event Grid podporuje řízení přístupu na základě IP adres pro publikování do témat a domén. Pomocí ovládacích prvků založených na protokolu IP můžete omezit vydavatele na téma nebo doménu jenom na sadu schválených sad počítačů a cloudových služeb. Tato funkce doplňuje [mechanismy ověřování](security-authentication.md) podporované nástrojem Event Grid.

Ve výchozím nastavení je k tématům a doménám přístup z Internetu, pokud požadavek přichází s platným ověřováním a autorizací. Pomocí brány firewall protokolu IP můžete tuto možnost omezit na více než jenom sadu IP adres nebo rozsahy IP adres v zápisu [CIDR (v Inter-Domain směrování v netřídě)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) . Vydavatelé pocházející z jakékoli jiné IP adresy se odmítnou a obdrží odpověď 403 (zakázáno).

Podrobné pokyny ke konfiguraci brány firewall protokolu IP pro témata a domény najdete v tématu [Konfigurace brány firewall protokolu IP](configure-firewall.md).

## <a name="private-endpoints"></a>Privátní koncové body
Pomocí [privátních koncových bodů](../private-link/private-endpoint-overview.md) můžete v případě, že chcete přijímat události přímo z vaší virtuální sítě, zabezpečit vaše témata a domény přes [privátní propojení](../private-link/private-link-overview.md) , aniž byste museli procházet veřejným internetem. Privátní koncový bod je speciální síťové rozhraní pro službu Azure ve vaší virtuální síti. Když vytvoříte privátní koncový bod pro vaše téma nebo doménu, zajistíte zabezpečené připojení mezi klienty ve vaší virtuální síti a vaším prostředkem Event Grid. Privátnímu koncovému bodu je přiřazena IP adresa z rozsahu IP adres vaší virtuální sítě. Připojení mezi soukromým koncovým bodem a službou Event Grid používá zabezpečený privátní odkaz.

![Diagram architektury](./media/network-security/architecture-diagram.png)

Použití privátních koncových bodů pro váš prostředek Event Grid vám umožní:

- Zabezpečený přístup k vašemu tématu nebo doméně ze sítě VNet přes páteřní síť Microsoftu na rozdíl od veřejného Internetu.
- Připojte se bezpečně z místních sítí, které se připojují k virtuální síti pomocí sítě VPN nebo Express Routes s privátním partnerským vztahem.

Když vytvoříte privátní koncový bod pro téma nebo doménu ve vaší virtuální síti, pošle se žádost o souhlas ke schválení vlastníkem prostředku. Pokud uživatel žádající o vytvoření privátního koncového bodu je zároveň vlastníkem prostředku, je tato žádost o souhlas automaticky schválena. V opačném případě je připojení v **nedokončeném** stavu, dokud není schváleno. Aplikace ve virtuální síti se můžou bez problémů připojit k Event Grid službě přes soukromý koncový bod pomocí stejných připojovacích řetězců a autorizačních mechanismů, které by jinak používaly. Vlastníci prostředků mohou spravovat žádosti o souhlas a soukromé koncové body prostřednictvím karty **privátní koncové body** pro prostředek v Azure Portal.

### <a name="connect-to-private-endpoints"></a>Připojit k privátním koncovým bodům
Vydavatelé ve virtuální síti, které používají privátní koncový bod, by měli použít stejný připojovací řetězec pro téma nebo doménu jako klienti připojující se ke veřejnému koncovému bodu. Překlad názvů DNS automaticky směruje připojení z virtuální sítě k tématu nebo doméně prostřednictvím privátního propojení. Ve výchozím nastavení vytvoří služba Event Grid [privátní ZÓNU DNS](../dns/private-dns-overview.md) připojenou k virtuální síti s potřebnou aktualizací privátních koncových bodů. Pokud ale používáte vlastní server DNS, možná budete muset provést další změny v konfiguraci DNS.

### <a name="dns-changes-for-private-endpoints"></a>Změny DNS u privátních koncových bodů
Při vytváření privátního koncového bodu se záznam CNAME DNS pro prostředek aktualizuje na alias v subdoméně s předponou `privatelink` . Ve výchozím nastavení se vytvoří privátní zóna DNS, která odpovídá subdoméně privátního odkazu. 

Když vyřešíte adresu URL tématu nebo adresy URL koncového bodu domény mimo virtuální síť s privátním koncovým bodem, přeloží se na veřejný koncový bod služby. Záznamy o prostředcích DNS pro "téma", pokud jsou vyřešeny **mimo virtuální síť** hostující soukromý koncový bod, bude:

| Název                                          | Typ      | Hodnota                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | CNAME     | \<Azure traffic manager profile\>

Pomocí [brány firewall protokolu IP](#ip-firewall)můžete odepřít nebo řídit přístup pro klienta mimo virtuální síť prostřednictvím veřejného koncového bodu. 

Při překladu z virtuální sítě hostující soukromý koncový bod se v tématu nebo adrese URL koncového bodu domény přeloží na IP adresu privátního koncového bodu. Záznamy o prostředcích DNS pro téma "kapitola", při jejich vyřešení z **virtuální** sítě, která je hostitelem privátního koncového bodu, bude:

| Název                                          | Typ      | Hodnota                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | A         | 10.0.0.5

Tento přístup umožňuje přístup k tématu nebo doméně pomocí stejného připojovacího řetězce pro klienty ve virtuální síti, která je hostitelem privátních koncových bodů, a klientů mimo virtuální síť.

Pokud ve vaší síti používáte vlastní server DNS, klienti můžou přeložit plně kvalifikovaný název domény pro téma nebo koncový bod domény na IP adresu privátního koncového bodu. Nakonfigurujte server DNS tak, aby delegoval subdoménu privátního propojení s privátní zónou DNS pro virtuální síť, nebo nakonfigurujte záznamy A pro `topicOrDomainName.regionName.privatelink.eventgrid.azure.net` IP adresu privátního koncového bodu.

Doporučený název zóny DNS je `privatelink.eventgrid.azure.net` .

### <a name="private-endpoints-and-publishing"></a>Soukromé koncové body a publikování

Následující tabulka popisuje různé stavy připojení privátního koncového bodu a důsledky publikování:

| Stav připojení   |  Úspěšné publikování (ano/ne) |
| ------------------ | -------------------------------|
| Schválené           | Yes                            |
| Zamítnuto           | No                             |
| Čekající            | No                             |
| Odpojeno       | No                             |

Aby publikování bylo úspěšné, měla by být **schválen** stav připojení privátního koncového bodu. Pokud se připojení odmítne, nebude možné ho schválit pomocí Azure Portal. Jedinou možností je odstranit připojení a místo toho vytvořit nový.

## <a name="pricing-and-quotas"></a>Ceny a kvóty
**Soukromé koncové body** jsou k dispozici v Event Grid úrovně Basic i Premium. Event Grid umožňuje vytvoření připojení privátního koncového bodu 64 pro každé téma nebo doménu. 

Funkce **brány firewall protokolu IP** je k dispozici v Event Grid úrovně Basic i Premium. Pro každé téma nebo doménu vám umožníme vytvořit až 16 pravidel brány firewall protokolu IP.

## <a name="next-steps"></a>Další kroky
Můžete nakonfigurovat bránu firewall protokolu IP pro prostředek Event Grid, abyste omezili přístup přes veřejný Internet jenom z vybrané sady IP adres nebo rozsahů IP adres. Podrobné pokyny najdete v tématu [Konfigurace brány firewall protokolu IP](configure-firewall.md).

Privátní koncové body můžete nakonfigurovat tak, aby omezily přístup jenom z vybraných virtuálních sítí. Podrobné pokyny najdete v tématu [Konfigurace privátních koncových bodů](configure-private-endpoints.md).

Problémy s připojením k síti najdete v tématu řešení potíží s [připojením k síti](troubleshoot-network-connectivity.md) .