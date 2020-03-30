---
title: Konfigurace privátních IP adres pro virtuální počítače (klasické) – portál Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak nakonfigurovat privátní IP adresy pro virtuální počítače (Classic) pomocí portálu Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: 9de9fd2eb7a46719b841a885ac173f8d9c1271c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71059108"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Konfigurace privátních IP adres pro virtuální počítač (Classic) pomocí portálu Azure

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Tento článek se týká modelu nasazení Classic. Statickou [privátní IP adresu](virtual-networks-static-private-ip-arm-pportal.md)můžete spravovat také v modelu nasazení Správce prostředků .

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Ukázkové kroky, které následují očekávat jednoduché prostředí již vytvořené. Pokud chcete spustit kroky tak, jak jsou zobrazeny v tomto dokumentu, nejprve vytvořte testovací prostředí popsané v [vytvoření virtuální sítě](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Jak zadat statickou privátní IP adresu při vytváření virtuálního počítačů
Chcete-li vytvořit virtuální hovirtuální ho dohledu *dns01* v podsíti *frontendu* virtuální sítě s názvem *TestVNet* se statickou privátní IP adresou *192.168.1.101*, postupujte takto:

1. V prohlížeči přejděte na https://portal.azure.com a v případě potřeby se přihlaste pomocí účtu Azure.
2. Vyberte **NOVÉ** > **Výpočetní** > **datové centrum Windows Server 2012 R2**, všimněte si, že seznam Vybrat model **nasazení** již zobrazuje **classic**, a pak vyberte **Vytvořit**.
   
    ![Vytvoření virtuálního počítače na webu Azure Portal](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. V **části Vytvořit virtuální hod**zadejte název virtuálního počítače, který má být vytvořen (*DNS01* v scénáři), účet místního správce a heslo.
   
    ![Vytvoření virtuálního počítače na webu Azure Portal](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Vyberte **Volitelná konfigurační** > **síť** > **Virtuální síť**a pak vyberte **TestVNet**. Pokud **TestVNet** není k dispozici, ujistěte se, že používáte *umístění centrální USA* a vytvořili testovací prostředí popsané na začátku tohoto článku.
   
    ![Vytvoření virtuálního počítače na webu Azure Portal](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. V části **Síť**zkontrolujte, zda je aktuálně vybraná podsíť *FrontEnd*, vyberte v části **Přiřazení IP adres** **položku** **Statické**a zadejte *hodnotu 192.168.1.101* pro **adresu IP,** jak je uvedeno níže.
   
    ![Vytvoření virtuálního počítače na webu Azure Portal](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. V části **IP adresy**vyberte **OK** , vyberte **OK** v části **Síť**a pak v části **Volitelná konfigurace**vyberte **OK** .
7. V části **Vytvořit virtuální hod**vyberte **Vytvořit**. Všimněte si níže uvedené dlaždice zobrazené na řídicím panelu:
   
    ![Vytvoření virtuálního počítače na webu Azure Portal](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Jak načíst statické informace o privátní IP adrese pro virtuální hod
Chcete-li zobrazit informace o statické privátní IP adresy pro virtuální ho virtuálního počítačů vytvořeného pomocí výše uvedených kroků, proveďte následující kroky.

1. Na webu Azure Portal vyberte **PROCHÁZET VŠECHNY** > **VIRTUÁLNÍ POČÍTAČE (klasické)** > **DNS01** > **Všechna nastavení** > IP**adresy** a všimněte si přiřazení IP adresy a IP adresy, jak je vidět níže.
   
    ![Vytvoření virtuálního počítače na webu Azure Portal](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Jak odebrat statickou privátní IP adresu z virtuálního počítačů

V části **IP adresy**vyberte **Dynamická** vpravo od **přiřazení IP adresy**, vyberte **Uložit**a pak vyberte **Ano**, jak je znázorněno na následujícím obrázku:
   
    ![Create VM in Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Jak přidat statickou privátní IP adresu do existujícího virtuálního počítačů

1. V části IP adresy , **zobrazené**dříve, vyberte **Statický** vpravo od **přiřazení IP adresy**.
2. Zadejte *192.168.1.101* pro **adresu IP**, vyberte **uložit**a pak vyberte **Ano**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Nastavení ADRES IP v operačním systému

Doporučujeme, abyste staticky nepřiřazovali privátní IP adresu přiřazenou virtuálnímu počítači Azure v rámci operačního systému virtuálního počítače, pokud to není nutné. Pokud ručně nastavíte privátní IP adresu v rámci operačního systému, ujistěte se, že se jedná o stejnou adresu jako privátní IP adresa přiřazená virtuálnímu počítači Azure, nebo můžete ztratit připojení k virtuálnímu počítači. Nikdy byste neměli ručně přiřadit veřejnou IP adresu přiřazenou virtuálnímu počítači Azure v rámci operačního systému virtuálního počítače.

## <a name="next-steps"></a>Další kroky
* Informace o [vyhrazených veřejných IP](virtual-networks-reserved-public-ip.md) adresách.
* Další informace o [veřejných adresách IP (ILPIP) na úrovni instance.](virtual-networks-instance-level-public-ip.md)
* Prohlédněte si [rezervovaná práva API IP REST](https://msdn.microsoft.com/library/azure/dn722420.aspx).

