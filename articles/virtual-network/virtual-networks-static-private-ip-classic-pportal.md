---
title: Nakonfigurovat privátní IP adresy pro virtuální počítače (klasické) - portálu Azure | Microsoft Docs
description: Zjistěte, jak nakonfigurovat privátní IP adresy pro virtuální počítače (klasické) pomocí portálu Azure.
services: virtual-network
documentationcenter: na
author: genli
manager: cshepard
editor: tysonn
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7b81f0fc1d504a0cb3422e003aa4ad7986396652
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Nakonfigurovat privátní IP adresy pro virtuální počítač (klasický) pomocí portálu Azure

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Tento článek se týká modelu nasazení Classic. Můžete také [spravovat statickou privátní IP adresou v modelu nasazení Resource Manager](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Ukázka kroky, které postupujte podle očekávat jednoduché prostředí již vytvořeny. Pokud chcete spustit kroky, jak jsou zobrazeny v tomto dokumentu, nejprve vytvořit testovací prostředí popsané v [vytvoření virtuální sítě](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Postup při vytváření virtuálního počítače zadat statickou privátní IP adresu
Vytvoření virtuálního počítače s názvem *DNS01* v *front-endu* podsíť virtuální sítě s názvem *TestVNet* se statickou privátní IP z *192.168.1.101*, dokončení Následující kroky:

1. V prohlížeči přejděte na https://portal.azure.com a v případě potřeby se přihlaste pomocí účtu Azure.
2. Vyberte **nový** > **výpočetní** > **Windows Server 2012 R2 Datacenter**, Všimněte si, že **vybrat model nasazení** seznam již ukazuje **Classic**a potom vyberte **vytvořit**.
   
    ![Vytvoření virtuálního počítače na portálu Azure](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. V části **vytvoření virtuálního počítače**, zadejte název virtuálního počítače, který se má vytvořit (*DNS01* ve scénáři), účet místního správce a heslo.
   
    ![Vytvoření virtuálního počítače na portálu Azure](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Vyberte **volitelné konfiguraci** > **sítě** > **virtuální sítě**a potom vyberte **TestVNet** . Pokud **TestVNet** není k dispozici, ujistěte se, že používáte *střed USA* umístění a vytvořili testovací prostředí popsané na začátku tohoto článku.
   
    ![Vytvoření virtuálního počítače na portálu Azure](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. V části **sítě**, zkontrolujte, zda je aktuálně vybrané podsítě *front-endu*, pak vyberte **IP adresy**v části **přiřazení IP adresy** Vyberte **statické**a potom zadejte *192.168.1.101* pro **IP adresu** jak vidíte níže.
   
    ![Vytvoření virtuálního počítače na portálu Azure](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Vyberte **OK** pod **IP adresy**, vyberte **OK** pod **sítě**a potom vyberte **OK** v části **Volitelné konfigurace**.
7. V části **vytvoření virtuálního počítače**, vyberte **vytvořit**. Všimněte si dlaždice níže zobrazí v řídicím panelu:
   
    ![Vytvoření virtuálního počítače na portálu Azure](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Jak načíst statickou privátní IP adresu informace pro virtuální počítač
Chcete-li zobrazit statických informací privátní IP adresu pro virtuální počítač vytvořený s výše uvedených kroků, proveďte následující postup.

1. Z portálu Azure vyberte **Procházet vše** > **virtuálních počítačů (klasické)** > **DNS01** > **všechny nastavení** > **IP adresy** a Všimněte si přiřazení IP adresy a IP adresu, jak vidíte níže.
   
    ![Vytvoření virtuálního počítače na portálu Azure](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Postup odebrání statickou privátní IP adresu z virtuálního počítače

V části **IP adresy**, vyberte **dynamické** napravo od **přiřazení IP adresy**, vyberte **Uložit**a potom vyberte  **Ano**, jak je znázorněno na následujícím obrázku:
   
    ![Create VM in Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Postup přidání statickou privátní IP adresu do stávajícího virtuálního počítače

1. V části **IP adresy**uvedené dříve, vyberte **statické** napravo od **přiřazení IP adresy**.
2. Typ *192.168.1.101* pro **IP adresu**, vyberte **Uložit**a potom vyberte **Ano**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Nastavení IP adresy v rámci operačního systému

Doporučuje se, že nepřiřadíte staticky privátní IP přiřazené k virtuálnímu počítači Azure v rámci operačního systému virtuálního počítače, pokud nezbytné. Pokud ručně nastavit privátní IP adresu v operačním systému, ujistěte se, že se jedná o stejnou adresu jako přiřazené k virtuálnímu počítači Azure privátní IP adresy nebo ztratíte připojení k virtuálnímu počítači. Byste měli přiřadit nikdy ručně veřejnou IP adresu přiřazené pro virtuální počítač Azure v rámci operačního systému virtuálního počítače.

## <a name="next-steps"></a>Další postup
* Další informace o [vyhrazené veřejné IP adresy](virtual-networks-reserved-public-ip.md) adresy.
* Další informace o [veřejné IP (splnění) na úrovni instance](virtual-networks-instance-level-public-ip.md) adresy.
* Obrátit [vyhrazené IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx).

