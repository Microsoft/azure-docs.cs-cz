---
title: Otevření portů k virtuálnímu počítači pomocí Azure Portal
description: Přečtěte si, jak otevřít port/vytvořit koncový bod pro virtuální počítač pomocí Azure Portal
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/27/2020
ms.author: cynthn
ms.openlocfilehash: 28b3ee98d44e1567d9e25cf9237015396975ab01
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550446"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Postup otevření portů na virtuálním počítači s Azure Portal
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

1. Vyhledejte a vyberte skupinu prostředků pro virtuální počítač, zvolte **Přidat**, vyhledejte a vyberte **skupinu zabezpečení sítě**.

1. Vyberte **Vytvořit**.

    Otevře se okno **vytvořit skupinu zabezpečení sítě** .

    ![Vytvoření skupiny zabezpečení sítě](./media/nsg-quickstart-portal/create-nsg.png)

1. Zadejte název skupiny zabezpečení sítě. 

1. Vyberte nebo vytvořte skupinu prostředků a potom vyberte umístění.

1. Vyberte **vytvořit** a vytvořte skupinu zabezpečení sítě.

## <a name="create-an-inbound-security-rule"></a>Vytvoření příchozího pravidla zabezpečení

1. Vyberte novou skupinu zabezpečení sítě. 

1. V nabídce vlevo vyberte **příchozí pravidla zabezpečení** a pak vyberte **Přidat**.

    ![Přepnout na stránku Upřesnit](./media/nsg-quickstart-portal/advanced.png)

1. Na stránce **Přidat pravidlo zabezpečení příchozího** přenosu přepněte na **Upřesnit** ze **základního** umístění v horní části stránky. 

1. Z rozevírací nabídky, jako je **http**, vyberte běžnou **službu** . Můžete také vybrat možnost **vlastní** , pokud chcete zadat konkrétní port, který chcete použít. 

1. Volitelně můžete změnit **prioritu** nebo **název**. Priorita má vliv na pořadí, ve kterém jsou použita pravidla: čím nižší je číselná hodnota, použije se předchozí pravidlo.

1. Vyberte **Přidat** a vytvořte pravidlo.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Přidružení skupiny zabezpečení sítě k podsíti

Posledním krokem je přidružení skupiny zabezpečení sítě k podsíti nebo konkrétnímu síťovému rozhraní. V tomto příkladu přidružíme skupinu zabezpečení sítě k podsíti. 

1. V nabídce vlevo vyberte **podsítě** a pak vyberte **přidružit**.

1. Vyberte virtuální síť a pak vyberte vhodnou podsíť.

    ![Přidružení skupiny zabezpečení sítě k virtuální síti](./media/nsg-quickstart-portal/select-vnet-subnet.png)

1. Jakmile budete hotovi, vyberte **OK**.

## <a name="additional-information"></a>Další informace

[Kroky v tomto článku můžete provést také pomocí Azure PowerShell](nsg-quickstart-powershell.md).

Příkazy popsané v tomto článku vám umožní rychle získat provoz do svého virtuálního počítače. Skupiny zabezpečení sítě poskytují mnoho skvělých funkcí a členitosti pro řízení přístupu k vašim prostředkům. Další informace najdete v tématu [filtrování síťového provozu pomocí skupiny zabezpečení sítě](../../virtual-network/tutorial-filter-network-traffic.md).

V případě webových aplikací s vysokou dostupností zvažte umístění virtuálních počítačů za nástroj pro vyrovnávání zatížení Azure. Nástroj pro vyrovnávání zatížení distribuuje provoz do virtuálních počítačů pomocí skupiny zabezpečení sítě, která poskytuje filtrování přenosů. Další informace najdete v tématu věnovaném [Vyrovnávání zatížení virtuálních počítačů s Windows v Azure za účelem vytvoření vysoce dostupné aplikace](tutorial-load-balancer.md).

## <a name="next-steps"></a>Další kroky
V tomto článku jste vytvořili skupinu zabezpečení sítě, vytvořili příchozí pravidlo, které umožňuje přenosy HTTP na portu 80 a pak toto pravidlo přidružit k podsíti. 

Informace o vytváření podrobnějších prostředí najdete v následujících článcích:
- [Přehled Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [Skupiny zabezpečení](../../virtual-network/network-security-groups-overview.md)
