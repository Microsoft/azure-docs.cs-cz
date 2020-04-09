---
title: Azure Virtual Network | Microsoft Docs
description: Seznamte se s koncepty a funkcemi služby Azure Virtual Network.
services: virtual-network
documentationcenter: na
author: anavinahar
tags: azure-resource-manager
Customer intent: As someone with a basic network background that is new to Azure, I want to understand the capabilities of Azure Virtual Network, so that my Azure resources such as VMs, can securely communicate with each other, the internet, and my on-premises resources.
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2019
ms.author: anavin
ms.openlocfilehash: 967d391d4ac9a9704688dce9636d9a71b2002549
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879356"
---
# <a name="what-is-azure-virtual-network"></a>Co je Azure Virtual Network?

Virtuální síť Azure (VNet) je základní stavební blok pro vaši privátní síť v Azure. Virtuální síť umožňuje mnoho typů prostředků Azure, jako jsou virtuální počítače Azure (VM), bezpečně komunikovat mezi sebou, internet a místní sítě. Virtuální síť je podobná tradiční síti, kterou byste provozovali ve vlastním datovém centru, ale přináší s sebou další výhody infrastruktury Azure, jako je škálování, dostupnost a izolace.

## <a name="vnet-concepts"></a>Koncepty virtuální sítě

- **Adresní prostor:** Při vytváření virtuální sítě je nutné zadat vlastní privátní ip adresní prostor pomocí veřejných a privátních adres (RFC 1918). Azure přiřazuje prostředkům ve virtuální síti privátní IP adresy z přiřazeného adresního prostoru. Pokud například nasadíte virtuální ho ve virtuální síti s adresním prostorem 10.0.0.0/16, bude virtuálnímu virtuálnímu síti přiřazena privátní IP adresa jako 10.0.0.4.
- **Podsítě:** Podsítě umožňují segmentovat virtuální síť do jedné nebo více podsítí a přidělit část adresního prostoru virtuální sítě každé podsíti. Potom můžete nasadit prostředky Azure v konkrétní podsíti. Stejně jako v tradiční síti, podsítě umožňují segmentovat adresní prostor virtuální sítě do segmentů, které jsou vhodné pro interní síť organizace. To také zlepšuje efektivitu přidělování adres. Prostředky v podsítích můžete zabezpečit pomocí skupin zabezpečení sítě. Další informace naleznete v [tématu Skupiny zabezpečení](security-overview.md).
- **Oblasti**: Virtuální síť je vymezena na jednu oblast/umístění; více virtuálních sítí z různých oblastí však lze propojit pomocí partnerského vztahu virtuální sítě.
- **Předplatné:** Virtuální síť je vymezena na předplatné. V každém [předplatném](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) Azure a v každé [oblasti](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) Azure můžete implementovat několik virtuálních sítí.

## <a name="best-practices"></a>Osvědčené postupy

Při vytváření sítě v Azure je důležité mít na paměti následující univerzální principy návrhu:

- Zajistěte nepřekrývající se adresní prostory. Ujistěte se, že váš adresní prostor virtuální sítě (blok CIDR) se nepřekrývá s jinými síťovými rozsahy vaší organizace.
- Vaše podsítě by neměly pokrývat celý adresní prostor virtuální sítě. Naplánujte si dopředu a vyhraďte si do budoucna nějaký adresní prostor.
- Doporučujese mít méně velkých virtuálních sítí než více malých virtuálních sítí. Tím se zabrání režii správy.
- Zabezpečte virtuální síť přiřazením skupin zabezpečení sítě (NSG) k podsítím pod nimi.

## <a name="communicate-with-the-internet"></a>Komunikace s internetem

Všechny prostředky ve virtuální síti můžete komunikovat odchozí k Internetu, ve výchozím nastavení. Příchozí komunikaci s prostředkem můžete umožnit tím, že prostředku přiřadíte veřejnou IP adresu nebo veřejný Load Balancer. Veřejnou IP adresu nebo veřejný Load Balancer můžete použít také ke správě odchozích připojení.  Další informace o odchozích připojeních v Azure najdete v tématech [Odchozí připojení](../load-balancer/load-balancer-outbound-connections.md), [Veřejné IP adresy](virtual-network-public-ip-address.md) a [Load Balancer](../load-balancer/load-balancer-overview.md).

