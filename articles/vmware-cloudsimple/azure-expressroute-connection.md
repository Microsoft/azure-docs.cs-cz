---
title: Řešení Azure VMware podle CloudSimple – připojení privátního cloudu k síti Azure pomocí ExpressRoute
description: Popisuje postup připojení privátního cloudového prostředí CloudSimple k virtuální síti Azure pomocí ExpressRoute.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 98eec6b48d2b02c6eb7f6faea38994a9176bcf87
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97895984"
---
# <a name="connect-your-cloudsimple-private-cloud-environment-to-the-azure-virtual-network-using-expressroute"></a>Připojení prostředí privátního cloudu CloudSimple k virtuální síti Azure pomocí ExpressRoute

Privátní cloud CloudSimple může být připojen ke službě Azure Virtual Network pomocí Azure ExpressRoute.  Tato velká šířka pásma, připojení s nízkou latencí umožňuje přístup ke službám běžícím v předplatném Azure z vašeho privátního cloudového prostředí.

Připojení k virtuální síti vám umožní:

* Použijte Azure jako cíl zálohování pro virtuální počítače ve vašem privátním cloudu.
* Nasaďte servery služby správy klíčů v předplatném Azure pro šifrování úložiště dat privátního cloudu síti vSAN.
* Použijte hybridní aplikace, ve kterých se webová vrstva aplikace spouští ve veřejném cloudu, zatímco vrstvy aplikace a databáze běží ve vašem privátním cloudu.

![Připojení Azure ExpressRoute k virtuální síti](media/cloudsimple-azure-network-connection.png)

## <a name="set-up-a-virtual-network-connection"></a>Nastavit připojení k virtuální síti

Pokud chcete nastavit připojení k virtuální síti k privátnímu cloudu, budete potřebovat autorizační klíč, identifikátor URI partnerského okruhu a přístup k vašemu předplatnému Azure. Tyto informace jsou k dispozici na stránce Virtual Network připojení na portálu CloudSimple. Pokyny najdete v tématu [získání informací o partnerském vztahu pro službu Azure Virtual Network do CloudSimple připojení](virtual-network-connection.md). Pokud máte potíže s získáním informací, odešlete <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">žádost o podporu</a>.

> [!TIP]
> Pokud už máte virtuální síť Azure, podsíť brány a bránu virtuální sítě, můžete přejít ke kroku 4.

1. Vytvořte ve svém předplatném Azure virtuální síť a ověřte, jestli se vybraný adresní prostor liší od adresního prostoru vašeho privátního cloudu.  Pokud už máte virtuální síť Azure, můžete použít tu existující.  Podrobnosti najdete v tématu [vytvoření virtuální sítě pomocí Azure Portal](../virtual-network/quick-create-portal.md).
2. Vytvořte podsíť brány ve službě Azure Virtual Network.  Pokud už máte podsíť brány ve službě Azure Virtual Network, můžete použít stávající. Podrobnosti najdete v tématu [Vytvoření podsítě brány](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-gateway-subnet).
3. Vytvořte bránu virtuální sítě ve virtuální síti.  Pokud máte existující bránu virtuální sítě, můžete použít stávající. Podrobnosti najdete v tématu [Vytvoření brány virtuální sítě](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway).
4. Pomocí autorizačního klíče, který je popsaný v tématu [připojení virtuální sítě k jinému okruhu](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription), vytvořte propojení mezi vaší virtuální sítí a privátním cloudem.

> [!WARNING]
> Pokud používáte existující bránu virtuální sítě a má připojení ExpressRoute ke stejnému umístění jako okruh ExpressRoute CloudSimple, připojení se nevytvoří.  Vytvořte novou virtuální síť a postupujte podle předchozích kroků.

## <a name="test-the-virtual-network-connection"></a>Test připojení k virtuální síti

Po vytvoření připojení můžete stav připojení ověřit tak, že v části **Nastavení** vyberete **vlastnosti** .  Stav a stav zřizování by se měl zobrazit jako **úspěšný**.

![Stav připojení](media/azure-expressroute-connection.png)

Test připojení k virtuální síti:

1. Vytvořte virtuální počítač ve svém předplatném Azure.
2. Vyhledejte IP adresu vašeho privátního cloudu vCenter (podívejte se na uvítací e-mail).
3. Pomocí příkazů otestujete svůj Cloud vCenter z virtuálního počítače vytvořeného ve vaší virtuální síti Azure.
4. Otestujte virtuální počítač Azure z virtuálního počítače spuštěného ve vašem privátním cloudu vCenter.

Pokud máte nějaké problémy s navázáním připojení, odešlete <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">žádost o podporu</a>.
