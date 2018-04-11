---
title: "Správa skupin Nsg pomocí portálu Azure | Microsoft Docs"
description: "Naučte se spravovat existující skupiny Nsg pomocí portálu Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 5d55679d-57da-457c-97dc-1e1973909ee5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2016
ms.author: jdial
ms.openlocfilehash: e9bcf8a893ff209337f6a5763b631a22f8514e20
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
---
# <a name="manage-nsgs-using-the-portal"></a>Správa skupin Nsg pomocí portálu

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-manage-nsg-arm-portal.md)
> * [PowerShell](virtual-network-manage-nsg-arm-ps.md)
> * [Azure CLI](virtual-network-manage-nsg-arm-cli.md)
>

[!INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

> [!NOTE]
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Resource Manager a klasický model](../resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Resource Manager, které společnost Microsoft doporučuje pro většinu nových nasazení místo modelu nasazení classic.
>

[!INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

## <a name="retrieve-information"></a>Načtení informací o
Můžete zobrazit stávající skupiny Nsg, načíst pravidla pro existující skupiny NSG a zjistit, jaké prostředky skupinu NSG je přidružena k.

### <a name="view-existing-nsgs"></a>Zobrazit existující skupiny Nsg

Chcete-li zobrazit všechny existující skupiny Nsg v předplatném, proveďte následující kroky:

1. V prohlížeči přejděte na http://portal.azure.com a v případě potřeby se přihlaste pomocí účtu Azure.

2. Klikněte na tlačítko **procházet >** > **skupin zabezpečení sítě**.

    ![Portál Azure – skupiny Nsg](./media/virtual-network-manage-nsg-arm-portal/figure1.png)

3. Zkontrolujte seznam skupin Nsg v **skupin zabezpečení sítě** okno.

    ![Portál Azure – skupiny Nsg](./media/virtual-network-manage-nsg-arm-portal/figure2.png)

### <a name="view-nsgs-in-a-resource-group"></a>Zobrazení skupiny Nsg ve skupině prostředků

Chcete-li zobrazit seznam skupin Nsg v **RG NSG** prostředků skupiny, proveďte následující kroky:

1. Klikněte na tlačítko **skupiny prostředků >** > **RG NSG** > **...** .

    ![Portál Azure – skupiny Nsg](./media/virtual-network-manage-nsg-arm-portal/figure3.png)

2. V seznamu prostředků, vyhledejte položky zobrazení na ikonu NSG, jak je znázorněno v **prostředky** okno níže.

    ![Portál Azure – skupiny Nsg](./media/virtual-network-manage-nsg-arm-portal/figure4.png)

### <a name="list-all-rules-for-an-nsg"></a>Seznam všech pravidel pro skupiny NSG

Chcete-li zobrazit pravidla s názvem skupiny NSG **NSG front-endu**, proveďte následující kroky:

1. Z **skupin zabezpečení sítě** okno, nebo **prostředky** uvedené výše, klikněte na **NSG front-endu**.

2. V **nastavení** , klikněte na **příchozí pravidla zabezpečení**.

    ![Portál Azure – skupiny Nsg](./media/virtual-network-manage-nsg-arm-portal/figure5.png)

3. **Příchozí pravidla zabezpečení** jak je uvedeno níže, zobrazí se okno.

    ![Portál Azure – skupiny Nsg](./media/virtual-network-manage-nsg-arm-portal/figure6.png)

4. V **nastavení** , klikněte na **odchozí pravidla zabezpečení** zobrazíte odchozí pravidla.

    > [!NOTE]
    > Chcete-li zobrazit výchozí pravidla, klikněte na tlačítko **výchozí pravidla** ikonu v horní části okna, která zobrazuje pravidla.
    >

### <a name="view-nsgs-associations"></a>Zobrazte přidružení skupiny Nsg

Chcete-li zobrazit prostředky **NSG front-endu** NSG je spojený s, proveďte následující kroky:

1. Z **skupin zabezpečení sítě** okno, nebo **prostředky** uvedené výše, klikněte na **NSG front-endu**.

2. V **nastavení** , klikněte na **podsítě** Chcete-li zobrazit, jaké podsítě jsou přidružené k této skupině.

    ![Portál Azure – skupiny Nsg](./media/virtual-network-manage-nsg-arm-portal/figure7.png)

3. V **nastavení** , klikněte na **síťových rozhraní** Chcete-li zobrazit, co jsou přidružené k této skupině síťových adaptérů.

## <a name="manage-rules"></a>Spravovat pravidla
Můžete přidat pravidla do existující skupiny NSG, upravit stávající pravidla a odstranit pravidla.

### <a name="add-a-rule"></a>Přidání pravidla
Chcete-li přidat pravidlo, které povoluje **příchozí** přenosy na portu **443** z libovolného počítače k **NSG front-endu** NSG, proveďte následující kroky:

1. Z **skupin zabezpečení sítě** okno, nebo **prostředky** uvedené výše, klikněte na **NSG front-endu**.
2. V **nastavení** , klikněte na **příchozí pravidla zabezpečení**.
3. V **příchozí pravidla zabezpečení** okně klikněte na tlačítko **přidat**. Pak na **přidat příchozí pravidlo zabezpečení** okno, zadejte hodnoty, jak je uvedeno níže a potom klikněte na **OK**.

    ![Portál Azure – skupiny Nsg](./media/virtual-network-manage-nsg-arm-portal/figure8.png)

    Za několik sekund, Všimněte si nové pravidlo v **příchozí pravidla zabezpečení** okno.

    ![Portál Azure – skupiny Nsg](./media/virtual-network-manage-nsg-arm-portal/figure9.png)

### <a name="change-a-rule"></a>Změna pravidla
Chcete-li změnit pravidlo vytvořili výše, které pokud chcete povolit příchozí přenosy z **Internet** pouze, proveďte následující kroky:

1. Z **skupin zabezpečení sítě** okno, nebo **prostředky** uvedené výše, klikněte na **NSG front-endu**.
2. V **nastavení** , klikněte na pravidlo vytvořili výše.
3. V **povolit https** okně změnu **zdroj** vlastnost, jak je uvedeno níže a pak klikněte na **Uložit**.

    ![Portál Azure – skupiny Nsg](./media/virtual-network-manage-nsg-arm-portal/figure10.png)

### <a name="delete-a-rule"></a>Odstranění pravidla

Pokud chcete odstranit pravidlo vytvořili výše, proveďte následující kroky:

1. Z **skupin zabezpečení sítě** okno, nebo **prostředky** uvedené výše, klikněte na **NSG front-endu**.
2. V **nastavení** , klikněte na pravidlo vytvořili výše.
3. V **povolit https** okně klikněte na tlačítko **odstranit**a potom klikněte na **Ano**.

    ![Portál Azure – skupiny Nsg](./media/virtual-network-manage-nsg-arm-portal/figure11.png)

## <a name="manage-associations"></a>Správa přidružení
Můžete přidružit skupiny NSG k podsítí a síťových karet. Můžete také zrušit přidružení skupiny NSG ze všech prostředků, které je přidružené k.

### <a name="associate-an-nsg-to-a-nic"></a>Přidružení skupiny NSG k síťové karty
Pro přidružení **NSG front-endu** NSG k **TestNICWeb1** síťovou kartu, proveďte následující kroky:

1. Z **skupin zabezpečení sítě** okno, nebo **prostředky** uvedené výše, klikněte na **NSG front-endu**.
2. V **nastavení** , klikněte na **síťových rozhraní** > **přidružit** > **TestNICWeb1**.

    ![Portál Azure – skupiny Nsg](./media/virtual-network-manage-nsg-arm-portal/figure12.png)

### <a name="dissociate-an-nsg-from-a-nic"></a>Zrušit přidružení skupiny NSG z síťový adaptér

Zrušení přidružení **NSG front-endu** NSG z **TestNICWeb1** síťovou kartu, proveďte následující kroky:

1. Z portálu Azure klikněte na tlačítko **skupiny prostředků >** > **RG NSG** > **...**   >  **TestNICWeb1**.

2. V **TestNICWeb1** okně klikněte na tlačítko **změnit zabezpečení...**   >  **Žádné**.

    ![Portál Azure – skupiny Nsg](./media/virtual-network-manage-nsg-arm-portal/figure13.png)

> [!NOTE]
> Toto okno můžete taky přidružit síťovou kartu k žádné existující skupina NSG.
>

### <a name="dissociate-an-nsg-from-a-subnet"></a>Zrušit přidružení skupiny NSG z podsítě

Zrušení přidružení **NSG front-endu** NSG z **front-endu** podsíť, proveďte následující kroky:

1. Z portálu Azure klikněte na tlačítko **skupiny prostředků >** > **RG NSG** > **...**   >  **TestVNet**.

2. V **nastavení** okně klikněte na tlačítko **podsítě** > **front-endu** > **skupinu zabezpečení sítě**  >  **Žádné**.

    ![Portál Azure – skupiny Nsg](./media/virtual-network-manage-nsg-arm-portal/figure14.png)

3. V **front-endu** okně klikněte na tlačítko **Uložit**.

    ![Portál Azure – skupiny Nsg](./media/virtual-network-manage-nsg-arm-portal/figure15.png)

### <a name="associate-an-nsg-to-a-subnet"></a>Přidružení skupiny NSG k podsíti

Pro přidružení **NSG front-endu** NSG k **FronEnd** podsíť znovu, proveďte následující kroky:

1. Z portálu Azure klikněte na tlačítko **skupiny prostředků >** > **RG NSG** > **...**   >  **TestVNet**.
2. V **nastavení** okně klikněte na tlačítko **podsítě** > **front-endu** > **skupinu zabezpečení sítě** > **NSG front-endu**.
3. V **front-endu** okně klikněte na tlačítko **Uložit**.

> [!NOTE]
> Můžete také přidružíte skupinu NSG k podsíti z thh NSG na **nastavení** okno.
>

## <a name="delete-an-nsg"></a>Odstranit skupinu NSG
Skupinu NSG můžete odstranit, pouze pokud má není přidružen k žádnému prostředku. Chcete-li odstranit skupinu NSG, proveďte následující kroky:.

1. Z portálu Azure klikněte na tlačítko **skupiny prostředků >** > **RG NSG** > **...**   >  **NSG front-endu**.
2. V **nastavení** okně klikněte na tlačítko **síťových rozhraní**.
3. Pokud nejsou žádné síťové adaptéry uvedené, klikněte na síťový adaptér a postupujte podle kroku 2 v [zrušit přidružení skupiny NSG z síťový adaptér](#Dissociate-an-NSG-from-a-NIC).
4. Opakujte krok 3 pro každý síťový adaptér.
5. V **nastavení** okně klikněte na tlačítko **podsítě**.
6. Pokud neexistují žádné podsítě uvedena, klikněte na podsíť a postupujte podle kroků 2 a 3 v [zrušit přidružení skupiny NSG z podsítě](#Dissociate-an-NSG-from-a-subnet).
7. Posune zleva **NSG front-endu** okno, pak klikněte na tlačítko **odstranit** > **Ano**.

    ![Portál Azure – skupiny Nsg](./media/virtual-network-manage-nsg-arm-portal/figure16.png)

## <a name="next-steps"></a>Další kroky
* [Povolit protokolování](virtual-network-nsg-manage-log.md) pro skupiny Nsg.
