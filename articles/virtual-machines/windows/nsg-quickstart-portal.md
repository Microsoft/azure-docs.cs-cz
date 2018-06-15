---
title: Otevřete porty, které se virtuální počítač pomocí portálu Azure | Microsoft Docs
description: Zjistěte, jak otevřít port / create koncového bodu váš virtuální počítač s Windows pomocí modelu nasazení resource manager na portálu Azure
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: iainfou
ms.openlocfilehash: a64e2bbe1bb784f0b6032980d6f212470549cdf4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34366915"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Jak otevřít porty, které se virtuální počítač pomocí portálu Azure
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Rychlé příkazy
Můžete také [proveďte tyto kroky, pomocí Azure PowerShell](nsg-quickstart-powershell.md).

Nejprve vytvořte skupinu zabezpečení vaší sítě. Vyberte skupinu prostředků na portálu, zvolte **přidat**, vyhledejte a vyberte **skupinu zabezpečení sítě**:

![Přidat skupinu zabezpečení sítě](./media/nsg-quickstart-portal/add-nsg.png)

Zadejte název pro vaší skupinu zabezpečení sítě, vyberte nebo vytvořte skupinu prostředků a vyberte umístění. Vyberte **vytvořit** po dokončení:

![Vytvořit skupinu zabezpečení sítě](./media/nsg-quickstart-portal/create-nsg.png)

Vyberte novou skupinu zabezpečení sítě. Vyberte 'příchozí pravidla zabezpečení, a pak vyberte **přidat** tlačítko k vytvoření pravidla:

![Přidat příchozí pravidlo](./media/nsg-quickstart-portal/add-inbound-rule.png)

Pokud chcete vytvořit pravidlo, které umožňuje přenos:

- Vyberte **základní** tlačítko. Ve výchozím nastavení **Upřesnit** okno poskytuje některé další možnosti konfigurace, jako třeba definovat rozsah bloku nebo portu IP konkrétního zdroje.
- Zvolte společného **služby** z rozevírací nabídky, jako například *HTTP*. Můžete také vybrat *vlastní* zajistit konkrétní port, které se má použít. 
- V případě potřeby změňte prioritu nebo název. Prioritu má vliv pořadí, ve kterém jsou použity pravidla - dolní číselnou hodnotu, dříve pravidlo se použije.
- Až budete připravení, vyberte **OK** vytvoření pravidla:

![Vytvoření příchozího pravidla](./media/nsg-quickstart-portal/create-inbound-rule.png)

Posledním krokem je vaše skupina zabezpečení sítě přidružit podsítě nebo konkrétní síťové rozhraní. Skupina zabezpečení sítě umožňuje přidružit podsítě. Vyberte **podsítě**, zvolte **přidružit**:

![Přidružte skupinu zabezpečení sítě s podsítí](./media/nsg-quickstart-portal/associate-subnet.png)

Vyberte virtuální síť a pak vyberte příslušnou podsítí:

![Přidružení skupiny zabezpečení sítě pomocí virtuální sítě](./media/nsg-quickstart-portal/select-vnet-subnet.png)

Nyní jste vytvořili skupinu zabezpečení sítě, vytvořit vstupní pravidlo umožňující přenosy na portu 80 a spojené s podsítí. Všechny virtuální počítače, ke kterým se připojujete k této podsíti jsou dostupné na portu 80.

## <a name="more-information-on-network-security-groups"></a>Další informace o skupinách zabezpečení sítě
Rychlé příkazy umožňují zprovoznění s provoz do virtuálního počítače. Skupiny zabezpečení sítě zadejte mnoho funkcí a členitosti pro řízení přístupu k prostředkům. Další informace o [vytvoření skupiny zabezpečení sítě a seznamu ACL pravidla zde](../../virtual-network/tutorial-filter-network-traffic.md).

Pro vysokou dostupnost webové aplikace měli byste umístit virtuální počítače za pro vyrovnávání zatížení Azure. Nástroje pro vyrovnávání zatížení distribuuje provoz do virtuálních počítačů s skupinu zabezpečení sítě, která poskytuje filtrování provozu. Další informace najdete v tématu [jak načíst vyvážit virtuální počítače s Linuxem v Azure k vytvoření vysoce dostupné aplikace](tutorial-load-balancer.md).

## <a name="next-steps"></a>Další postup
V tomto příkladu jste vytvořili jednoduché pravidlo umožňující přenos HTTP. Můžete najít informace o vytváření podrobnější prostředí v těchto článcích:

* [Přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md)
* [Co je skupina zabezpečení sítě?](../../virtual-network/security-overview.md)