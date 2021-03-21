---
title: Integrace služby Azure s integrací virtuální sítě pro izolaci sítě
titlesuffix: Azure Virtual Network
description: Tento článek popisuje různé metody integrace služby Azure do virtuální sítě, která umožňuje zabezpečený přístup ke službě Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: kumud
ms.openlocfilehash: 2c0c4bec93b8fa61275c376fbae2a3a063e72a6f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98785528"
---
# <a name="integrate-azure-services-with-virtual-networks-for-network-isolation"></a>Integrace služeb Azure s virtuálními sítěmi pro izolaci sítě

Integrace Virtual Network (VNet) pro službu Azure umožňuje uzamknout přístup k této službě pouze do vaší infrastruktury virtuální sítě. Infrastruktura virtuální sítě také zahrnuje partnerské virtuální sítě a místní sítě.

Integrace virtuální sítě poskytuje službám Azure výhody izolace sítě a je možné ji provést pomocí jedné nebo několika z následujících metod:
- [Nasazení vyhrazených instancí služby do virtuální sítě](virtual-network-for-azure-services.md). Služby pak mohou být soukromě přistupované v rámci virtuální sítě a z místních sítí.
- Pomocí [privátního koncového bodu](../private-link/private-endpoint-overview.md) , který se připojuje soukromě a bezpečně ke službě využívající [privátní propojení Azure](../private-link/private-link-overview.md). Privátní koncový bod používá privátní IP adresu z vaší virtuální sítě a efektivně ho přinášejí do vaší virtuální sítě.
- Přístup ke službě pomocí veřejných koncových bodů rozšířením virtuální sítě na službu prostřednictvím [koncových bodů služby](virtual-network-service-endpoints-overview.md). Koncové body služby umožňují zabezpečit prostředky služby ve virtuální síti.
- Použití [značek služeb](service-tags-overview.md) k povolení nebo odepření provozu do prostředků Azure do a z koncových bodů veřejných IP adres.

## <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>Nasazení vyhrazených služeb Azure do virtuálních sítí

Když nasadíte vyhrazené služby Azure ve virtuální síti, můžete komunikovat s prostředky služby soukromě prostřednictvím privátních IP adres.