>[!NOTE]
>Pokud používáte pouze interní [Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md), odchozí připojení nebudou dostupná, dokud neurčíte, jak mají [odchozí připojení](../load-balancer/load-balancer-outbound-connections.md) pracovat s veřejnou IP adresou nebo veřejným Load Balancerem na úrovni instance.

## <a name="communicate-between-azure-resources"></a>Komunikace mezi prostředky Azure

Prostředky Azure mezi sebou zabezpečeně komunikují jedním z následujících způsobů:

- **Prostřednictvím virtuální sítě:** Do virtuální sítě můžete nasadit virtuální počítače a několik dalších typů prostředků Azure, jako jsou služby Azure App Service Environment, Azure Kubernetes Service (AKS) a Azure Virtual Machine Scale Sets. Úplný seznam prostředků Azure, které můžete nasadit do virtuální sítě, najdete v tématu věnovaném [integraci virtuální sítě do služeb](virtual-network-for-azure-services.md).
- **Prostřednictvím koncového bodu služby pro virtuální síť**: Privátní adresní prostor a identitu virtuální sítě můžete přes přímé připojení rozšířit na prostředky služeb Azure, jako jsou účty Azure Storage a databáze Azure SQL. Koncové body služeb umožňují svázat vaše důležité prostředky služeb Azure pouze s virtuální sítí. Další informace najdete v tématu [Přehled koncových bodů služeb virtuální sítě](virtual-network-service-endpoints-overview.md).
- **Prostřednictvím partnerského vztahu virtuální sítě**: Můžete připojit virtuální sítě k sobě navzájem, povolení prostředků v obou virtuálních sítí komunikovat mezi sebou, pomocí partnerského vztahu virtuální sítě. Propojené virtuální sítě se můžou nacházet ve stejné oblasti Azure nebo v různých oblastech. Další informace najdete v tématu [Partnerský vztah virtuálních sítí](virtual-network-peering-overview.md).

## <a name="communicate-with-on-premises-resources"></a>Komunikace s místními prostředky

Své místní počítače a sítě můžete připojit k virtuální síti pomocí jakékoli kombinace následujících možností:

