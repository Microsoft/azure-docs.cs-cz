---
title: Použití Azure Application Gateway s virtuálními počítači VMware
description: Popisuje, jak pomocí služby Azure Application Gateway spravovat příchozí webový provoz na webových serverech, které běží na virtuálních počítačích VMware, do prostředí privátního cloudu CloudSimple.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d14fdc4e2b2b1dfca8194077acc0453808611946
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97897939"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-cloudsimple-private-cloud-environment"></a>Použití Azure Application Gateway s virtuálními počítači VMware v prostředí privátního cloudu CloudSimple

Azure Application Gateway můžete použít ke správě příchozího webového provozu pro webové servery běžící na virtuálních počítačích VMware v rámci vašeho privátního cloudového prostředí CloudSimple.

Díky využití Azure Application Gateway v hybridním nasazení veřejného privátního prostředí můžete spravovat webový provoz do vašich aplikací, poskytovat zabezpečený front-end a přesměrovat zpracování TLS pro své služby běžící v prostředí VMware. Azure Application Gateway směruje příchozí webový provoz do instancí back-endu, které se nacházejí v prostředí VMware, podle nakonfigurovaných pravidel a sond stavu.

Toto řešení Application Gateway Azure vyžaduje:

* Mít předplatné Azure.
* Vytvořte a nakonfigurujte virtuální síť Azure a podsíť v rámci virtuální sítě.
* Vytváření a konfigurace pravidel NSG a partnerských virtuálních sítí pomocí ExpressRoute k privátnímu cloudu CloudSimple.
* Vytvoření & konfigurace privátního cloudu.
* Vytvořte & nakonfigurujte Application Gateway Azure.

## <a name="azure-application-gateway-deployment-scenario"></a>Scénář nasazení Azure Application Gateway

V tomto scénáři se Azure Application Gateway spouští ve vaší virtuální síti Azure. Virtuální síť je připojená k privátnímu cloudu přes okruh ExpressRoute. Všechny podsítě v privátním cloudu jsou IP adresy dosažitelné z podsítí virtuální sítě.

![Azure Load Balancer ve službě Azure Virtual Network](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>Jak nasadit řešení

Proces nasazení se skládá z následujících úloh:

1. [Ověření splnění požadavků](#1-verify-prerequisites)
2. [Připojení virtuálního připojení Azure k privátnímu cloudu](#2-connect-your-azure-virtual-network-to-your-private-cloud)
3. [Nasazení služby Azure Application Gateway](#3-deploy-an-azure-application-gateway)
4. [Vytvoření a konfigurace fondu virtuálních počítačů webového serveru v privátním cloudu](#4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud)

## <a name="1-verify-prerequisites"></a>1. Ověření požadovaných součástí

Ověřte, že jsou splněné tyto požadavky:

* Azure Resource Manager a virtuální síť už je vytvořená.
* Vyhrazená podsíť (pro Application Gateway) v rámci vaší virtuální sítě Azure je už vytvořená.
* Privátní cloud CloudSimple je už vytvořený.
* Mezi podsítěmi IP ve virtuální síti a podsítěmi v privátním cloudu nedochází ke konfliktu IP adres.

## <a name="2-connect-your-azure-virtual-network-to-your-private-cloud"></a>2. Připojte svoji virtuální síť Azure k privátnímu cloudu

Pokud chcete připojit virtuální síť Azure k privátnímu cloudu, postupujte podle tohoto postupu.

1. [Na portálu CloudSimple zkopírujte informace o partnerském vztahu ExpressRoute](virtual-network-connection.md).

2. [Nakonfigurujte bránu virtuální sítě pro vaši virtuální síť Azure](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).

3. [Připojte virtuální síť k okruhu ExpressRoute CloudSimple](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

4. [Pomocí informací o partnerském vztahu, které jste zkopírovali k propojení virtuální sítě s okruhem ExpressRoute](virtual-network-connection.md).

## <a name="3-deploy-an-azure-application-gateway"></a>3. nasazení služby Azure Application Gateway

Podrobné pokyny k tomuto postupu jsou k dispozici v tématu [Vytvoření aplikační brány s pravidly směrování na základě cesty pomocí Azure Portal](../application-gateway/create-url-route-portal.md). Tady je souhrn požadovaných kroků:

1. Vytvořte ve svém předplatném a skupině prostředků virtuální síť.
2. Vytvořte podsíť (která se má použít jako vyhrazená podsíť) v rámci vaší virtuální sítě.
3. Vytvořit standardní Application Gateway (volitelně povolit WAF): na domovské stránce Azure Portal klikněte na **prostředky**  >    >  **Application Gateway** sítě v levém horním rohu stránky. Vyberte standardní SKU a velikost a zadejte předplatné Azure, informace o skupině prostředků a umístění. V případě potřeby vytvořte novou veřejnou IP adresu pro tuto aplikační bránu a zadejte podrobnosti o virtuální síti a vyhrazené podsíti pro aplikační bránu.
4. Přidejte back-end fond s virtuálními počítači a přidejte ho do aplikační brány.

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud"></a>4. vytvoření a konfigurace fondu virtuálních počítačů webového serveru v privátním cloudu

V vCenter vytvořte virtuální počítače s operačním systémem a webovým serverem podle vašeho výběru (například Windows/IIS nebo Linux/Apache). Vyberte podsíť/síť VLAN, která je určená pro webovou vrstvu ve vašem privátním cloudu. Ověřte, že minimálně jeden vNIC virtuálních počítačů webového serveru je na podsíti webové vrstvy.
