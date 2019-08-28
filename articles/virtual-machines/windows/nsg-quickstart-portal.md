---
title: Otevření portů k virtuálnímu počítači pomocí Azure Portal | Microsoft Docs
description: Přečtěte si, jak otevřít port/vytvořit koncový bod pro virtuální počítač s Windows pomocí modelu nasazení Resource Manager na webu Azure Portal.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: f9edee7a0ff19a536d0ea719ede6d0cd2e9d6ac7
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102637"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Postup otevření portů na virtuálním počítači s Azure Portal
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-network-security-group"></a>Vytvoření skupiny zabezpečení sítě

1. Vyhledejte a vyberte skupinu prostředků pro virtuální počítač, zvolte **Přidat**, vyhledejte a vyberte **skupinu zabezpečení sítě**.

2. Vyberte **Vytvořit**.

    Otevře se okno **vytvořit skupinu zabezpečení sítě** .

    ![Vytvoření skupiny zabezpečení sítě](./media/nsg-quickstart-portal/create-nsg.png)

2. Zadejte název skupiny zabezpečení sítě. 

3. Vyberte nebo vytvořte skupinu prostředků a potom vyberte umístění.

4. Vyberte **vytvořit** a vytvořte skupinu zabezpečení sítě.

## <a name="create-an-inbound-security-rule"></a>Vytvoření příchozího pravidla zabezpečení

1. Vyberte novou skupinu zabezpečení sítě. 

2. Vyberte **příchozí pravidla zabezpečení**a pak vyberte **Přidat**.

    ![Přidat příchozí pravidlo](./media/nsg-quickstart-portal/add-inbound-rule.png)

3. Vyberte **Upřesnit**. 

4. Z rozevírací nabídky, jako je **http**, vyberte běžnou **službu** . Můžete také vybrat možnost **vlastní** , pokud chcete zadat konkrétní port, který chcete použít. 

5. Volitelně můžete změnit **prioritu** nebo **název**. Priorita má vliv na pořadí, ve kterém jsou použita pravidla: čím nižší je číselná hodnota, použije se předchozí pravidlo.

6. Vyberte **Přidat** a vytvořte pravidlo.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Přidružení skupiny zabezpečení sítě k podsíti

Posledním krokem je přidružení skupiny zabezpečení sítě k podsíti nebo konkrétnímu síťovému rozhraní. V tomto příkladu přidružíme skupinu zabezpečení sítě k podsíti. 

1. Vyberte **podsítě**a pak vyberte **přidružit**.

    ![Přidružení skupiny zabezpečení sítě k podsíti](./media/nsg-quickstart-portal/associate-subnet.png)

2. Vyberte virtuální síť a pak vyberte vhodnou podsíť.

    ![Přidružení skupiny zabezpečení sítě k virtuální síti](./media/nsg-quickstart-portal/select-vnet-subnet.png)

    Všechny virtuální počítače, ke kterým se připojíte k této podsíti, jsou teď dostupné na portu 80.

## <a name="additional-information"></a>Další informace

[Kroky v tomto článku můžete provést také pomocí Azure PowerShell](nsg-quickstart-powershell.md).

Příkazy popsané v tomto článku vám umožní rychle získat provoz do svého virtuálního počítače. Skupiny zabezpečení sítě poskytují mnoho skvělých funkcí a členitosti pro řízení přístupu k vašim prostředkům. Další informace najdete v tématu [filtrování síťového provozu pomocí skupiny zabezpečení sítě](../../virtual-network/tutorial-filter-network-traffic.md).

V případě webových aplikací s vysokou dostupností zvažte umístění virtuálních počítačů za nástroj pro vyrovnávání zatížení Azure. Nástroj pro vyrovnávání zatížení distribuuje provoz do virtuálních počítačů pomocí skupiny zabezpečení sítě, která poskytuje filtrování přenosů. Další informace najdete v tématu věnovaném vyrovnávání [zatížení virtuálních počítačů s Windows v Azure za účelem vytvoření vysoce dostupné aplikace](tutorial-load-balancer.md).

## <a name="next-steps"></a>Další kroky
V tomto článku jste vytvořili skupinu zabezpečení sítě, vytvořili příchozí pravidlo, které umožňuje přenosy HTTP na portu 80 a pak toto pravidlo přidružit k podsíti. 

Informace o vytváření podrobnějších prostředí najdete v následujících článcích:
- [Přehled Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md)
- [Skupiny zabezpečení](../../virtual-network/security-overview.md)