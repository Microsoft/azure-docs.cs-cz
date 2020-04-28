---
title: Otevření portů k virtuálnímu počítači pomocí portálu Azure
description: Naučte se otevřít port nebo vytvořit koncový bod pro virtuální počítač s Windows pomocí portálu Azure Portal
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: 1844236a77b688819832b3fe0bf6736beea4bfae
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81865487"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Jak otevřít porty do virtuálního počítače pomocí portálu Azure
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

1. Vyhledejte a vyberte skupinu prostředků pro virtuální hosti, zvolte **Přidat**a pak vyhledejte a vyberte **skupinu zabezpečení sítě**.

2. Vyberte **Vytvořit**.

    Otevře se okno **Vytvořit skupinu zabezpečení sítě.**

    ![Vytvoření skupiny zabezpečení sítě](./media/nsg-quickstart-portal/create-nsg.png)

2. Zadejte název skupiny zabezpečení sítě. 

3. Vyberte nebo vytvořte skupinu prostředků a pak vyberte umístění.

4. Vyberte **Vytvořit,** chcete-li vytvořit skupinu zabezpečení sítě.

## <a name="create-an-inbound-security-rule"></a>Vytvoření pravidla příchozího zabezpečení

1. Vyberte novou skupinu zabezpečení sítě. 

2. Vyberte **Příchozí pravidla zabezpečení**a pak vyberte **Přidat**.

    ![Přidat příchozí pravidlo](./media/nsg-quickstart-portal/add-inbound-rule.png)

3. Vyberte **Upřesnit**. 

4. V rozevírací nabídce zvolte běžnou **službu,** například **HTTP**. Můžete také vybrat **vlastní,** pokud chcete poskytnout konkrétní port k použití. 

5. Volitelně můžete změnit **prioritu** nebo **název**. Priorita ovlivňuje pořadí, ve kterém jsou pravidla použita: čím nižší je číselná hodnota, tím dříve je pravidlo použito.

6. Chcete-li vytvořit pravidlo, vyberte **Přidat.**

## <a name="associate-your-network-security-group-with-a-subnet"></a>Přidružení skupiny zabezpečení sítě k podsíti

Posledním krokem je přidružení skupiny zabezpečení sítě k podsíti nebo konkrétnímu síťovému rozhraní. V tomto příkladu přidručíme skupinu zabezpečení sítě k podsíti. 

1. Vyberte **podsítě**, pak vyberte **Přidružit**.

    ![Přidružení skupiny zabezpečení sítě k podsíti](./media/nsg-quickstart-portal/associate-subnet.png)

2. Vyberte virtuální síť a pak vyberte vhodnou podsíť.

    ![Připojení skupiny zabezpečení sítě k virtuální síti](./media/nsg-quickstart-portal/select-vnet-subnet.png)

    Všechny virtuální uživatele, které připojíte k této podsíti, jsou teď dostupné na portu 80.

## <a name="additional-information"></a>Další informace

Kroky v tomto článku můžete provést také [pomocí Azure PowerShellu](nsg-quickstart-powershell.md).

Příkazy popsané v tomto článku umožňují rychle získat provoz tekoucí do virtuálního počítače. Skupiny zabezpečení sítě poskytují mnoho skvělých funkcí a rozlišovací schopnost pro řízení přístupu k prostředkům. Další informace naleznete v [tématu Filtrování síťového provozu se skupinou zabezpečení sítě](../../virtual-network/tutorial-filter-network-traffic.md).

U vysoce dostupných webových aplikací zvažte umístění virtuálních počítačů za Azure balancer. Správce načítání distribuuje provoz do virtuálních připojení se skupinou zabezpečení sítě, která poskytuje filtrování provozu. Další informace najdete [v tématu Vyrovnávání zatížení windows virtuální počítače v Azure k vytvoření vysoce dostupné aplikace](tutorial-load-balancer.md).

## <a name="next-steps"></a>Další kroky
V tomto článku jste vytvořili skupinu zabezpečení sítě, vytvořili příchozí pravidlo, které umožňuje přenos protokolu HTTP na portu 80, a pak toto pravidlo přidružili k podsíti. 

Informace o vytváření podrobnějších prostředí naleznete v následujících článcích:
- [Přehled Správce prostředků Azure](../../azure-resource-manager/management/overview.md)
- [Skupiny zabezpečení](../../virtual-network/security-overview.md)