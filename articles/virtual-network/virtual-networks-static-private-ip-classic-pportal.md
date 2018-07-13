---
title: Konfigurace privátních IP adres pro virtuální počítače (Classic) – Azure portal | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat privátní IP adresy pro virtuální počítače (Classic) pomocí webu Azure portal.
services: virtual-network
documentationcenter: na
author: genlin
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
ms.openlocfilehash: 1aae74d8077a75e5ab556703b1c1531f540bbdb4
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38698560"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Konfigurace privátních IP adres pro virtuální počítač (Classic) pomocí webu Azure portal

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Tento článek se týká modelu nasazení Classic. Můžete také [spravovat statickou privátní IP adresou v modelu nasazení Resource Manager](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Následující ukázka kroky očekávat jednoduché prostředí už vytvořili. Pokud chcete spustit kroky, jak jsou zobrazeny v tomto dokumentu, nejprve vytvořit testovací prostředí popsané v [vytvořit síť vnet](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Jak zadat statickou privátní IP adresu při vytváření virtuálního počítače
Vytvoření virtuálního počítače s názvem *DNS01* v *front-endu* podsíti virtuální sítě s názvem *TestVNet* se statickou privátní IP adresou z *192.168.1.101*, dokončení Následující kroky:

1. V prohlížeči přejděte na https://portal.azure.com a v případě potřeby se přihlaste pomocí účtu Azure.
2. Vyberte **nový** > **Compute** > **systému Windows Server 2012 R2 Datacenter**, Všimněte si, že **vybrat model nasazení** seznam již obsahuje **Classic**a pak vyberte **vytvořit**.
   
    ![Vytvoření virtuálního počítače na webu Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. V části **vytvořit virtuální počítač**, zadejte název virtuálního počítače, který se má vytvořit (*DNS01* ve scénáři), účet místního správce a heslo.
   
    ![Vytvoření virtuálního počítače na webu Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Vyberte **volitelná konfigurace** > **sítě** > **virtuální sítě**a pak vyberte **TestVNet** . Pokud **TestVNet** není k dispozici, ujistěte se, že používáte *USA (střed)* umístění a testovací prostředí popsané na začátku tohoto článku vytvořili.
   
    ![Vytvoření virtuálního počítače na webu Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. V části **sítě**, ujistěte se, že je aktuálně vybraná podsíť *front-endu*a pak vyberte **IP adresy**v části **přiřazení IP adresy** Vyberte **statické**a pak zadejte *192.168.1.101* pro **IP adresu** jak vidíte níže.
   
    ![Vytvoření virtuálního počítače na webu Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Vyberte **OK** pod **IP adresy**vyberte **OK** pod **sítě**a pak vyberte **OK** v části **Volitelná konfigurace**.
7. V části **vytvořit virtuální počítač**vyberte **vytvořit**. Všimněte si, že na dlaždici níže zobrazené v řídicím panelu:
   
    ![Vytvoření virtuálního počítače na webu Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Jak načíst statické privátní informace o IP adrese virtuálního počítače
Pokud chcete zobrazit statické informace privátní IP adresu pro virtuální počítač vytvořený pomocí výše uvedených kroků, spusťte následující postup.

1. Na webu Azure Portal, vyberte **Procházet vše** > **virtuální počítače (classic)** > **DNS01** > **všechny nastavení** > **IP adresy** a Všimněte si přidělování IP adres a IP adresu, jak je vidět níže.
   
    ![Vytvoření virtuálního počítače na webu Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Postup odebrání statickou privátní IP adresu z virtuálního počítače

V části **IP adresy**vyberte **dynamické** napravo od **přiřazení IP adresy**vyberte **Uložit**a pak vyberte  **Ano**, jak je znázorněno na následujícím obrázku:
   
    ![Create VM in Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Postup přidání statické privátní IP adresu do existujícího virtuálního počítače

1. V části **IP adresy**uvedeno dříve, vyberte **statické** napravo od **přiřazení IP adresy**.
2. Typ *192.168.1.101* pro **IP adresu**vyberte **Uložit**a pak vyberte **Ano**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Nastavení IP adresy v rámci operačního systému

Doporučuje se, že nepřiřadíte staticky privátní IP adresa přiřazená virtuální počítač Azure v rámci operačního systému virtuálního počítače, není-li nezbytné. Pokud jste ručně nastavili privátní IP adresu v rámci operačního systému, ujistěte se, že se jedná o stejnou adresu jako privátní IP adresa přiřazená k virtuálnímu počítači Azure nebo ztratíte připojení k virtuálnímu počítači. Veřejná IP adresa přidělená k virtuálnímu počítači Azure v rámci operačního systému virtuálního počítače by nikdy ručně přiřadit.

## <a name="next-steps"></a>Další postup
* Další informace o [vyhrazené veřejné IP adresy](virtual-networks-reserved-public-ip.md) adresy.
* Další informace o [veřejné IP (ILPIP) na úrovni instance](virtual-networks-instance-level-public-ip.md) adresy.
* Poraďte [vyhrazené IP rozhraní REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx).

