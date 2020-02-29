---
title: Konfigurace privátních IP adres pro virtuální počítače – Azure Portal
description: Přečtěte si, jak nakonfigurovat privátní IP adresy pro virtuální počítače pomocí Azure Portal.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: tysonn
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: kumud
ms.openlocfilehash: b1019b15463a03282c5d1bd8f0a878433d7f488e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199559"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal"></a>Konfigurace privátních IP adres pro virtuální počítač pomocí Azure Portal

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Následující vzorové kroky očekávají, že už je vytvořené jednoduché prostředí. Chcete-li spustit postup, který je zobrazen v tomto dokumentu, nejdříve Sestavte testovací prostředí popsané v tématu [vytvoření virtuální sítě](quick-create-portal.md).

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Postup vytvoření virtuálního počítače pro testování statických privátních IP adres
Během vytváření virtuálního počítače v režimu nasazení Správce prostředků pomocí Azure Portal nemůžete nastavit statickou privátní IP adresu. Nejprve musíte vytvořit virtuální počítač a potom nastavit jeho privátní IP adresu na statickou.

Pokud chcete vytvořit virtuální počítač s názvem *DNS01* v podsíti *front-endu* virtuální sítě s názvem *TestVNet*, postupujte takto:

1. V prohlížeči přejděte na https://portal.azure.com a v případě potřeby se přihlaste pomocí účtu Azure.
2. Klikněte na **vytvořit prostředek** > **COMPUTE** > **Windows Server 2012 R2 Datacenter**. Všimněte si, že seznam **Vyberte model nasazení** již obsahuje **Správce prostředků**a potom klikněte na tlačítko **vytvořit**, jak je vidět na následujícím obrázku.
   
    ![Vytvořit virtuální počítač v Azure Portal](./media/virtual-networks-static-ip-arm-pportal/figure01.png)
3. V podokně **základy** zadejte název virtuálního počítače, který se má vytvořit (*DNS01* ve scénáři), účet místního správce a heslo, jak je vidět na následujícím obrázku.
   
    ![Podokno základy](./media/virtual-networks-static-ip-arm-pportal/figure02.png)
4. Ujistěte se, že vybrané **umístění** je *střed USA*, klikněte **na vybrat existující** v části **Skupina prostředků**, pak znovu klikněte na **Skupina prostředků** , potom klikněte na *TestRG*a pak klikněte na **OK**.
   
    ![Podokno základy](./media/virtual-networks-static-ip-arm-pportal/figure03.png)
5. V podokně **Zvolte velikost** vyberte **a1 Standard**a pak klikněte na **Vybrat**.
   
    ![Zvolit podokno velikosti](./media/virtual-networks-static-ip-arm-pportal/figure04.png)    
6. V podokně **Nastavení** se ujistěte, že jsou nastavené vlastnosti s následujícími hodnotami, a pak klikněte na **OK**.
   
    -**účet úložiště**: *vnetstorage*
   
   * **Síť**: *TestVNet*
   * **Podsíť**: *front-end*
     
     ![Zvolit podokno velikosti](./media/virtual-networks-static-ip-arm-pportal/figure05.png)     
