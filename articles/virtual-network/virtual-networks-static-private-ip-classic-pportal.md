---
title: Konfigurace privátních IP adres pro virtuální počítače (Classic) – Azure Portal | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat privátní IP adresy pro virtuální počítače (Classic) pomocí Azure Portal.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6986f6f16cbd32d44223bba4f4be4577fa11258c
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222900"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Konfigurace privátních IP adres pro virtuální počítač (Classic) pomocí Azure Portal

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Tento článek se týká modelu nasazení Classic. [V modelu nasazení Správce prostředků můžete také spravovat statickou privátní IP adresu](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Následující ukázkové kroky očekávají, že už je vytvořené jednoduché prostředí. Chcete-li spustit postup, který je zobrazen v tomto dokumentu, nejdříve Sestavte testovací prostředí popsané v tématu [vytvoření virtuální](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal)sítě.

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Jak zadat statickou privátní IP adresu při vytváření virtuálního počítače
Pokud chcete vytvořit virtuální počítač s názvem *DNS01* v podsíti *front-endu* virtuální sítě s názvem *TestVNet* se statickou privátní IP adresou *192.168.1.101*, proveďte následující kroky:

1. V prohlížeči přejděte na https://portal.azure.com a v případě potřeby se přihlaste pomocí účtu Azure.
2. Vyberte **Nový**  >  **COMPUTE**  >  **Windows Server 2012 R2 Datacenter**. Všimněte si, že seznam **Vyberte model nasazení** již zobrazuje **klasický** a pak vyberte **vytvořit**.
   
    ![Snímek obrazovky zobrazující Azure Portal s novým > COMPUTE > Windows Server 2012 R2 Datacenter se zvýrazní.](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. V části **vytvořit virtuální počítač** zadejte název virtuálního počítače, který se má vytvořit (*DNS01* ve scénáři), účet místního správce a heslo.
   
    ![Snímek obrazovky, který ukazuje, jak vytvořit virtuální počítač zadáním názvu virtuálního počítače, uživatelského jména a hesla místního správce.](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Vyberte **volitelná**  >  **Síťová síť**  >  **Virtual Network** a pak vyberte **TestVNet**. Pokud **TestVNet** není k dispozici, ujistěte se, že používáte umístění *střed USA* a vytvořili jste testovací prostředí popsané na začátku tohoto článku.
   
    ![Snímek obrazovky, který zobrazuje volitelnou konfiguraci > > sítě Virtual Network > možnost TestVNet se zvýrazní.](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. V části **síť** se ujistěte, že je aktuálně vybraná podsíť *front-end*, a pak vyberte **IP adresy**, v části **přiřazení IP adresy** vyberte **static** a pak zadejte *192.168.1.101* pro **IP adresu** , jak je vidět níže.
   
    ![Snímek obrazovky, který zvýrazní pole adresy IP, kam zadáte statickou IP adresu.](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. V části **IP adresy** vyberte **OK** , v části **síť** vyberte **OK** a potom v části **volitelná konfigurace** vyberte **OK** .
7. V části **vytvořit virtuální počítač** vyberte **vytvořit**. Všimněte si následující dlaždice zobrazené na řídicím panelu:
   
    ![Snímek obrazovky zobrazující dlaždici Windows Server 2012 R2 Datacenter](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Jak načíst informace o statických privátních IP adresách pro virtuální počítač
Pokud chcete zobrazit informace o statických privátních IP adresách pro virtuální počítač vytvořený pomocí kroků uvedených výše, proveďte následující kroky.

1. V Azure Portal vyberte **Procházet všechny**  >  **virtuální počítače (Classic)**  >  **DNS01**  >  **všechna nastavení**  >  **IP adresy** a Všimněte si přiřazení IP adresy a IP adresy, jak vidíte níže.
   
    ![Vytvořit virtuální počítač v Azure Portal](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Jak odebrat statickou privátní IP adresu z virtuálního počítače

V části **IP adresy** vyberte **dynamické** napravo od **přiřazení IP adresy**, vyberte **Uložit** a pak vyberte **Ano**, jak je znázorněno na následujícím obrázku:
   
![Snímek obrazovky, který ukazuje, jak odebrat statickou privátní IP adresu z virtuálního počítače tak, že vyberete možnost dynamický napravo od popisku přiřazení IP adresy.](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Postup přidání statické privátní IP adresy do existujícího virtuálního počítače

1. V části **IP adresy**, které jste předtím zobrazili, vyberte možnost **staticky** u **přiřazení IP adresy**.
2. Zadejte *192.168.1.101* pro **IP adresu**, vyberte **Uložit** a pak vyberte **Ano**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Nastavení IP adres v operačním systému

V případě potřeby doporučujeme, abyste privátní IP adresu přiřazenou k virtuálnímu počítači Azure nepřiřadili staticky v operačním systému virtuálního počítače. Pokud ručně nastavíte privátní IP adresu v operačním systému, ujistěte se, že se jedná o stejnou adresu jako soukromá IP adresa přiřazená k virtuálnímu počítači Azure, nebo můžete ztratit připojení k virtuálnímu počítači. Nikdy byste neměli ručně přiřadit veřejnou IP adresu přiřazenou k virtuálnímu počítači Azure v operačním systému virtuálního počítače.

## <a name="next-steps"></a>Další kroky
* Seznamte se s [rezervovanými veřejnými IP](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) adresami.
* Přečtěte si informace o [veřejných IP adresách na úrovni instance (ILPIP)](/previous-versions/azure/virtual-network/virtual-networks-instance-level-public-ip) .
* Projděte si [vyhrazená IP adresa rozhraní REST API](/previous-versions/azure/reference/dn722420(v=azure.100)).