---
title: Vytvořte skupinu zabezpečení sítě - portálu Azure | Microsoft Docs
description: Zjistěte, jak vytvořit a nasadit skupin zabezpečení sítě pomocí portálu Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 5bc8fc2e-1e81-40e2-8231-0484cd5605cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d8a66de0b0239fef12168733eca7af85c8b08f82
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="create-a-network-security-group-using-the-azure-portal"></a>Vytvořte skupinu zabezpečení sítě pomocí portálu Azure

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Tento článek se týká modelu nasazení Resource Manager. Můžete také [vytvářet skupiny Nsg v modelu nasazení classic](virtual-networks-create-nsg-classic-ps.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]


## <a name="create-the-nsg-frontend-nsg"></a>Vytvořit skupinu NSG NSG front-endu
Chcete-li vytvořit **NSG front-endu** NSG, jak je zobrazeno ve scénáři, proveďte následující kroky:

1. V prohlížeči přejděte na https://portal.azure.com a v případě potřeby se přihlaste pomocí účtu Azure.
2. Vyberte **+ vytvořit prostředek >** > **skupin zabezpečení sítě**.
   
    ![Portál Azure – skupiny Nsg](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)
3. V části **skupin zabezpečení sítě**, vyberte **přidat**.
   
    ![Portál Azure – skupiny Nsg](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)
4. V části **vytvořit skupinu zabezpečení sítě**, vytvořit skupinu NSG s názvem *NSG front-endu* v *RG NSG* prostředku, skupiny a pak vyberte **vytvořit** .
   
    ![Portál Azure – skupiny Nsg](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>Vytvoření pravidel v existující skupině NSG
K vytvoření pravidel v existující skupina NSG z portálu Azure, proveďte následující kroky:

1. Vyberte **všechny služby**, vyhledejte **skupin zabezpečení sítě**. Když **skupin zabezpečení sítě** nezobrazí, vyberte ho.
2. Vyberte v seznamu skupin Nsg, **NSG front-endu** > **příchozí pravidla zabezpečení**
   
    ![Portál Azure – skupina NSG front-endu](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)
3. V seznamu **příchozí pravidla zabezpečení**, vyberte **přidat**.
   
    ![Portál Azure – přidání pravidla](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)
4. V části **přidat příchozí pravidlo zabezpečení**, vytvořit pravidlo s názvem *pravidlo webové* s prioritu *200* povolením přístupu prostřednictvím *TCP* port *80* k žádné virtuální počítače z jakéhokoli zdroje a pak vyberte **OK**. Všimněte si, že většinu těchto nastavení jsou již výchozí hodnoty.
   
    ![Portál Azure – nastavení pravidla](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)
5. Za několik sekund zobrazí nové pravidlo v této skupině.
   
    ![Portál Azure – nové pravidlo](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)
6. Opakujte kroky 6 k vytvoření příchozího pravidla s názvem *rdp pravidlo* s prioritou *250* povolením přístupu prostřednictvím *TCP* na port *3389* k žádné virtuální počítače z jakéhokoli zdroje.

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>Přidružení NSG k podsíti FrontEnd

1. Vyberte **všechny služby >**, zadejte **skupiny prostředků**, vyberte **skupiny prostředků** když se objeví, pak vyberte **RG NSG**.
2. V části **RG NSG**, vyberte **...**   >  **TestVNet**.
   
    ![Portál Azure – TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)
3. V části **nastavení**, vyberte **podsítě** > **front-endu** > **skupinu zabezpečení sítě**  >  **NSG front-endu**.
   
    ![Portál Azure – nastavení podsítě](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)
4. V **front-endu** vyberte **Uložit**.
   
    ![Portál Azure – nastavení podsítě](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>Vytvořit skupinu NSG NSG back-end
K vytvoření **NSG back-end** NSG a přidružte ji k **back-end** podsíť, proveďte následující kroky:

1. Chcete-li vytvořit skupinu NSG s názvem *NSG back-end*, opakujte kroky v [vytvořit NSG NSG front-endu](#Create-the-NSG-FrontEnd-NSG).
2. Chcete-li vytvořit **příchozí** pravidla v následující tabulce, opakujte kroky v [vytvořit pravidla v existující skupině](#Create-rules-in-an-existing-NSG).
   
   | Příchozí pravidlo | Odchozí pravidlo |
   | --- | --- |
   | ![Portál Azure – příchozí pravidlo](./media/virtual-networks-create-nsg-arm-pportal/figure17.png) |![Portál Azure – odchozí pravidlo](./media/virtual-networks-create-nsg-arm-pportal/figure18.png) |
3. Pro přidružení **NSG back-end** NSG k **back-end** podsítě, opakujte kroky v [přidružení skupiny NSG k podsíti front-endu](#Associate-the-NSG-to-the-FrontEnd-subnet).

## <a name="next-steps"></a>Další kroky
* Zjistěte, jak [spravovat existující skupiny Nsg](manage-network-security-group.md)
* [Povolit protokolování](virtual-network-nsg-manage-log.md) pro skupiny Nsg.