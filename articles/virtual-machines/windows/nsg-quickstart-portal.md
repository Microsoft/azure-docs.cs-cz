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
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: cac17403425f53593d4f48692b4216a92c8624e3
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2018
ms.locfileid: "48884381"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Postup otevření portů k virtuálnímu počítači pomocí webu Azure portal
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

1. Vyhledejte a vyberte skupinu prostředků příslušného virtuálního počítače, zvolte **přidat**, vyhledejte a vyberte **skupinu zabezpečení sítě**.

2. Vyberte **Vytvořit**.

    **Vytvořit skupinu zabezpečení sítě** otevře se okno.

    ![Vytvoření skupiny zabezpečení sítě](./media/nsg-quickstart-portal/create-nsg.png)

2. Zadejte název skupiny zabezpečení sítě. 

3. Vyberte nebo vytvořte skupinu prostředků a potom vyberte umístění.

4. Vyberte **vytvořit** vytvořte skupinu zabezpečení sítě.

## <a name="create-an-inbound-security-rule"></a>Vytvoření pravidla zabezpečení příchozích dat

1. Vyberte novou skupinu zabezpečení sítě. 

2. Vyberte **příchozí pravidla zabezpečení**a pak vyberte **přidat**.

    ![Přidat příchozí pravidlo](./media/nsg-quickstart-portal/add-inbound-rule.png)

3. Vyberte **Upřesnit**. 

4. Zvolte společný **služby** z rozevírací nabídky, jako například **HTTP**. Můžete také vybrat **vlastní** Pokud byste chtěli poskytnout konkrétní port, které se má použít. 

5. Volitelně můžete změnit **Priority** nebo **název**. Priorita ovlivňuje pořadí, ve kterém jsou použity pravidla: Čím nižší číselná hodnota, tím výše se pravidlo použije.

6. Vyberte **přidat** vytvoření pravidla.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Přidružte skupinu zabezpečení sítě k podsíti

Posledním krokem je přidružení skupiny zabezpečení sítě k podsíti nebo konkrétnímu síťovému rozhraní. V tomto příkladu jsme budete přidružte skupinu zabezpečení sítě k podsíti. 

1. Vyberte **podsítě**a pak vyberte **přidružit**.

    ![Přidružte skupinu zabezpečení sítě k podsíti](./media/nsg-quickstart-portal/associate-subnet.png)

2. Vyberte virtuální síť a pak vyberte vhodnou podsíť.

    ![Přidružení skupiny zabezpečení sítě s virtuální sítí](./media/nsg-quickstart-portal/select-vnet-subnet.png)

    Všechny virtuální počítače, ke kterým se připojujete k této podsíti jsou teď dostupné na portu 80.

## <a name="additional-information"></a>Další informace

Můžete také [proveďte kroky v tomto článku pomocí Azure Powershellu](nsg-quickstart-powershell.md).

Příkazů popsaných v tomto článku vám umožňují rychle zajistit provoz k vašemu virtuálnímu počítači. Skupiny zabezpečení sítě poskytují mnoho skvělých funkcí a členitost řízení přístupu k vašim prostředkům. Další informace najdete v tématu [filtrování provozu sítě s použitím skupiny zabezpečení sítě](../../virtual-network/tutorial-filter-network-traffic.md).

U vysoce dostupných webových aplikací zvažte umístění virtuálních počítačů za služby Azure load balancer. Nástroje pro vyrovnávání zatížení distribuuje provoz do virtuálních počítačů, se skupinou zabezpečení sítě, která poskytuje filtrování provozu. Další informace najdete v tématu [vyrovnávat zatížení virtuálních počítačů Windows v Azure za účelem vytvoření vysoce dostupné aplikace](tutorial-load-balancer.md).

## <a name="next-steps"></a>Další postup
V tomto článku jste vytvořili skupinu zabezpečení sítě, vytvoří příchozí pravidlo, které umožní provoz protokolu HTTP na portu 80 a toto pravidlo potom přidružené k podsíti. 

Můžete najít informace o vytváření podrobnější prostředí v následujících článcích:
- [Přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md)
- [Skupiny zabezpečení](../../virtual-network/security-overview.md)