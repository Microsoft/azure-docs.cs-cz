---
title: Práce s virtuálními počítači a skupin zabezpečení sítě v Azure Bastionu | Dokumentace Microsoftu
description: Tento článek popisuje, jak začlenit přístup skupiny zabezpečení sítě pomocí Azure Bastionu
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: e7210b2b1be072f5326070d768d5fe12c386ee0b
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191600"
---
# <a name="working-with-nsg-access-and-azure-bastion-preview"></a>Práce s přístupem k NSG a Bastionu Azure (Preview)

Při práci s Azure Bastionu, můžete použít skupiny zabezpečení sítě (Nsg). Další informace najdete v tématu [skupiny zabezpečení](../virtual-network/security-overview.md). 

> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

![Architektura](./media/bastion-nsg/nsg_architecture.png)

V tomto diagramu:

* Bastion host se nasadí ve virtuální síti.
* Uživatel se připojí k webu Azure portal pomocí libovolného prohlížeče HTML5.
* Uživatel vybere virtuálního počítače pro připojení k.
* Jedním kliknutím se v prohlížeči otevře relaci RDP/SSH.
* Žádná veřejná IP adresa musí být ve virtuálním počítači Azure.

## <a name="nsg"></a>Skupiny zabezpečení sítě

* **AzureBastionSubnet:** Nasazení Azure Bastionu v konkrétní AzureBastionSubnet.  
    * **Příchozí provoz z veřejného Internetu:** Bastionu Azure vytvoří veřejnou IP adresu, která potřebuje port 443 na veřejné IP adresy pro příchozí provoz povolené. Port 3389/22 jsou potřeba otevřít v AzureBastionSubnet nevyžaduje.
    * **Výchozí přenos do cílové virtuální počítače:** Azure Bastionu dosáhnou cílových virtuálních počítačů přes privátní IP adresa. Skupiny zabezpečení sítě je potřeba povolit výchozí přenos do jiných podsítí cílového virtuálního počítače.
* **Cílová podsíť virtuálních počítačů:** Toto je podsíť, která obsahuje cílový virtuální počítač, který chcete připojit prostřednictvím RDP/SSH k.
    * **Příchozí provoz z Azure Bastionu:** Azure Bastionu dosáhnou k cílovému virtuálnímu počítači přes privátní IP adresa. RDP/SSH porty (porty 3389 a 22, v uvedeném pořadí) potřeba otevřít na cílové straně virtuálního počítače přes privátní IP adresa.

## <a name="apply"></a>Použít skupiny Nsg na AzureBastionSubnet

Pokud použijete skupiny Nsg k **AzureBastionSubnet**, povolit následující dvě značky služeb pro rovina řízení Azure a infrastrukturou:

* **GatewayManager (pouze Resource Manager)** : Tato značka označuje předpony adres služeb Azure Správce brány. Pokud zadáte hodnotu GatewayManager, provoz je povolený nebo zakázaný pro GatewayManager.

* **AzureCloud (pouze Resource Manager)** : Tato značka označuje adresní prostor IP adres pro Azure, včetně všech datových center veřejné IP adresy. Pokud zadáte hodnotu AzureCloud, provoz je povolený nebo zakázaný k veřejným IP adresám Azure. Pokud chcete povolit přístup k AzureCloud v konkrétní oblasti, můžete zadat tuto oblast. Například pokud chcete povolit přístup pouze k AzureCloud Azure v oblasti východní USA, můžete jako značku služby zadat AzureCloud.EastUS.

## <a name="next-steps"></a>Další postup

Další informace o Azure Bastionu, najdete v článku [– nejčastější dotazy](bastion-faq.md)