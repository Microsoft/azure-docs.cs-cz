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
ms.openlocfilehash: 22c1e3050915fc697a62862620ef492ef22f80b8
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542836"
---
# <a name="what-is-azure-virtual-network"></a>Co je Azure Virtual Network?

Azure Virtual Network (VNet) je základním stavebním blokem vaší privátní sítě v Azure. Virtuální síť umožňuje řadě typů prostředků Azure, jako jsou Azure Virtual Machines (VM), zabezpečeně komunikovat mezi sebou, internet a místní sítí. VNet je podobné jako u tradiční sítě, který bude pracovat ve svém vlastním datovém centru, ale přináší další výhody infrastruktury Azure, jako je například škálování, dostupnost a izolaci.

## <a name="vnet-concepts"></a>Koncepty virtuální sítě

- **Adresní prostor:** Při vytváření virtuální sítě, je nutné zadat vlastní privátní adresní prostor IP adres pomocí veřejných a privátních (RFC 1918) adres. Azure přiřazuje prostředkům ve virtuální síti privátní IP adresy z přiřazeného adresního prostoru. Například pokud nasadíte virtuální počítač ve virtuální síti s adresním prostorem, 10.0.0.0/16, virtuálnímu počítači přiřadí privátní IP adresa, jako je 10.0.0.4.
- **Podsítě:** Podsítě umožňují segmentaci virtuální sítě do jedné nebo více dílčích sítí a přidělení části adresního prostoru virtuální sítě ke každé podsíti. Pak můžete nasadit prostředky Azure v určité podsíti. Stejně jako u tradiční sítě, podsítě umožňují rozdělit adresní prostor vaší virtuální sítě do segmentů, které jsou vhodné pro interní síť organizace. To také zlepšuje efektivitu přidělení adresy. Můžete svázat prostředky v rámci podsítě pomocí skupin zabezpečení sítě. Další informace najdete v tématu [skupiny zabezpečení](security-overview.md).
- **Oblasti**: Virtuální síť je vymezen na jednu oblast nebo umístění; ale více virtuálních sítí z různých oblastí může být propojeny pomocí partnerské vztahy virtuálních sítí.
- **Předplatné:** Virtuální síť je vymezen na předplatné. V každém [předplatném](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) Azure a v každé [oblasti](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) Azure můžete implementovat několik virtuálních sítí.

## <a name="best-practices"></a>Osvědčené postupy

Během vytváření vaší sítě v Azure, je důležité mít na paměti následující univerzální MSDL:

- Ujistěte se překrývat adresní prostory. Ujistěte se, že se adresní prostor vaší virtuální sítě (blok CIDR) nemá překrývají s vaší organizací na jiné rozsahy adres sítě.
- Podsítě by neměl zahrnovat celým adresním prostorem virtuální sítě. Plánujte dopředu a vyhradit některé adresní prostor pro budoucnost.
- Doporučujeme, že abyste měli méně velkých virtuálních sítí než několik malých virtuálních sítí. To zabrání režie na správu.
- Zabezpečení virtuální sítě pomocí skupin zabezpečení sítě (Nsg).

## <a name="communicate-with-the-internet"></a>Komunikace s internetem

Všechny prostředky ve virtuální síti umožňují odchozí komunikaci na Internetu, ve výchozím nastavení. Příchozí komunikaci s prostředkem můžete umožnit tím, že prostředku přiřadíte veřejnou IP adresu nebo veřejný Load Balancer. Veřejnou IP adresu nebo veřejný Load Balancer můžete použít také ke správě odchozích připojení.  Další informace o odchozích připojeních v Azure najdete v tématech [Odchozí připojení](../load-balancer/load-balancer-outbound-connections.md), [Veřejné IP adresy](virtual-network-public-ip-address.md) a [Load Balancer](../load-balancer/load-balancer-overview.md).

>[!NOTE]
>Pokud používáte pouze interní [Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md), odchozí připojení nebudou dostupná, dokud neurčíte, jak mají [odchozí připojení](../load-balancer/load-balancer-outbound-connections.md) pracovat s veřejnou IP adresou nebo veřejným Load Balancerem na úrovni instance.

## <a name="communicate-between-azure-resources"></a>Komunikace mezi prostředky Azure

Prostředky Azure mezi sebou zabezpečeně komunikují jedním z následujících způsobů:

- **Prostřednictvím virtuální sítě**: Virtuální počítače a několik dalších typů prostředků Azure můžete nasadit do virtuální sítě, jako je například Azure App Service Environment, Azure Kubernetes Service (AKS) a Azure Virtual Machine Scale Sets. Úplný seznam prostředků Azure, které můžete nasadit do virtuální sítě, najdete v tématu věnovaném [integraci virtuální sítě do služeb](virtual-network-for-azure-services.md).
- **Prostřednictvím koncového bodu služby virtuální sítě**: Rozšiřují privátní adresní prostor vaší virtuální sítě a identitu vaší virtuální síti a prostředky služeb Azure, jako jsou účty Azure Storage a databáze Azure SQL přes přímé připojení. Koncové body služeb umožňují svázat vaše důležité prostředky služeb Azure pouze s virtuální sítí. Další informace najdete v tématu [Přehled koncových bodů služeb virtuální sítě](virtual-network-service-endpoints-overview.md).
- **Prostřednictvím partnerského vztahu virtuálních sítí**: Pomocí partnerského vztahu virtuálních sítí můžete propojit virtuální sítě mezi sebou a tím umožnit vzájemnou komunikaci prostředků v obou virtuálních sítích. Propojené virtuální sítě se můžou nacházet ve stejné oblasti Azure nebo v různých oblastech. Další informace najdete v tématu [Partnerský vztah virtuálních sítí](virtual-network-peering-overview.md).

