---
title: Otevření portů k virtuálnímu počítači pomocí webu Azure portal | Dokumentace Microsoftu
description: Zjistěte, jak otevřít port / vytvořit koncový bod k virtuálnímu počítači s Windows pomocí modelu nasazení resource manager na webu Azure Portal
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: 2820dcabf042d7463f9776b42f277a0457caf3b6
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929011"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Postup otevření portů k virtuálnímu počítači pomocí webu Azure portal
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Rychlé příkazy
Můžete také [tyto kroky provést pomocí prostředí Azure PowerShell](nsg-quickstart-powershell.md).

Nejprve vytvořte skupinu zabezpečení sítě. Vyberte skupinu prostředků na portálu, zvolte **přidat**, vyhledejte a vyberte **skupinu zabezpečení sítě**:

![Přidat skupinu zabezpečení sítě](./media/nsg-quickstart-portal/add-nsg.png)

Zadejte název skupiny zabezpečení sítě, vyberte nebo vytvořte skupinu prostředků a vyberte umístění. Vyberte **vytvořit** po dokončení:

![Vytvořte skupinu zabezpečení sítě](./media/nsg-quickstart-portal/create-nsg.png)

Vyberte novou skupinu zabezpečení sítě. Vyberte "Příchozí pravidla zabezpečení" a potom **přidat** tlačítko vytvoříte pravidlo:

![Přidat příchozí pravidlo](./media/nsg-quickstart-portal/add-inbound-rule.png)

Chcete-li vytvořit pravidlo, které umožní provoz:

- Vyberte **základní** tlačítko. Ve výchozím nastavení **Upřesnit** okno poskytuje některé další možnosti konfigurace, například pro účely definování rozsahu konkrétní zdrojové IP bloku nebo portu.
- Zvolte společný **služby** z rozevírací nabídky, jako například *HTTP*. Můžete také vybrat *vlastní* poskytnout konkrétní port, které se má použít. 
- V případě potřeby změňte prioritu nebo název. Prioritu má vliv na pořadí, ve kterém jsou použita pravidla - nižší číselná hodnota, tím výše se pravidlo použije.
- Až budete připravení, vyberte **OK** vytvoření pravidla:

![Vytvoření příchozího pravidla](./media/nsg-quickstart-portal/create-inbound-rule.png)

Posledním krokem je přidružení skupiny zabezpečení sítě k podsíti nebo konkrétnímu síťovému rozhraní. Pojďme přidružte skupinu zabezpečení sítě k podsíti. Vyberte **podsítě**, klikněte na tlačítko **přidružit**:

![Přidružte skupinu zabezpečení sítě k podsíti](./media/nsg-quickstart-portal/associate-subnet.png)

Vyberte virtuální síť a potom vyberte vhodnou podsíť:

![Přidružení skupiny zabezpečení sítě s virtuální sítí](./media/nsg-quickstart-portal/select-vnet-subnet.png)

Nyní jste vytvořili skupinu zabezpečení sítě, vytvoří příchozí pravidlo, které povoluje provoz na portu 80 a jeho přidružené k podsíti. Všechny virtuální počítače, ke kterým se připojujete k této podsíti jsou dostupné na portu 80.

## <a name="more-information-on-network-security-groups"></a>Další informace o skupinách zabezpečení sítě
Rychlé příkazy umožní začít pracovat s přenosy směřující do virtuálního počítače. Skupiny zabezpečení sítě poskytují mnoho skvělých funkcí a členitost řízení přístupu k vašim prostředkům. Další informace o [vytvoření skupiny zabezpečení sítě a seznamu ACL pravidla zde](../../virtual-network/tutorial-filter-network-traffic.md).

U vysoce dostupných webových aplikací by měl umístěte své virtuální počítače za nástrojem Azure Load Balancer. Nástroje pro vyrovnávání zatížení distribuuje provoz do virtuálních počítačů pomocí skupiny zabezpečení sítě, která poskytuje filtrování provozu. Další informace najdete v tématu [zatížení vyrovnávat virtuální počítače s Linuxem v Azure za účelem vytvoření vysoce dostupné aplikace](tutorial-load-balancer.md).

## <a name="next-steps"></a>Další postup
V tomto příkladu jste vytvořili jednoduché pravidlo pro povolení provozu HTTP. Můžete najít informace o vytváření podrobnější prostředí v následujících článcích:

* [Přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md)
* [Co je skupina zabezpečení sítě?](../../virtual-network/security-overview.md)