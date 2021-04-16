---
title: Dokumentace k síťové architektuře Azure
description: Přečtěte si informace o dokumentaci k referenční architektuře dostupné pro síťové služby Azure.
services: networking
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.date: 03/30/2021
ms.author: kumud
ms.openlocfilehash: c98bdbb9fba2a6ba01e4ce590c36d57e68390f17
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484791"
---
# <a name="azure-networking-architecture-documentation"></a>Dokumentace k síťové architektuře Azure

Tento článek poskytuje informace o průvodcích architektury, které vám pomůžou prozkoumat různé síťové služby v Azure, které máte k dispozici pro vytváření aplikací.

## <a name="networking-overview"></a>Přehled sítí

Následující tabulka obsahuje články, které poskytují přehled o sítích pro virtuální datové centrum a topologii hvězdicové topologie v Azure.

|Nadpis |Popis  |
|---------|---------|
|[Virtuální datová centra](/azure/architecture/vdc/networking-virtual-datacenter)   | Poskytuje perspektivu sítě pro virtuální datové centrum v Azure.       |
|[Topologie centrum – paprsky](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)  |Poskytuje přehled síťové topologie centra a paprsků v Azure spolu s informacemi o omezeních předplatného a několika rozbočovačích.          |

## <a name="connect-to-azure-resources"></a>Připojení k prostředkům Azure

Následující tabulka obsahuje články o síťových službách Azure, které poskytují konektivitu mezi prostředky Azure, připojením z místní sítě k prostředkům Azure a větví k připojení větví v Azure.

|Nadpis |Popis  |
|---------|---------|
|[Přidat adresní prostory IP adres do partnerských virtuálních sítí](/azure/architecture/networking/prefixes/add-ip-space-peered-vnet)     | Poskytuje skripty, které vám pomůžou přidat adresní prostory IP adres do partnerských virtuálních sítí.        |
|[Připojení samostatných serverů pomocí síťového adaptéru Azure](/azure/architecture/hybrid/azure-network-adapter)   | Ukazuje, jak připojit místní samostatný server k Microsoft Azure virtuální sítě pomocí síťového adaptéru Azure, který nasazujete prostřednictvím centra pro správu systému Windows.        |
|[Volba mezi partnerským vztahem virtuální sítě a bránami sítě VPN](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering)   | Porovná dva způsoby, jak propojit virtuální sítě v Azure: partnerské vztahy virtuálních sítí a brány VPN.        |
|[Připojení místní sítě k Azure](/azure/architecture/reference-architectures/hybrid-networking/)  | Porovná možnosti připojení místní sítě k Azure Virtual Network (VNet). Pro každou z možností je k dispozici podrobnější referenční architektura.        |
|[Architektura připojení SD-WAN s Azure Virtual WAN](../../virtual-wan/sd-wan-connectivity-architecture.md)|Popisuje různé možnosti připojení pro propojení privátního softwaru definovaného sítě WAN (SD-WAN) s Azure Virtual WAN.|

## <a name="deploy-highly-available-applications"></a>Nasazení vysoce dostupných aplikací

Následující tabulka obsahuje články, které popisují, jak nasadit aplikace pro zajištění vysoké dostupnosti pomocí kombinace síťových služeb Azure.

|Nadpis |Popis  |
|---------|---------|
|[N-vrstvá aplikace ve více oblastech](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)  | Popisuje N-vrstvou aplikaci, která používá Traffic Manager ke směrování příchozích požadavků do primární oblasti a v případě, že tato oblast nebude k dispozici, Traffic Manager převzetí služeb při selhání sekundární oblastí.      |
| [SaaS pro více tenantů v Azure](https://docs.microsoft.com/azure/architecture/example-scenario/multi-saas/multitenant-saas)       |   Používá víceklientské řešení, které obsahuje kombinaci front-dveří a Application Gateway.  Přední dveře pomáhají vyrovnávat zatížení napříč různými oblastmi a Application Gateway trasy a provoz vyrovnává zatížení interně v aplikaci do různých služeb, které odpovídají obchodním potřebám klienta.  |
| [Vícevrstvá webová aplikace vytvořená pro zajištění vysoké dostupnosti a zotavení po havárii ](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/multi-tier-app-disaster-recovery)        |      Nasadí odolné vícevrstvé aplikace vytvořené pro vysokou dostupnost a zotavení po havárii. Pokud bude primární oblast nedostupná, Traffic Manager převzít služeb při selhání do sekundární oblasti.  |
|[IaaS: webová aplikace s relační databází](/azure/architecture/high-availability/ref-arch-iaas-web-and-db)    |   Popisuje, jak používat prostředky rozdělené do několika zón, aby poskytovala architekturu vysoké dostupnosti pro hostování webové aplikace infrastruktury jako služby (IaaS) a databáze SQL Server.     |
|[Sdílení umístění v reálném čase s využitím nízkonákladových bezserverových služeb Azure](/azure/architecture/example-scenario/signalr/#azure-front-door)       |   Používá přední dvířka Azure k zajištění vyšší dostupnosti pro vaše aplikace než nasazení do jedné oblasti. Pokud oblastní výpadek ovlivní primární oblast, můžete pomocí služby Front Door převzít služby při selhání sekundární oblastí.      |
|[Vysoce dostupná virtuální síťová zařízení](/azure/architecture/reference-architectures/dmz/nva-ha)     | Ukazuje, jak nasadit sadu síťových virtuálních zařízení (síťová virtuální zařízení) pro zajištění vysoké dostupnosti v Azure.        |
|[Vyrovnávání zatížení ve více oblastech pomocí Traffic Manager a Application Gateway](/azure/architecture/high-availability/reference-architecture-traffic-manager-application-gateway)     | Popisuje, jak nasadit odolné vícevrstvé aplikace v několika oblastech Azure, aby bylo možné dosáhnout dostupnosti a robustní infrastruktury pro zotavení po havárii.        |

## <a name="secure-your-network-resources"></a>Zabezpečení síťových prostředků

Následující tabulka obsahuje články, které popisují, jak chránit síťové prostředky pomocí síťových služeb Azure.

|Nadpis |Popis  |
|---------|---------|
|[Osvědčené postupy zabezpečení sítě](../../security/fundamentals/network-best-practices.md) |Popisuje shromažďování osvědčených postupů Azure pro vylepšení zabezpečení sítě.         |
[Průvodce architekturou Azure Firewallu](/azure/architecture/example-scenario/firewalls/) | Poskytuje strukturovaný přístup pro vytváření vysoce dostupných bran firewall v Azure s využitím virtuálních zařízení třetích stran.        |
|[Implementace zabezpečené hybridní sítě](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)     | Popisuje architekturu, která implementuje DMZ (označované také jako hraniční síť) mezi místní sítí a virtuální sítí Azure. Veškerý příchozí a odchozí provoz projde přes Azure Firewall.        |
|[Zabezpečené a řízené úlohy se segmentací na úrovni sítě](/azure/architecture/reference-architectures/hybrid-networking/network-level-segmentation) | Popisuje tři běžné vzory, které se používají k uspořádání úloh v Azure z hlediska sítě.   Každý z těchto vzorů poskytuje jiný typ izolace a připojení.      |
|[Brána firewall a Application Gateway pro virtuální sítě](/azure/architecture/example-scenario/gateway/firewall-application-gateway) | Popisuje služby Azure Virtual Network Security, jako je Azure Firewall a Azure Application Gateway, kdy použít každou službu a možnosti návrhu sítě, které kombinují obojí.      |

## <a name="next-steps"></a>Další kroky

Přečtěte si o [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md).
