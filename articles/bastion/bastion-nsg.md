---
title: Práce s virtuálními počítači a skupinami zabezpečení v Azure Bastion
description: Tento článek popisuje, jak začlenit přístup k nsg s Azure Bastion
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: charwen
ms.openlocfilehash: 0188f9bc1c7c0e8d7fed9f590d078085b175614f
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732200"
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

### <a name="azurebastionsubnet"></a><a name="apply"></a>AzureBastionSubnet

Azure Bastion se nasazuje speciálně do ***AzureBastionSubnet***.

* **Přenos příchozího přenosu dat:**

   * **Příchozí přenosy z veřejného internetu:** Bašta Azure vytvoří veřejnou IP adresu, která potřebuje port 443 povolena na veřejné IP pro příchozí přenosy. Port 3389/22 není nutné otevřít na AzureBastionSubnet.
   * **Přenos y přenosu dat z řídicí roviny Azure Bastion:** Pro připojení k řídicí rovině povolte port 443 příchozí z výrobní ho disponu služby **GatewayManager.** To umožňuje rovině ovládacího prvku, to znamená Gateway Manager moci mluvit s Azure Bastion.

* **Odchozí provoz:**

   * **Odchozí provoz na cílové virtuální chod:** Azure Bastion dosáhne cílové virtuální počítače přes privátní IP. Skupiny sítě nsg musí povolit odchozí přenosy do jiných cílových podsítí virtuálních bylin pro port 3389 a 22.
   * **Odchozí provoz do jiných veřejných koncových bodů v Azure:** Azure Bastion musí být možné připojení k různým veřejným koncovým bodům v rámci Azure (například pro ukládání protokolů diagnostiky a protokolů měření). Z tohoto důvodu Azure Bastion potřebuje odchozí na 443 na značku služby **AzureCloud.**

### <a name="target-vm-subnet"></a>Cílová podsíť virtuálních mís
Toto je podsíť, která obsahuje cílový virtuální počítač, který chcete RDP/SSH.

   * **Příchozí přenosy z Azure Bastion:** Azure Bastion dosáhne cílového virtuálního počítače přes privátní IP. Porty RDP/SSH (porty 3389/22) je třeba otevřít na straně cílového virtuálního počítači přes privátní IP adresu. Jako osvědčený postup můžete přidat rozsah IP adres podsítě Azure Bastion v tomto pravidle, aby bylo možné otevřít tyto porty na cílových virtuálních počítačích v cílové podsíti virtuálních počítačů.


## <a name="next-steps"></a>Další kroky

Další informace o Azure Bastion najdete v [častých otázkách](bastion-faq.md).
