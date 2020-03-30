---
title: Řešení Azure VMware od CloudSimple – připojení privátního cloudu k síti Azure pomocí ExpressRoute
description: Popisuje, jak připojit privátní cloudové prostředí CloudSimple k virtuální síti Azure pomocí ExpressRoute.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9bb68ec68f4de646239477ceeaac50a7a33989fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77015231"
---
# <a name="connect-your-cloudsimple-private-cloud-environment-to-the-azure-virtual-network-using-expressroute"></a>Připojení privátního cloudového prostředí CloudSimple k virtuální síti Azure pomocí ExpressRoute

Váš cloudový privátní cloud se dá připojit k virtuální síti Azure pomocí Azure ExpressRoute.  Toto připojení s vysokou šířkou pásma a nízkou latencí umožňuje přístup ke službám spuštěných ve vašem předplatném Azure z vašeho prostředí Privátního cloudu.

Připojení virtuální sítě umožňuje:

* Azure můžete použít jako cíl zálohování pro virtuální počítače ve vašem privátním cloudu.
* Nasaďte servery KMS ve svém předplatném Azure a šifrujte úložiště dat Private Cloud vSAN.
* Používejte hybridní aplikace, kde webová vrstva aplikace běží ve veřejném cloudu, zatímco vrstvy aplikací a databází běží ve vašem privátním cloudu.

![Připojení K virtuální síti Azure ExpressRoute](media/cloudsimple-azure-network-connection.png)

## <a name="set-up-a-virtual-network-connection"></a>Nastavení připojení k virtuální síti

K nastavení připojení virtuální sítě k privátnímu cloudu potřebujete autorizační klíč, identifikátor URI okruhu rovnocenných připojení a přístup k předplatnému Azure. Tyto informace jsou k dispozici na stránce Připojení k virtuální síti na portálu CloudSimple. Pokyny najdete v [tématu Získání informací o partnerské síti pro virtuální síť Azure připojení CloudSimple](virtual-network-connection.md). Máte-li potíže se získáním informací, odešlete <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">žádost o podporu</a>.

> [!TIP]
> If you already have a Azure virtual network, gateway subnet, and virtual network gateway, you can skip to step 4.

1. Vytvořte virtuální síť v rámci předplatného Azure a ověřte, že adresní prostor, který vyberete, se liší od adresního prostoru vašeho privátního cloudu.  Pokud už máte virtuální síť Azure, můžete použít existující.  Podrobnosti najdete [v tématu Vytvoření virtuální sítě pomocí portálu Azure](../virtual-network/quick-create-portal.md).
2. Vytvořte podsíť brány ve virtuální síti Azure.  Pokud už máte podsíť brány ve virtuální síti Azure, můžete použít stávající. Podrobnosti naleznete [v tématu Vytvoření podsítě brány](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).
3. Vytvořte bránu virtuální sítě ve virtuální síti.  Pokud máte existující bránu virtuální sítě, můžete použít existující bránu. Podrobnosti najdete [v tématu Vytvoření brány virtuální sítě](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway).
4. Vytvořte připojení mezi virtuální sítí a privátním cloudem uplatněním autorizačního klíče, jak je popsáno v [tématu Připojení virtuální sítě k okruhu – jinému předplatnému](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

> [!WARNING]
> Pokud používáte existující bránu virtuální sítě a má připojení ExpressRoute do stejného umístění jako okruh CloudSimple ExpressRoute, připojení nebude navázáno.  Vytvořte novou virtuální síť a postupujte podle předchozích kroků.

## <a name="test-the-virtual-network-connection"></a>Testování připojení k virtuální síti

Po vytvoření připojení můžete zkontrolovat stav připojení výběrem **možnosti Vlastnosti** v části **Nastavení**.  Stav a zřizování stát by měl zobrazit **úspěšné**.

![Stav připojení](media/azure-expressroute-connection.png)

Testování připojení k virtuální síti:

1. Vytvořte virtuální počítač ve svém předplatném Azure.
2. Najděte IP adresu vašeho private cloudu vCenter (viz uvítací e-mail).
3. Ping vašeho Cloud vCenter z virtuálního počítače vytvořeného ve vaší virtuální síti Azure.
4. Ping vašeho virtuálního počítače Azure z virtuálního počítače spuštěného ve vašem privátním cloudu vCenter.

Pokud máte nějaké problémy s navázáním připojení, odešlete <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">žádost o podporu</a>.