7. V podokně **Souhrn** klikněte na tlačítko **OK**. Všimněte si, že na řídicím panelu se zobrazí následující dlaždice.
   
    ![Vytvořit virtuální počítač v Azure Portal](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

Doporučuje se, abyste privátní IP adresu přiřazenou k virtuálnímu počítači Azure v operačním systému virtuálního počítače nepřiřadili staticky, pokud to není nutné, například při [přiřazování více IP adres k virtuálnímu počítači s Windows](virtual-network-multiple-ip-addresses-portal.md). Pokud ručně nastavíte privátní IP adresu v operačním systému, ujistěte se, že se jedná o stejnou adresu jako soukromá IP adresa přiřazená [síťovému rozhraní](virtual-network-network-interface-addresses.md#change-ip-address-settings)Azure, nebo můžete ztratit připojení k virtuálnímu počítači. Přečtěte si další informace o nastavení [privátních IP adres](virtual-network-network-interface-addresses.md#private) . Veřejná IP adresa přidělená k virtuálnímu počítači Azure v rámci operačního systému virtuálního počítače by nikdy ručně přiřadit.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Jak načíst statické privátní informace o IP adrese virtuálního počítače
Pokud chcete zobrazit informace o statických privátních IP adresách pro virtuální počítač vytvořený pomocí výše uvedených kroků, proveďte následující kroky.

1. V Azure Portal klikněte na **Procházet všechny** > **virtuální počítače** > **DNS01** > **všechna nastavení** > **Síťová rozhraní** a pak klikněte na uvedené jediné síťové rozhraní.
   
    ![Dlaždice nasazení virtuálního počítače](./media/virtual-networks-static-ip-arm-pportal/figure07.png)
2. V podokně **síťové rozhraní** klikněte na **všechna nastavení** > **IP adresy** a Všimněte si hodnot **přiřazení** a **IP adresy** .
   
    ![Dlaždice nasazení virtuálního počítače](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Postup přidání statické privátní IP adresy do existujícího virtuálního počítače
Pokud chcete přidat statickou privátní IP adresu k virtuálnímu počítači vytvořenému pomocí výše uvedeného postupu, postupujte takto:

1. V podokně **IP adresy** zobrazené výše klikněte na **statické** v části **přiřazení**.
2. Zadejte *192.168.1.101* pro **IP adresu**a pak klikněte na **Uložit**.
   
    ![Vytvořit virtuální počítač v Azure Portal](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

> [!NOTE]
> Když kliknete na **Uložit**, všimnete si, že přiřazení je pořád nastavené na **dynamickou**, to znamená, že zadaná IP adresa se už používá. Zkuste použít jinou IP adresu.
> 
> 

Doporučuje se, abyste privátní IP adresu přiřazenou k virtuálnímu počítači Azure v operačním systému virtuálního počítače nepřiřadili staticky, pokud to není nutné, například při [přiřazování více IP adres k virtuálnímu počítači s Windows](virtual-network-multiple-ip-addresses-portal.md). Pokud ručně nastavíte privátní IP adresu v operačním systému, ujistěte se, že se jedná o stejnou adresu jako soukromá IP adresa přiřazená [síťovému rozhraní](virtual-network-network-interface-addresses.md#change-ip-address-settings)Azure, nebo můžete ztratit připojení k virtuálnímu počítači. Přečtěte si další informace o nastavení [privátních IP adres](virtual-network-network-interface-addresses.md#private) . Veřejná IP adresa přidělená k virtuálnímu počítači Azure v rámci operačního systému virtuálního počítače by nikdy ručně přiřadit.

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Postup odebrání statickou privátní IP adresu z virtuálního počítače
Pokud chcete odebrat statickou privátní IP adresu z virtuálního počítače, který jste vytvořili výše, proveďte následující krok:

V podokně **IP adresy** zobrazené výše klikněte na **dynamické** v části **přiřazení**a pak klikněte na **Uložit**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Nastavení IP adresy v rámci operačního systému

Doporučuje se, abyste privátní IP adresu přiřazenou k virtuálnímu počítači Azure v operačním systému virtuálního počítače nepřiřadili staticky, pokud to není nutné, například při [přiřazování více IP adres k virtuálnímu počítači s Windows](virtual-network-multiple-ip-addresses-portal.md). Pokud ručně nastavíte privátní IP adresu v operačním systému, ujistěte se, že se jedná o stejnou adresu jako soukromá IP adresa přiřazená [síťovému rozhraní](virtual-network-network-interface-addresses.md#change-ip-address-settings)Azure, nebo můžete ztratit připojení k virtuálnímu počítači. Přečtěte si další informace o nastavení [privátních IP adres](virtual-network-network-interface-addresses.md#private) . Veřejná IP adresa přidělená k virtuálnímu počítači Azure v rámci operačního systému virtuálního počítače by nikdy ručně přiřadit.

## <a name="next-steps"></a>Další kroky

Přečtěte si informace o správě [nastavení IP adres](virtual-network-network-interface-addresses.md).