![Nasazení vyhrazených služeb Azure do virtuálních sítí](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Nasazení vyhrazené služby Azure do vaší virtuální sítě poskytuje následující možnosti:
- Prostředky ve virtuální síti můžou vzájemně komunikovat soukromě prostřednictvím privátních IP adres. Například přímý přenos dat mezi HDInsight a SQL Server běžící na virtuálním počítači ve virtuální síti.
- Místní prostředky mají přístup k prostředkům ve virtuální síti pomocí privátních IP adres přes síť VPN typu Site-to-Site (VPN Gateway) nebo ExpressRoute.
- U virtuálních sítí je možné navázat partnerský vztah, aby bylo možné mezi sebou vzájemně komunikovat prostředky pomocí privátních IP adres.
- Instance služby ve virtuální síti jsou většinou plně spravovány službou Azure. To zahrnuje monitorování stavu prostředků a škálování s využitím zátěže.
- Instance služby jsou nasazeny do podsítě ve virtuální síti. Příchozí a odchozí síťový přístup pro podsíť musí být otevřený prostřednictvím skupin zabezpečení sítě podle pokynů poskytovaných službou.
- Některé služby také ukládají omezení pro podsíť, ve které jsou nasazená, a omezují použití zásad, trasy nebo kombinování virtuálních počítačů a prostředků služeb ve stejné podsíti. U každé služby se můžete podívat na konkrétní omezení, která se v průběhu času můžou měnit. Příklady takových služeb jsou Azure NetApp Files, vyhrazené HSM, Azure Container Instances App Service.
- V případě potřeby můžou služby vyžadovat delegovanou podsíť jako explicitní identifikátor, který podsíť může hostovat určitou službu. Delegováním služeb získají explicitní oprávnění k vytváření prostředků specifických pro službu v delegované podsíti.
- Podívejte se na příklad odpovědi REST API ve virtuální síti s delegovanou podsítí. K dispozici je úplný seznam služeb, které používají delegovaný model podsítě, k dispozici prostřednictvím rozhraní API pro delegování.

Seznam služeb, které se dají nasadit do virtuální sítě, najdete v tématu [nasazení vyhrazených služeb Azure do virtuálních sítí](virtual-network-for-azure-services.md).

## <a name="private-link-and-private-endpoints"></a>Privátní odkaz a soukromé koncové body

Soukromé koncové body můžete použít k zabezpečenému přenosu událostí přímo z vaší virtuální sítě do prostředku Azure prostřednictvím privátního propojení, aniž byste museli procházet veřejným internetem. Privátní koncový bod je speciální síťové rozhraní pro službu Azure ve vaší virtuální síti. Při vytváření privátního koncového bodu pro prostředek Azure zajišťuje zabezpečené připojení mezi klienty ve vaší virtuální síti a vaším prostředkem Azure. Privátnímu koncovému bodu je přiřazena IP adresa z rozsahu IP adres vaší virtuální sítě. Připojení mezi soukromým koncovým bodem a službou Azure používá zabezpečený privátní odkaz.

Následující příklad ukazuje privátní přístup k privátnímu koncovému bodu prostředku Event Grid, který poskytuje zabezpečené připojení mezi klienty ve virtuální síti a prostředky Event Grid.

![Privátní přístup k prostředku databáze SQL pomocí privátního koncového bodu](./media/network-isolation/architecture-diagram.png)

Další informace o privátních odkazech a seznam podporovaných služeb Azure najdete v tématu [co je to privátní odkaz?](../private-link/private-link-overview.md)

## <a name="service-endpoints"></a>Koncové body služby
Koncový bod služby virtuální sítě poskytuje zabezpečené a přímé připojení ke službám Azure přes optimalizovanou trasu přes páteřní síť Azure. Koncové body umožňují svázat vaše důležité prostředky služeb Azure pouze s vašimi virtuálními sítěmi. Koncové body služby umožňují privátním IP adresám ve virtuální síti dosáhnout koncového bodu služby Azure bez nutnosti veřejné IP adresy ve virtuální síti.

![Svázání služeb Azure s virtuálními sítěmi](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

Další informace najdete v tématu [koncové body služby virtuální sítě](virtual-network-service-endpoints-overview.md) .

## <a name="service-tags"></a>Značky služeb

Značka služby představuje skupinu předpon IP adres z dané služby Azure. Pomocí značek služeb můžete definovat řízení přístupu k síti pro [skupiny zabezpečení sítě](./network-security-groups-overview.md#security-rules) nebo [Azure firewall](../firewall/service-tags.md). Zadáním názvu značky služby (například AzureEventGrid) v příslušném zdrojovém nebo cílovém poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu.

![Povolení nebo zamítnutí provozu pomocí značek služeb](./media/network-isolation/service-tags.png)

Pomocí značek služeb můžete zajistit izolaci sítě a chránit prostředky Azure před obecným internetem při přístupu ke službám Azure, které mají veřejné koncové body. Vytvořte pravidla skupiny zabezpečení příchozích a odchozích sítí pro zamítnutí provozu do a z **Internetu** a umožněte přenos do/z **AzureCloud** nebo jiných [dostupných značek služeb](service-tags-overview.md#available-service-tags) pro konkrétní služby Azure.

Další informace o značkách služeb a službách Azure, které je podporují, najdete v tématu [Přehled značek služeb](service-tags-overview.md) .

## <a name="next-steps"></a>Další kroky

- Naučte se [integrovat aplikaci do sítě Azure](../app-service/web-sites-integrate-with-vnet.md).
- Naučte se, jak [omezit přístup k prostředkům pomocí značek služeb](tutorial-restrict-network-access-to-resources.md).
- Naučte se [připojit soukromě k účtu Azure Cosmos pomocí privátního odkazu Azure](../private-link/tutorial-private-endpoint-cosmosdb-portal.md).