## <a name="communicate-with-on-premises-resources"></a>Komunikace s místními prostředky

Své místní počítače a sítě můžete připojit k virtuální síti pomocí jakékoli kombinace následujících možností:

- **Point-to-site virtuální privátní sítě (VPN):** Vytváří se mezi virtuální sítí a jedním počítačem ve vaší síti. Každý počítač, který chcete navázat připojení k virtuální síti, musí toto připojení nakonfigurovat. Tento typ připojení je skvělý, pokud teprve začínáte s Azure, nebo pro vývojáře, protože nevyžaduje téměř nebo vůbec žádné změny vaší stávající sítě. Komunikace mezi počítačem a virtuální sítí se odesílá prostřednictvím šifrovaného tunelu přes internet. Další informace najdete v tématu popisujícím [síť VPN typu Point-to-Site](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#P2S).
- **Síť Site-to-site VPN:** Vytváří se mezi vaše místní zařízení VPN a službou Azure VPN Gateway, který je nasazený ve virtuální síti. Tento typ připojení povoluje přístup k virtuální síti všem místním prostředkům, které autorizujete. Komunikace mezi místním zařízením VPN a službou Azure VPN Gateway se odesílá prostřednictvím šifrovaného tunelu přes internet. Další informace najdete v tématu popisujícím [síť VPN typu Site-to-Site](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti).
- **Azure ExpressRoute:** Vytváří se mezi vaší sítí a Azure prostřednictvím partnera ExpressRoute. Toto připojení je soukromé. Provoz se nepřenáší přes internet. Další informace najdete v tématu popisujícím [ExpressRoute](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ExpressRoute).

## <a name="filter-network-traffic"></a>Filtrování provozu sítě

Síťový provoz mezi podsítěmi můžete filtrovat pomocí jedné nebo obou z následujících možností:

- **Skupiny zabezpečení:** Skupiny zabezpečení sítě a skupin zabezpečení aplikací může obsahovat více příchozích a odchozích pravidel zabezpečení, které umožňují filtrovat provoz do a z prostředků podle zdrojové a cílové IP adresy, portu a protokolu. Další informace najdete v tématu [skupiny zabezpečení sítě](security-overview.md#network-security-groups) nebo [skupiny zabezpečení aplikací](security-overview.md#application-security-groups).
- **Síťová virtuální zařízení:** Síťové virtuální zařízení je virtuální počítač, který provádí síťovou funkci, jako je například Brána firewall, optimalizace sítě WAN nebo jiná síťová funkce. Úplný seznam dostupných síťových virtuálních zařízení, která můžete nasadit do virtuální sítě, najdete na webu [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

## <a name="route-network-traffic"></a>Směrování provozu sítě

Azure ve výchozím nastavení směruje provoz mezi podsítěmi, propojenými virtuálními sítěmi, místními sítěmi a internetem. K přepsání výchozích tras, které Azure vytváří, můžete implementovat jednu nebo obě z následujících možností:

- **Směrovací tabulky:** Nemůžete vytvářet vlastní směrovací tabulky s trasami tento ovládací prvek, kde provoz se směruje do pro každou podsíť. Další informace o [směrovacích tabulkách](virtual-networks-udr-overview.md#user-defined).
- **Border gateway protocol (BGP) trasy:** Pokud připojíte virtuální síť k místní síti pomocí připojení k Azure VPN Gateway nebo ExpressRoute, můžete rozšířit místní trasy protokolu BGP s virtuálními sítěmi. Další informace o použití BGP se službou [Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange).

## <a name="azure-vnet-limits"></a>Omezení pro virtuální síť v Azure

Existují určitá omezení kolem počet prostředky Azure, které můžete nasadit. Omezení pro většinu služeb Azure sítě jsou na maximální hodnoty. Můžete však [zvýšit určitá omezení pro sítě](../azure-supportability/networking-quota-requests.md) uvedené na [virtuální sítě omezuje stránky](../azure-subscription-service-limits.md#networking-limits). 

## <a name="pricing"></a>Ceny

Neexistuje žádné poplatky za využívání virtuální síť Azure, je zdarma. Standardní poplatky jsou použitelné pro prostředky, jako jsou virtuální počítače (VM) a další produkty. Další informace najdete v tématu [VNet ceny](https://azure.microsoft.com/pricing/details/virtual-network/) a Azure [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/).

## <a name="next-steps"></a>Další postup

 Pokud chcete začít používat virtuální síť, nějakou vytvořte, nasaďte do ní několik virtuálních počítačů a navažte komunikaci mezi těmito virtuálními počítači. Informace o postupu najdete v rychlém startu [Vytvoření virtuální sítě](quick-create-portal.md).
