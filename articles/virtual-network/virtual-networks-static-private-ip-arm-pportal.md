---
title: Konfigurace privátních IP adres pro virtuální počítače – Azure portal | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat privátní IP adresy pro virtuální počítače pomocí webu Azure portal.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38705880"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal"></a>Konfigurace privátních IP adres pro virtuální počítač pomocí webu Azure portal

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-networks-static-private-ip-arm-pportal.md)
> * [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
> * [Azure CLI](virtual-networks-static-private-ip-arm-cli.md)
> * [Azure portal (Classic)](virtual-networks-static-private-ip-classic-pportal.md)
> * [PowerShell (Classic)](virtual-networks-static-private-ip-classic-ps.md)
> * [Rozhraní příkazového řádku Azure (Classic)](virtual-networks-static-private-ip-classic-cli.md)


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Tento článek se týká modelu nasazení Resource Manager. Můžete také [spravovat statickou privátní IP adresou v modelu nasazení classic](virtual-networks-static-private-ip-classic-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Následující ukázkový postup očekávat jednoduché prostředí už vytvořili. Pokud chcete spustit kroky, jak jsou zobrazeny v tomto dokumentu, nejprve vytvořit testovací prostředí popsané v [vytvoření virtuální sítě](quick-create-portal.md).

## <a name="how-to-create-a-vm-for-testing-static-private-ip-addresses"></a>Vytvoření virtuálního počítače pro testování statické privátní IP adresy
Nelze nastavit statickou privátní IP adresu během vytváření virtuálního počítače v režimu nasazení Resource Manageru pomocí webu Azure portal. Je nutné nejprve vytvořit virtuální počítač a potom nastavte jeho privátní IP adresa jako statický.

Vytvoření virtuálního počítače s názvem *DNS01* v *front-endu* podsíti virtuální sítě s názvem *TestVNet*, postupujte podle těchto kroků:

1. V prohlížeči přejděte na http://portal.azure.com a v případě potřeby se přihlaste pomocí účtu Azure.
2. Klikněte na tlačítko **vytvořit prostředek** > **Compute** > **systému Windows Server 2012 R2 Datacenter**, Všimněte si, že **vyberte nasazení model** seznam již obsahuje **Resource Manageru**a potom klikněte na tlačítko **vytvořit**, jak je znázorněno na následujícím obrázku.
   
    ![Vytvoření virtuálního počítače na webu Azure portal](./media/virtual-networks-static-ip-arm-pportal/figure01.png)
3. V **Základy** podokně, zadejte název virtuálního počítače k vytvoření (*DNS01* ve scénáři), účet místního správce a heslo, jak je znázorněno na následujícím obrázku.
   
    ![Základní informace o podokně](./media/virtual-networks-static-ip-arm-pportal/figure02.png)
4. Ujistěte se, že **umístění** vybraného je *USA (střed)*, klikněte na **vybrat existující** pod **skupinu prostředků**, pak klikněte na tlačítko **Skupiny prostředků** znovu, klikněte na *TestRG*a potom klikněte na tlačítko **OK**.
   
    ![Základní informace o podokně](./media/virtual-networks-static-ip-arm-pportal/figure03.png)
5. V **zvolte velikost** vyberte **A1 Standard**a potom klikněte na tlačítko **vyberte**.
   
    ![Zvolte velikost podokna](./media/virtual-networks-static-ip-arm-pportal/figure04.png)    
6. V **nastavení** podokně zkontrolujte, že vlastnosti jsou nastaveny následující hodnoty a pak klikněte na tlačítko **OK**.
   
    -**Účet úložiště**: *vnetstorage*
   
   * **Síť**: *TestVNet*
   * **Podsíť**: *front-endu*
     
     ![Zvolte velikost podokna](./media/virtual-networks-static-ip-arm-pportal/figure05.png)     
7. V **Souhrn** podokně klikněte na tlačítko **OK**. Všimněte si, že následující dlaždice na řídicím panelu zobrazí.
   
    ![Vytvoření virtuálního počítače na webu Azure portal](./media/virtual-networks-static-ip-arm-pportal/figure06.png)

Doporučuje se, že nepřiřadíte staticky privátní IP adresa přiřazená virtuální počítač Azure v rámci operačního systému virtuálního počítače, není-li nezbytné, například kdy [přidělení více IP adres k virtuálnímu počítači s Windows](virtual-network-multiple-ip-addresses-portal.md). Pokud jste ručně nastavili privátní IP adresu v rámci operačního systému, ujistěte se, že se jedná o stejné adrese jako privátní IP adresy přiřazené k Azure [síťové rozhraní](virtual-network-network-interface-addresses.md#change-ip-address-settings), nebo může ztratit připojení k virtuálnímu počítači. Další informace o [privátní IP adresu](virtual-network-network-interface-addresses.md#private) nastavení. Veřejná IP adresa přidělená k virtuálnímu počítači Azure v rámci operačního systému virtuálního počítače by nikdy ručně přiřadit.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Jak načíst statické privátní informace o IP adrese virtuálního počítače
Chcete-li zobrazit statické informace privátní IP adresu pro virtuální počítač vytvořený pomocí výše uvedených kroků, proveďte následující kroky.

1. Na webu Azure Portal, klikněte na tlačítko **Procházet vše** > **virtuálních počítačů** > **DNS01** > **všechna nastavení**  >  **Síťová rozhraní** a potom klikněte na tlačítko na jedinou síťovém rozhraní uvedené.
   
    ![Dlaždice nasazování virtuálních počítačů](./media/virtual-networks-static-ip-arm-pportal/figure07.png)
2. V **síťové rozhraní** podokně klikněte na tlačítko **všechna nastavení** > **IP adresy** a Všimněte si, že **přiřazení** a  **IP adresa** hodnoty.
   
    ![Dlaždice nasazování virtuálních počítačů](./media/virtual-networks-static-ip-arm-pportal/figure08.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Postup přidání statické privátní IP adresu do existujícího virtuálního počítače
Přidání statické privátní IP adresu pro virtuální počítač vytvořený pomocí výše uvedených kroků, postupujte podle těchto kroků:

1. Z **IP adresy** podokně vidíte výše, klikněte na tlačítko **statické** pod **přiřazení**.
2. Typ *192.168.1.101* pro **IP adresu**a potom klikněte na tlačítko **Uložit**.
   
    ![Vytvoření virtuálního počítače na webu Azure portal](./media/virtual-networks-static-ip-arm-pportal/figure09.png)

> [!NOTE]
> Pokud po kliknutí na tlačítko **Uložit**, Všimněte si, že přiřazení je stále nastaveno na **dynamické**, to znamená, že zadaná adresa IP je již používán. Zkuste jinou IP adresu.
> 
> 

Doporučuje se, že nepřiřadíte staticky privátní IP adresa přiřazená virtuální počítač Azure v rámci operačního systému virtuálního počítače, není-li nezbytné, například kdy [přidělení více IP adres k virtuálnímu počítači s Windows](virtual-network-multiple-ip-addresses-portal.md). Pokud jste ručně nastavili privátní IP adresu v rámci operačního systému, ujistěte se, že se jedná o stejné adrese jako privátní IP adresy přiřazené k Azure [síťové rozhraní](virtual-network-network-interface-addresses.md#change-ip-address-settings), nebo může ztratit připojení k virtuálnímu počítači. Další informace o [privátní IP adresu](virtual-network-network-interface-addresses.md#private) nastavení. Veřejná IP adresa přidělená k virtuálnímu počítači Azure v rámci operačního systému virtuálního počítače by nikdy ručně přiřadit.

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Postup odebrání statickou privátní IP adresu z virtuálního počítače
Odebrání virtuálního počítače vytvořeného výše statické privátní IP adresy, proveďte následující kroky:

Z **IP adresy** podokně vidíte výše, klikněte na tlačítko **dynamické** pod **přiřazení**a potom klikněte na tlačítko **Uložit**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Nastavení IP adresy v rámci operačního systému

Doporučuje se, že nepřiřadíte staticky privátní IP adresa přiřazená virtuální počítač Azure v rámci operačního systému virtuálního počítače, není-li nezbytné, například kdy [přidělení více IP adres k virtuálnímu počítači s Windows](virtual-network-multiple-ip-addresses-portal.md). Pokud jste ručně nastavili privátní IP adresu v rámci operačního systému, ujistěte se, že se jedná o stejné adrese jako privátní IP adresy přiřazené k Azure [síťové rozhraní](virtual-network-network-interface-addresses.md#change-ip-address-settings), nebo může ztratit připojení k virtuálnímu počítači. Další informace o [privátní IP adresu](virtual-network-network-interface-addresses.md#private) nastavení. Veřejná IP adresa přidělená k virtuálnímu počítači Azure v rámci operačního systému virtuálního počítače by nikdy ručně přiřadit.

## <a name="next-steps"></a>Další postup

Další informace o správě [nastavení IP adresy](virtual-network-network-interface-addresses.md).

