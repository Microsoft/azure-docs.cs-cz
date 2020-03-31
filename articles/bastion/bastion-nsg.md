---
title: Práce s virtuálními počítači a skupinami zabezpečení v Azure Bastion
description: Tento článek popisuje, jak začlenit přístup k nsg s Azure Bastion
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 15abee4688a2f6aefa2b08ad2b8eee6622d56be2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087271"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>Práce s přístupem k nsg a baštou Azure

Při práci s Azure Bastion můžete použít skupiny zabezpečení sítě (NSGs). Další informace naleznete v [tématu Skupiny zabezpečení](../virtual-network/security-overview.md). 

![Architektura](./media/bastion-nsg/nsg-architecture.png)

V tomto diagramu:

* Hostitel Bastion je nasazený do virtuální sítě.
* Uživatel se připojí k portálu Azure pomocí libovolného prohlížeče HTML5.
* Uživatel přejde na virtuální počítač Azure rdp/SSH.
* Připojit integraci – relace RDP/SSH jedním kliknutím uvnitř prohlížeče
* Na virtuálním počítači Azure se nevyžaduje žádná veřejná IP adresa.

## <a name="network-security-groups"></a><a name="nsg"></a>Skupiny zabezpečení sítě

Tato část ukazuje síťový provoz mezi uživatelem a Azure Bastion a až k cílové virtuální počítače ve vaší virtuální síti:

### <a name="azurebastionsubnet"></a>AzureBastionSubnet

Azure Bastion se nasadí speciálně na AzureBastionSubnet.

* **Přenos příchozího přenosu dat:**

   * **Příchozí přenosy z veřejného internetu:** Bašta Azure vytvoří veřejnou IP adresu, která potřebuje port 443 povolena na veřejné IP pro příchozí přenosy. Port 3389/22 není nutné otevřít na AzureBastionSubnet.
   * **Přenos y přenosu dat z řídicí roviny Azure Bastion:** Pro připojení k řídicí rovině povolte port 443 příchozí z výrobní ho disponu služby **GatewayManager.** To umožňuje rovině ovládacího prvku, to znamená Gateway Manager moci mluvit s Azure Bastion.

* **Odchozí provoz:**

   * **Odchozí provoz na cílové virtuální chod:** Azure Bastion dosáhne cílové virtuální počítače přes privátní IP. Skupiny sítě nsg musí povolit odchozí přenosy do jiných cílových podsítí virtuálních bylin pro port 3389 a 22.
   * **Odchozí provoz do jiných veřejných koncových bodů v Azure:** Azure Bastion musí být možné připojení k různým veřejným koncovým bodům v rámci Azure (například pro ukládání protokolů diagnostiky a protokolů měření). Z tohoto důvodu Azure Bastion potřebuje odchozí na 443 na značku služby **AzureCloud.**

* **Cílová podsíť virtuálních mís:** Toto je podsíť, která obsahuje cílový virtuální počítač, který chcete RDP/SSH.

   * **Příchozí přenosy z Azure Bastion:** Azure Bastion dosáhne cílového virtuálního počítače přes privátní IP. Porty RDP/SSH (porty 3389/22) je třeba otevřít na straně cílového virtuálního počítači přes privátní IP adresu. Jako osvědčený postup můžete přidat rozsah IP adres podsítě Azure Bastion v tomto pravidle, aby bylo možné otevřít tyto porty na cílových virtuálních počítačích v cílové podsíti virtuálních počítačů.

## <a name="apply-nsgs-to-azurebastionsubnet"></a><a name="apply"></a>Použití skupin nsg pro AzureBastionSubnet

Pokud vytvoříte a použijete skupinu zabezpečení sítě pro ***AzureBastionSubnet***, ujistěte se, že jste do souboru zabezpečení sítě přidali následující pravidla. Pokud tato pravidla nepřidáte, vytvoření/aktualizace sítě zabezpečení sítě se nezdaří:

* **Připojení k řídicí rovině:** Příchozí na 443 z GatewayManager
* **Protokolování diagnostiky a další:** Odchozí na 443 na AzureCloud. Místní značky v rámci této značky služby ještě nejsou podporovány.
* **Cílový virtuální virtuální virtuální ms:** Odchozí pro 3389 a 22 do virtualnetwork

Příklad pravidla nsg je k dispozici pro odkaz v této [šabloně rychlého startu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion-nsg).

## <a name="next-steps"></a>Další kroky

Další informace o Azure Bastion najdete v [častých otázkách](bastion-faq.md).
