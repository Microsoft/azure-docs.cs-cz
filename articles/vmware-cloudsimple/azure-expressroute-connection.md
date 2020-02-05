---
title: Řešení Azure VMware (AVS) – připojení privátního cloudu služby AVS k síti Azure pomocí ExpressRoute
description: Popisuje postup připojení vašeho prostředí privátního cloudu služby AVS ke službě Azure Virtual Network pomocí ExpressRoute.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3d487794e219f63150142db8df4b0c1abf112947
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015231"
---
# <a name="connect-your-avs-private-cloud-environment-to-the-azure-virtual-network-using-expressroute"></a>Připojení prostředí privátního cloudu služby AVS k virtuální síti Azure pomocí ExpressRoute

Váš privátní cloud služby AVS můžete připojit k virtuální síti Azure pomocí Azure ExpressRoute. Tato velká šířka pásma, připojení s nízkou latencí umožňuje přístup ke službám běžícím v předplatném Azure z vašeho privátního cloudového prostředí služby AVS.

Připojení k virtuální síti vám umožní:

* Pro virtuální počítače v privátním cloudu služby AVS použijte Azure jako cíl zálohování.
* Nasaďte servery služby správy klíčů v předplatném Azure pro šifrování úložiště dat síti vSAN privátního cloudu služby AVS.
* Použijte hybridní aplikace, ve kterých se webová vrstva aplikace spouští ve veřejném cloudu, zatímco vrstvy aplikace a databáze běží ve vašem privátním cloudu služby AVS.

![Připojení Azure ExpressRoute k virtuální síti](media/cloudsimple-azure-network-connection.png)

## <a name="set-up-a-virtual-network-connection"></a>Nastavit připojení k virtuální síti

K nastavení připojení virtuální sítě k privátnímu cloudu služby AVS potřebujete svůj autorizační klíč, identifikátor URI partnerského okruhu a přístup k vašemu předplatnému Azure. Tyto informace jsou k dispozici na stránce Virtual Network připojení na portálu AVS. Pokyny najdete v tématu [získání informací o partnerském vztahu pro virtuální síť Azure a připojení k funkci AVS](virtual-network-connection.md). Pokud máte potíže s získáním informací, odešlete <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">žádost o podporu</a>.

> [!TIP]
> Pokud už máte virtuální síť Azure, podsíť brány a bránu virtuální sítě, můžete přejít ke kroku 4.

1. Vytvořte ve svém předplatném Azure virtuální síť a ověřte, jestli se vybraný adresní prostor liší od adresního prostoru vašeho privátního cloudu služby AVS. Pokud už máte virtuální síť Azure, můžete použít tu existující. Podrobnosti najdete v tématu [vytvoření virtuální sítě pomocí Azure Portal](../virtual-network/quick-create-portal.md).
2. Vytvořte podsíť brány ve službě Azure Virtual Network. Pokud už máte podsíť brány ve službě Azure Virtual Network, můžete použít stávající. Podrobnosti najdete v tématu [Vytvoření podsítě brány](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).
3. Vytvořte bránu virtuální sítě ve virtuální síti. Pokud máte existující bránu virtuální sítě, můžete použít stávající. Podrobnosti najdete v tématu [Vytvoření brány virtuální sítě](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway).
4. Vytvořte připojení mezi vaší virtuální sítí a privátním cloudem služby AVS pomocí uplatnění autorizačního klíče, jak je popsáno v tématu [připojení virtuální sítě k jinému okruhu](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

> [!WARNING]
> Pokud používáte existující bránu virtuální sítě a má připojení ExpressRoute ke stejnému umístění jako okruh služby AVS ExpressRoute, připojení se nevytvoří. Vytvořte novou virtuální síť a postupujte podle předchozích kroků.

## <a name="test-the-virtual-network-connection"></a>Test připojení k virtuální síti

Po vytvoření připojení můžete stav připojení ověřit tak, že v části **Nastavení**vyberete **vlastnosti** . Stav a stav zřizování by se měl zobrazit jako **úspěšný**.

![Stav připojení](media/azure-expressroute-connection.png)

Test připojení k virtuální síti:

1. Vytvořte virtuální počítač ve svém předplatném Azure.
2. Najděte si IP adresu vašeho privátního cloudu služby AVS (vaší uvítací e-mail).
3. Pomocí příkazů otestujete svůj Cloud vCenter z virtuálního počítače vytvořeného ve vaší virtuální síti Azure.
4. Otestujte virtuální počítač Azure na virtuálním počítači, který běží ve vašem privátním cloudovém vCenter služby AVS.

Pokud máte nějaké problémy s navázáním připojení, odešlete <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">žádost o podporu</a>.