- **Virtuální privátní síť (VPN) typu Point-to-Site:** Vytváří se mezi virtuální sítí a jedním počítačem ve vaší síti. Každý počítač, který chcete navázat připojení k virtuální síti, musí toto připojení nakonfigurovat. Tento typ připojení je skvělý, pokud teprve začínáte s Azure, nebo pro vývojáře, protože nevyžaduje téměř nebo vůbec žádné změny vaší stávající sítě. Komunikace mezi počítačem a virtuální sítí se odesílá prostřednictvím šifrovaného tunelu přes internet. Další informace najdete v tématu popisujícím [síť VPN typu Point-to-Site](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#P2S).
- **Síť VPN typu Site-to-Site:** Vytváří se mezi místním zařízením VPN a službou Azure VPN Gateway nasazenou do virtuální sítě. Tento typ připojení povoluje přístup k virtuální síti všem místním prostředkům, které autorizujete. Komunikace mezi místním zařízením VPN a službou Azure VPN Gateway se odesílá prostřednictvím šifrovaného tunelu přes internet. Další informace najdete v tématu popisujícím [síť VPN typu Site-to-Site](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti).
- **Azure ExpressRoute:** Vytváří se mezi vaší sítí a Azure prostřednictvím partnera ExpressRoute. Toto připojení je soukromé. Provoz se nepřenáší přes internet. Další informace najdete v tématu popisujícím [ExpressRoute](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ExpressRoute).

## <a name="filter-network-traffic"></a>Filtrování provozu sítě

Síťový provoz mezi podsítěmi můžete filtrovat pomocí jedné nebo obou z následujících možností:

- **Skupiny zabezpečení:** Skupiny zabezpečení sítě a skupiny zabezpečení aplikací mohou obsahovat více příchozích a odchozích pravidel zabezpečení, která umožňují filtrovat přenosy do a z prostředků podle zdrojové a cílové adresy IP, portu a protokolu. Další informace naleznete v [tématu Skupiny zabezpečení sítě](security-overview.md#network-security-groups) nebo [Skupiny zabezpečení aplikací](security-overview.md#application-security-groups).
- **Síťová virtuální zařízení:** Síťové virtuální zařízení je virtuální počítač, který provádí určitou síťovou funkci, jako je například brána firewall, optimalizace sítě WAN nebo jiná síťová funkce. Úplný seznam dostupných síťových virtuálních zařízení, která můžete nasadit do virtuální sítě, najdete na webu [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

## <a name="route-network-traffic"></a>Směrování provozu sítě

Azure ve výchozím nastavení směruje provoz mezi podsítěmi, propojenými virtuálními sítěmi, místními sítěmi a internetem. K přepsání výchozích tras, které Azure vytváří, můžete implementovat jednu nebo obě z následujících možností:

- **Směrovací tabulky:** Pro jednotlivé podsítě můžete vytvářet vlastní směrovací tabulky s trasami, které řídí cíl směrování provozu. Další informace o [směrovacích tabulkách](virtual-networks-udr-overview.md#user-defined).
- **Trasy protokolu Border Gateway Protocol (BGP):** Pokud připojíte virtuální síť k místní síti pomocí připojení Azure VPN Gateway nebo ExpressRoute, můžete do svých virtuálních sítí rozšířit místní trasy BGP. Další informace o použití BGP se službou [Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange).

## <a name="virtual-network-integration-for-azure-services"></a>Integrace virtuální sítě pro služby Azure

Integrace služeb Azure do virtuální sítě Azure umožňuje privátní přístup ke službě z virtuálních počítačů nebo výpočetních prostředků ve virtuální síti.
Služby Azure můžete integrovat do virtuální sítě s následujícími možnostmi:
- Nasazení [vyhrazených instancí služby](virtual-network-for-azure-services.md) do virtuální sítě. Ke službám pak lze přistupovat soukromě v rámci virtuální sítě a z místních sítí.
- Použití [private link](../private-link/private-link-overview.md) pro soukromý přístup k určité instanci služby z vaší virtuální sítě a z místních sítí.
- Ke službě můžete přistupovat také pomocí veřejných koncových bodů rozšířením virtuální sítě na službu prostřednictvím [koncových bodů služby](virtual-network-service-endpoints-overview.md). Koncové body služby umožňují prostředky služby, které mají být zabezpečeny do virtuální sítě.
 

## <a name="azure-vnet-limits"></a>Limity azure virtuální sítě

Počet prostředků Azure, které můžete nasadit, jsou určitá omezení. Většina síťových limitů Azure má maximální hodnoty. Můžete však [zvýšit určitá omezení sítě,](../azure-portal/supportability/networking-quota-requests.md) jak je uvedeno na [stránce omezení virtuální sítě](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits). 

## <a name="pricing"></a>Ceny

Za používání Azure Virtuální sítě se neplatí, je zadarmo. Standardní poplatky se vztahují na prostředky, jako jsou virtuální počítače (VM) a další produkty. Další informace najdete v tématu [ceny virtuální sítě](https://azure.microsoft.com/pricing/details/virtual-network/) a [cenová kalkulačka](https://azure.microsoft.com/pricing/calculator/)Azure .

## <a name="next-steps"></a>Další kroky

 Pokud chcete začít používat virtuální síť, nějakou vytvořte, nasaďte do ní několik virtuálních počítačů a navažte komunikaci mezi těmito virtuálními počítači. Informace o postupu najdete v rychlém startu [Vytvoření virtuální sítě](quick-create-portal.md).
