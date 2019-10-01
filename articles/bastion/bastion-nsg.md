---
title: Práce s virtuálními počítači a skupin zabezpečení sítě v Azure bastionu | Microsoft Docs
description: Tento článek popisuje, jak začlenit přístup NSG pomocí Azure bastionu.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: cherylmc
ms.openlocfilehash: 4f99b24435998fc4d0c7ab724c66a318586a80d4
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694947"
---
# <a name="working-with-nsg-access-and-azure-bastion-preview"></a>Práce s NSG přístupem a Azure bastionu (Preview)

Při práci s Azure bastionu můžete použít skupiny zabezpečení sítě (skupin zabezpečení sítě). Další informace najdete v tématu [skupiny zabezpečení](../virtual-network/security-overview.md). 

> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

![Architektura](./media/bastion-nsg/nsg_architecture.png)

V tomto diagramu:

* Hostitel bastionu je nasazený ve virtuální síti.
* Uživatel se připojí k Azure Portal pomocí libovolného prohlížeče HTML5.
* Uživatel vybere virtuální počítač, ke kterému se má připojit.
* Jediným kliknutím se v prohlížeči otevře relace RDP/SSH.
* Na virtuálním počítači Azure se nevyžaduje žádná veřejná IP adresa.

## <a name="nsg"></a>Skupiny zabezpečení sítě

* **AzureBastionSubnet:** Azure bastionu je nasazený v konkrétní AzureBastionSubnet.  
    * **Příchozí přenos dat z veřejného Internetu:** Azure bastionu vytvoří veřejnou IP adresu, která potřebuje port 443 povolený ve veřejné IP adrese pro přenos příchozích dat. Na AzureBastionSubnet se nevyžaduje otevřít port 3389/22.
    * **Odchozí přenos dat do cílových virtuálních počítačů:** Azure bastionu se dostane k cílovým virtuálním počítačům přes privátní IP adresu. Skupin zabezpečení sítě musí umožňovat přenos odchozího provozu do jiných cílových podsítí virtuálních počítačů.
* **Cílová podsíť virtuálních počítačů:** Jedná se o podsíť, která obsahuje cílový virtuální počítač, ke kterému chcete protokol RDP/SSH.
    * **Příchozí přenos dat z Azure bastionu:** Azure bastionu se bude přistihnout k cílovému virtuálnímu počítači přes privátní IP adresu. Porty RDP/SSH (porty 3389 a 22) je potřeba otevřít na cílové straně virtuálního počítače přes soukromou IP adresu.

## <a name="apply"></a>Použít skupin zabezpečení sítě na AzureBastionSubnet

Pokud skupin zabezpečení sítě použijete pro **AzureBastionSubnet**, povolte následující dvě značky služeb pro řídicí plochu a infrastrukturu Azure:

* **GatewayManager (pouze správce prostředků)** : Tato značka označuje předpony adresy služby Azure Gateway Manager. Pokud pro hodnotu zadáte GatewayManager, provoz se povolí nebo odepře GatewayManager.  Pokud vytváříte skupin zabezpečení sítě na AzureBastionSubnet, povolte pro příchozí provoz značku GatewayManager.

* **AzureCloud (jenom správce prostředků)** : Tato značka označuje adresní prostor IP adres pro Azure včetně všech veřejných IP adres Datacenter. Pokud pro tuto hodnotu zadáte AzureCloud, provoz se povolí nebo zamítne na veřejné IP adresy Azure. Pokud chcete povolený přístup jenom k AzureCloud v konkrétní oblasti, můžete zadat oblast. Pokud například chcete v Východní USA oblasti použít jenom přístup k Azure AzureCloud, můžete jako značku služby zadat AzureCloud. EastUS. Pokud vytváříte skupin zabezpečení sítě na AzureBastionSubnet, povolte pro odchozí přenosy značku AzureCloud. Pokud otevřete port 443 pro příchozí připojení k Internetu, nebudete muset povolit značku AzureCloud pro příchozí provoz.

## <a name="next-steps"></a>Další kroky

Další informace o Azure bastionu najdete v tématu [Nejčastější dotazy](bastion-faq.md) .
