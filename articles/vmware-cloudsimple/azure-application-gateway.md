---
title: Použití aplikační brány Azure s virtuálními počítači VMware
description: Popisuje, jak používat aplikační bránu Azure ke správě příchozího webového provozu pro webové servery spuštěné ve virtuálních počítačích VMware, vyhrává prostředí CloudSimple Private Cloud.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2cbfdd358fdfd5403c677c067376142169cdc6bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77015452"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-cloudsimple-private-cloud-environment"></a>Použití aplikační brány Azure s virtuálními počítači VMware v prostředí CloudSimple Privátního cloudu

Azure Application Gateway můžete použít ke správě příchozího webového provozu pro vaše webové servery spuštěné ve virtuálních počítačích VMware v prostředí CloudSimple Private Cloud.

Využitím Azure Application Gateway v hybridním nasazení veřejného a soukromého sektoru můžete spravovat webový provoz do vašich aplikací, poskytovat zabezpečené front-endy a offload SSL zpracování pro jejich služby spuštěné v prostředí VMware. Azure Application Gateway směruje příchozí webový provoz do back-endového fondu instancí, které se v prostředích VMware ubytovají podle nakonfigurovaných pravidel a sond stavu.

Toto řešení Azure Application Gateway vyžaduje:

* Mít předplatné Azure.
* Vytvořte a nakonfigurujte virtuální síť Azure a podsíť v rámci virtuální sítě.
* Vytvořte a nakonfigurujte pravidla skupiny zabezpečení sítě a přepojujte virtuální síť pomocí expressroute do privátního cloudu CloudSimple.
* Vytvořte & Konfigurace privátního cloudu.
* Vytvořte & Konfigurace aplikační brány Azure.

## <a name="azure-application-gateway-deployment-scenario"></a>Scénář nasazení Azure Application Gateway

V tomto scénáři azure aplikační brána běží ve vaší virtuální síti Azure. Virtuální síť je připojena k privátnímu cloudu přes okruh ExpressRoute. Všechny podsítě v privátním cloudu jsou IP dosažitelné z podsítí virtuální sítě.

![Nástroj pro vyrovnávání zatížení Azure ve virtuální síti Azure](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>Jak nasadit řešení

Proces nasazení se skládá z následujících úkolů:

1. [Ověření, zda jsou splněny požadavky](#1-verify-prerequisites)
2. [Připojení virtuálního připojení Azure k privátnímu cloudu](#2-connect-your-azure-virtual-network-to-your-private-cloud)
3. [Nasazení aplikační brány Azure](#3-deploy-an-azure-application-gateway)
4. [Vytvoření a konfigurace fondu virtuálních aplikací webového serveru v privátním cloudu](#4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud)

## <a name="1-verify-prerequisites"></a>1. Ověření požadavků

Ověřte, zda jsou tyto požadavky splněny:

* Správce prostředků Azure a virtuální síť se už vytvoří.
* Vyhrazená podsíť (pro aplikační bránu) v rámci virtuální sítě Azure je už vytvořená.
* CloudSimple Privátní cloud je již vytvořen.
* Neexistuje žádný konflikt IP mezi podsítěmi IP ve virtuální síti a podsítěmi v privátním cloudu.

## <a name="2-connect-your-azure-virtual-network-to-your-private-cloud"></a>2. Připojení virtuální sítě Azure k privátnímu cloudu

Pokud chcete virtuální síť Azure připojit k privátnímu cloudu, postupujte podle tohoto postupu.

1. [Na portálu CloudSimple zkopírujte informace o partnerskému vztahu ExpressRoute](virtual-network-connection.md).

2. [Konfigurace brány virtuální sítě pro vaši virtuální síť Azure](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).

3. [Propojte virtuální síť s okruhem CloudSimple ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

4. [Informace o partnerském vztahu, které jste zkopírovali, použijte k propojení virtuální sítě s okruhem ExpressRoute](virtual-network-connection.md).

## <a name="3-deploy-an-azure-application-gateway"></a>3. Nasazení aplikační brány Azure

Podrobné pokyny k tomu jsou k dispozici v [části Vytvoření aplikační brány s pravidly směrování na základě cesty pomocí portálu Azure](../application-gateway/create-url-route-portal.md). Zde je přehled požadovaných kroků:

1. Vytvořte virtuální síť ve skupině předplatného a prostředků.
2. Vytvořte podsíť (pro použití jako vyhrazená podsíť) v rámci virtuální sítě.
3. Vytvořte standardní aplikační bránu (volitelně povolte WAF): Na domovské stránce portálu Azure klikněte na**Networking** > **Brána aplikací** **sítě prostředků** > z levé horní části stránky. Vyberte standardní skladovou položku a velikost a poskytněte informace o předplatném Azure, skupině prostředků a umístění. V případě potřeby vytvořte novou veřejnou IP adresu pro tuto aplikační bránu a zadejte podrobnosti o virtuální síti a vyhrazené podsíti pro aplikační bránu.
4. Přidejte back-endový fond s virtuálními počítači a přidejte ho do brány aplikace.

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud"></a>4. Vytvoření a konfigurace fondu virtuálních aplikací webového serveru v privátním cloudu

V aplikaci vCenter vytvářejte virtuální počítače pomocí operačního systému a webového serveru podle vašeho výběru (například Windows/IIS nebo Linux/Apache). Zvolte podsíť/Síť VLAN, která je určena pro webovou vrstvu ve vašem privátním cloudu. Ověřte, že alespoň jeden virtuální počítač virtuálních počítačích webového serveru je v podsíti webové vrstvy.
