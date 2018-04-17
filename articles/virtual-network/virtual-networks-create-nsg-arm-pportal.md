---
title: Vytvoření sítě skupiny zabezpečení - portálu Azure | Microsoft Docs
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
ms.openlocfilehash: dd05df542327f9d8dae924b7097d247980a0558b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="create-network-security-groups-using-the-azure-portal"></a>Vytvoření sítě skupiny zabezpečení pomocí portálu Azure

[!INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Tento článek se týká modelu nasazení Resource Manager. Můžete také [vytvářet skupiny Nsg v modelu nasazení classic](virtual-networks-create-nsg-classic-ps.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Ukázka jednoduché prostředí už vytvořený očekávat níže uvedené příkazy prostředí PowerShell založené na výše uvedené scénáře. Pokud chcete ke spuštění příkazů, jak jsou zobrazeny v tomto dokumentu, nasazením nejprve vytvořit testovací prostředí [této šablony](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd), klikněte na tlačítko **nasadit do Azure**, nahradí výchozí hodnoty parametrů v případě potřeby a postupujte podle pokynů v portálu. Následující použijte postup **RG NSG** jako název šablony byla nasazena do skupiny prostředků.

## <a name="create-the-nsg-frontend-nsg"></a>Vytvořit skupinu NSG NSG front-endu
Chcete-li vytvořit **NSG front-endu** NSG, jak je znázorněno v tomto scénáři výše, použijte následující postup.

1. V prohlížeči přejděte na http://portal.azure.com a v případě potřeby se přihlaste pomocí účtu Azure.
2. Klikněte na tlačítko **procházet >** > **skupin zabezpečení sítě**.
   
    ![Portál Azure – skupiny Nsg](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)
3. V **skupin zabezpečení sítě** okně klikněte na tlačítko **přidat**.
   
    ![Portál Azure – skupiny Nsg](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)
4. V **vytvořit skupinu zabezpečení sítě** okně Vytvořit skupinu NSG s názvem *NSG front-endu* v *RG NSG* skupinu prostředků a pak klikněte na tlačítko **vytvořit**.
   
    ![Portál Azure – skupiny Nsg](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>Vytvoření pravidel v existující skupině NSG
Při vytváření pravidla v existující skupina NSG z portálu Azure, postupujte podle následujících kroků.

1. Klikněte na tlačítko **procházet >** > **skupin zabezpečení sítě**.
2. V seznamu skupin Nsg, klikněte na **NSG front-endu** > **příchozí pravidla zabezpečení**
   
    ![Portál Azure – skupina NSG front-endu](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)
3. V seznamu **příchozí pravidla zabezpečení**, klikněte na tlačítko **přidat**.
   
    ![Portál Azure – přidání pravidla](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)
4. V **přidat příchozí pravidlo zabezpečení** okně Vytvořit pravidlo s názvem *pravidlo webové* s prioritu *200* povolením přístupu prostřednictvím *TCP* na port *80* k žádné virtuální počítače z jakéhokoli zdroje a pak klikněte na tlačítko **OK**. Všimněte si, že většinu těchto nastavení jsou již výchozí hodnoty.
   
    ![Portál Azure – nastavení pravidla](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)
5. Za několik sekund zobrazí se nové pravidlo v této skupině.
   
    ![Portál Azure – nové pravidlo](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)
6. Opakujte kroky 6 k vytvoření příchozího pravidla s názvem *rdp pravidlo* s prioritou *250* povolením přístupu prostřednictvím *TCP* na port *3389* k žádné virtuální počítače z jakéhokoli zdroje.

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>Přidružení NSG k podsíti FrontEnd
1. Klikněte na tlačítko **procházet >** > **skupiny prostředků** > **RG NSG**.
2. V **RG NSG** okně klikněte na tlačítko **...**   >  **TestVNet**.
   
    ![Portál Azure – TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)
3. V **nastavení** okně klikněte na tlačítko **podsítě** > **front-endu** > **skupinu zabezpečení sítě** > **NSG front-endu**.
   
    ![Portál Azure – nastavení podsítě](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)
4. V **front-endu** okně klikněte na tlačítko **Uložit**.
   
    ![Portál Azure – nastavení podsítě](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>Vytvořit skupinu NSG NSG back-end
K vytvoření **NSG back-end** NSG a přidružte ji k **back-end** podsíť, použijte následující postup.

1. Opakujte kroky v [vytvořit NSG NSG front-endu](#Create-the-NSG-FrontEnd-NSG) vytvořit skupinu NSG s názvem *NSG back-end*
2. Opakujte kroky v [vytvořit pravidla v existující skupině](#Create-rules-in-an-existing-NSG) vytvořit **příchozí** pravidla v následující tabulce.
   
   | Příchozí pravidlo | Odchozí pravidlo |
   | --- | --- |
   | ![Portál Azure – příchozí pravidlo](./media/virtual-networks-create-nsg-arm-pportal/figure17.png) |![Portál Azure – odchozí pravidlo](./media/virtual-networks-create-nsg-arm-pportal/figure18.png) |
3. Opakujte kroky v [přidružení skupiny NSG k podsíti front-endu](#Associate-the-NSG-to-the-FrontEnd-subnet) přidružit **NSG back-end** NSG k **back-end** podsítě.

## <a name="next-steps"></a>Další kroky
* Zjistěte, jak [spravovat existující skupiny Nsg](manage-network-security-group.md)
* [Povolit protokolování](virtual-network-nsg-manage-log.md) pro skupiny Nsg.

