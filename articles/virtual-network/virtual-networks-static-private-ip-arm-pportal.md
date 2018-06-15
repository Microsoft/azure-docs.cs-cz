---
title: Nakonfigurovat privátní IP adresy pro virtuální počítače - portálu Azure | Microsoft Docs
description: Zjistěte, jak nakonfigurovat privátní IP adresy pro virtuální počítače pomocí portálu Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ff9d025980f80cb77246ea12dbf2e9bcedd73f86
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31423273"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal"></a>Nakonfigurovat privátní IP adresy pro virtuální počítač pomocí portálu Azure

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-networks-static-private-ip-arm-pportal.md)
> * [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
> * [Azure CLI](virtual-networks-static-private-ip-arm-cli.md)
> * [Portál Azure (klasický)](virtual-networks-static-private-ip-classic-pportal.md)
> * [PowerShell (Classic)](virtual-networks-static-private-ip-classic-ps.md)
> * [Rozhraní příkazového řádku Azure (klasický)](virtual-networks-static-private-ip-classic-cli.md)


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Tento článek se týká modelu nasazení Resource Manager. Můžete také [spravovat statickou privátní IP adresou v modelu nasazení classic](virtual-networks-static-private-ip-classic-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Následující ukázkový postup očekávat jednoduché prostředí již vytvořeny. Pokud chcete spustit kroky, jak jsou zobrazeny v tomto dokumentu, nejprve vytvořit testovací prostředí popsané v [vytvořit virtuální síť](quick-create-portal.md).

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Postup vytvoření virtuálního počítače pro testování statickou privátní IP adresy
Statickou privátní IP adresu nelze nastavit při vytváření virtuálního počítače v režimu Resource Manager nasazení pomocí portálu Azure. Je nutné nejprve vytvořit virtuální počítač a potom nastavte její privátní IP adresy na být statická.

Vytvoření virtuálního počítače s názvem *DNS01* v *front-endu* podsíť virtuální sítě s názvem *TestVNet*, postupujte takto:

1. V prohlížeči přejděte na http://portal.azure.com a v případě potřeby se přihlaste pomocí účtu Azure.
2. Klikněte na tlačítko **vytvořit prostředek** > **výpočetní** > **Windows Server 2012 R2 Datacenter**, Všimněte si, že **vyberte nasazení model** seznam již ukazuje **Resource Manager**a potom klikněte na **vytvořit**, jak je vidět na následujícím obrázku.
   
    ![Vytvoření virtuálního počítače na portálu Azure](./media/virtual-networks-static-ip-arm-pportal/figure01.png)
3. V **Základy** podokně, zadejte název virtuálního počítače vytvořit (*DNS01* ve scénáři), účet místního správce a hesla, jak je vidět na následujícím obrázku.
   
    ![Základy podokno](./media/virtual-networks-static-ip-arm-pportal/figure02.png)
4. Zajistěte, aby **umístění** vybraná *střed USA*, pak klikněte na tlačítko **vyberte existující** v části **skupiny prostředků**, klikněte **skupiny prostředků** znovu, klikněte *TestRG*a potom klikněte na **OK**.
   
    ![Základy podokno](./media/virtual-networks-static-ip-arm-pportal/figure03.png)
5. V **zvolte velikost** podokně, vyberte **A1 standardní**a potom klikněte na **vyberte**.
   
    ![Zvolte velikost podokna](./media/virtual-networks-static-ip-arm-pportal/figure04.png)    
6. V **nastavení** podokně, zkontrolujte, že vlastnosti se nastavují s následující hodnoty a pak klikněte na tlačítko **OK**.
   
    -**Účet úložiště**: *vnetstorage*
   
   * **Síť**: *TestVNet*
   * **Podsíť**: *front-endu*
     
     ![Zvolte velikost podokna](./media/virtual-networks-static-ip-arm-pportal/figure05.png)     
7. V **Souhrn** podokně klikněte na tlačítko **OK**. Všimněte si následujících dlaždice zobrazovaný v řídicím panelu.
   
    ![Vytvoření virtuálního počítače na portálu Azure](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

Doporučuje se, že nepřiřadíte staticky privátní IP přiřazené k virtuálnímu počítači Azure v rámci operačního systému virtuálního počítače, pokud nezbytné, jako např. kdy [přiřazení více IP adres pro virtuální počítač s Windows](virtual-network-multiple-ip-addresses-portal.md). Pokud ručně nastavit privátní IP adresu v operačním systému, zajistěte, aby byl stejnou adresu jako přiřazené Azure privátní IP adresy [síťové rozhraní](virtual-network-network-interface-addresses.md#change-ip-address-settings), nebo můžete ztratit připojení k virtuálnímu počítači. Další informace o [privátní IP adresu](virtual-network-network-interface-addresses.md#private) nastavení. Byste měli přiřadit nikdy ručně veřejnou IP adresu přiřazené pro virtuální počítač Azure v rámci operačního systému virtuálního počítače.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Jak načíst statickou privátní IP adresu informace pro virtuální počítač
Chcete-li zobrazit statické informace privátní IP adresu pro virtuální počítač vytvořený s výše uvedených kroků, spusťte následující kroky.

1. Z portálu Azure klikněte na tlačítko **Procházet vše** > **virtuální počítače** > **DNS01** > **všechna nastavení**  >  **Síťových rozhraní** a potom klikněte na uvedené jenom síťové rozhraní.
   
    ![Nasazení virtuálních počítačů dlaždice](./media/virtual-networks-static-ip-arm-pportal/figure07.png)
2. V **síťové rozhraní** podokně klikněte na tlačítko **všechna nastavení** > **IP adresy** a Všimněte si **přiřazení** a  **IP adresa** hodnoty.
   
    ![Nasazení virtuálních počítačů dlaždice](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Postup přidání statickou privátní IP adresu do stávajícího virtuálního počítače
Chcete-li přidat statickou privátní IP adresu na virtuální počítač vytvořený pomocí výše uvedených kroků, postupujte takto:

1. Z **IP adresy** podokně uvedené výše, klikněte na tlačítko **statické** pod **přiřazení**.
2. Typ *192.168.1.101* pro **IP adresu**a potom klikněte na **Uložit**.
   
    ![Vytvoření virtuálního počítače na portálu Azure](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

> [!NOTE]
> Pokud po kliknutí na **Uložit**, si všimnete, že je přiřazení stále nastavená na **dynamické**, znamená to zadaná IP adresa je již používán. Zkuste jinou IP adresu.
> 
> 

Doporučuje se, že nepřiřadíte staticky privátní IP přiřazené k virtuálnímu počítači Azure v rámci operačního systému virtuálního počítače, pokud nezbytné, jako např. kdy [přiřazení více IP adres pro virtuální počítač s Windows](virtual-network-multiple-ip-addresses-portal.md). Pokud ručně nastavit privátní IP adresu v operačním systému, zajistěte, aby byl stejnou adresu jako přiřazené Azure privátní IP adresy [síťové rozhraní](virtual-network-network-interface-addresses.md#change-ip-address-settings), nebo můžete ztratit připojení k virtuálnímu počítači. Další informace o [privátní IP adresu](virtual-network-network-interface-addresses.md#private) nastavení. Byste měli přiřadit nikdy ručně veřejnou IP adresu přiřazené pro virtuální počítač Azure v rámci operačního systému virtuálního počítače.

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Postup odebrání statickou privátní IP adresu z virtuálního počítače
Chcete-li odebrat statickou privátní IP adresou z virtuálního počítače vytvořili výše, proveďte následující krok:

Z **IP adresy** podokně uvedené výše, klikněte na tlačítko **dynamické** pod **přiřazení**a potom klikněte na **Uložit**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Nastavení IP adresy v rámci operačního systému

Doporučuje se, že nepřiřadíte staticky privátní IP přiřazené k virtuálnímu počítači Azure v rámci operačního systému virtuálního počítače, pokud nezbytné, jako např. kdy [přiřazení více IP adres pro virtuální počítač s Windows](virtual-network-multiple-ip-addresses-portal.md). Pokud ručně nastavit privátní IP adresu v operačním systému, zajistěte, aby byl stejnou adresu jako přiřazené Azure privátní IP adresy [síťové rozhraní](virtual-network-network-interface-addresses.md#change-ip-address-settings), nebo můžete ztratit připojení k virtuálnímu počítači. Další informace o [privátní IP adresu](virtual-network-network-interface-addresses.md#private) nastavení. Byste měli přiřadit nikdy ručně veřejnou IP adresu přiřazené pro virtuální počítač Azure v rámci operačního systému virtuálního počítače.

## <a name="next-steps"></a>Další postup

Další informace o správě [nastavení IP adresy](virtual-network-network-interface-addresses.md).

