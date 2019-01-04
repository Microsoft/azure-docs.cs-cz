---
title: Azure Virtual Network | Microsoft Docs
description: Seznamte se s koncepty a funkcemi služby Azure Virtual Network.
services: virtual-network
documentationcenter: na
author: jimdial
tags: azure-resource-manager
Customer intent: As someone with a basic network background that is new to Azure, I want to understand the capabilities of Azure Virtual Network, so that my Azure resources such as VMs, can securely communicate with each other, the internet, and my on-premises resources.
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/12/2018
ms.author: jdial
ms.openlocfilehash: 9fb6aa0c2bf585862f61d7c78bd09b340ff8a3ce
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54015895"
---
# <a name="what-is-azure-virtual-network"></a>Co je Azure Virtual Network?

Azure Virtual Network umožňuje řadě typů prostředků Azure, jako jsou virtuální počítače Azure, zabezpečeně komunikovat mezi sebou, s internetem a s místními sítěmi. Virtuální síť má obor do jedné oblasti; ale více virtuálních sítí z různých oblastí může být propojeny pomocí partnerské vztahy virtuálních sítí.

Azure Virtual Network poskytuje následující klíčové funkce:

## <a name="isolation-and-segmentation"></a>Izolace a segmentace

V každém [předplatném](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) Azure a v každé [oblasti](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) Azure můžete implementovat několik virtuálních sítí. Všechny virtuální sítě jsou mezi sebou izolované. Každá virtuální síť umožňuje:
- Zadání vlastního adresního prostoru privátních IP adres pomocí veřejných a privátních (RFC 1918) adres. Azure přiřazuje prostředkům ve virtuální síti privátní IP adresy z přiřazeného adresního prostoru.
- Segmentaci virtuální sítě do jedné nebo několika podsítí a přidělení části adresního prostoru virtuální sítě ke každé podsíti.
- Použití překladu adres, který poskytuje Azure, nebo zadání vlastního serveru DNS, který budou používat prostředky ve virtuální síti.

## <a name="communicate-with-the-internet"></a>Komunikace s internetem

Všechny prostředky ve virtuální síti ve výchozím nastavení umožňují odchozí komunikaci s internetem. Příchozí komunikaci s prostředkem můžete umožnit tím, že prostředku přiřadíte veřejnou IP adresu nebo veřejný Load Balancer. Veřejnou IP adresu nebo veřejný Load Balancer můžete použít také ke správě odchozích připojení.  Další informace o odchozích připojeních v Azure najdete v tématech [Odchozí připojení](../load-balancer/load-balancer-outbound-connections.md), [Veřejné IP adresy](virtual-network-public-ip-address.md) a [Load Balancer](../load-balancer/load-balancer-overview.md).

>[!NOTE]
>Pokud používáte pouze interní [Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md), odchozí připojení nebudou dostupná, dokud neurčíte, jak mají [odchozí připojení](../load-balancer/load-balancer-outbound-connections.md) pracovat s veřejnou IP adresou nebo veřejným Load Balancerem na úrovni instance.

## <a name="communicate-between-azure-resources"></a>Komunikace mezi prostředky Azure

Prostředky Azure mezi sebou zabezpečeně komunikují jedním z následujících způsobů:

- **Prostřednictvím virtuální sítě**: Virtuální počítače a několik dalších typů prostředků Azure můžete nasadit do virtuální sítě, jako je například Azure App Service Environment, Azure Kubernetes Service (AKS) a Azure Virtual Machine Scale Sets. Úplný seznam prostředků Azure, které můžete nasadit do virtuální sítě, najdete v tématu věnovaném [integraci virtuální sítě do služeb](virtual-network-for-azure-services.md). 
- **Prostřednictvím koncového bodu služby virtuální sítě**: Rozšiřují privátní adresní prostor vaší virtuální sítě a identitu vaší virtuální síti a prostředky služeb Azure, jako jsou účty Azure Storage a databáze Azure SQL přes přímé připojení. Koncové body služeb umožňují svázat vaše důležité prostředky služeb Azure pouze s virtuální sítí. Další informace najdete v tématu [Přehled koncových bodů služeb virtuální sítě](virtual-network-service-endpoints-overview.md).
 
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

## <a name="connect-virtual-networks"></a>Připojení virtuálních sítí

Pomocí partnerského vztahu virtuálních sítí můžete propojit virtuální sítě mezi sebou a tím umožnit vzájemnou komunikaci prostředků v obou virtuálních sítích. Propojené virtuální sítě se můžou nacházet ve stejné oblasti Azure nebo v různých oblastech. Další informace najdete v tématu [Partnerský vztah virtuálních sítí](virtual-network-peering-overview.md).

## <a name="next-steps"></a>Další postup

Teď máte přehled o službě Azure Virtual Network. Pokud chcete začít používat virtuální síť, nějakou vytvořte, nasaďte do ní několik virtuálních počítačů a navažte komunikaci mezi těmito virtuálními počítači. Informace o postupu najdete v rychlém startu [Vytvoření virtuální sítě](quick-create-portal.md).
