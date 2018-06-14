---
title: Vytvoření zoned virtuálního počítače s Windows pomocí portálu Azure | Microsoft Docs
description: Vytvoření virtuálního počítače s Windows v zóně dostupnosti pomocí portálu Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 3d3561cf1ad760930821fabeef9839c25d55f2a9
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
ms.locfileid: "30321984"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>Vytvoření virtuálního počítače s Windows v zóně dostupnosti pomocí portálu Azure

Tento článek obsahuje kroky prostřednictvím portálu Azure k vytvoření virtuálního počítače v zóně Azure dostupnosti. [Zóna dostupnosti](../../availability-zones/az-overview.md) je fyzicky oddělená zóna v oblasti Azure. Zóny dostupnosti se používají k ochraně aplikací a dat před málo pravděpodobným selháním nebo ztrátou celého datového centra.

Pokud chcete použít dostupnosti zóny, vytvoření virtuálního počítače v prostředí [podporované oblasti Azure](../../availability-zones/az-overview.md#regions-that-support-availability-zones).

## <a name="log-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.

2. Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**. 

3. Zadejte informace o virtuálním počítači. Uživatelské jméno a heslo, které tady zadáte, se používá k přihlášení k virtuálnímu počítači. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](faq.md#what-are-the-password-requirements-when-creating-a-vm). Vyberte umístění, jako je například Východ USA 2, který podporuje dostupnost zóny. Jakmile budete hotovi, klikněte na **OK**.

    ![Zadání základních informací o virtuálním počítači v okně portálu](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Zvolte velikost virtuálního počítače. Vyberte doporučená velikost nebo filtr na základě funkcí. Potvrďte, že velikost je k dispozici v zóně, kterou chcete použít.

    ![Vyberte velikost virtuálního počítače](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. V části **nastavení** > **vysokou dostupnost**, vyberte jednu z číslem zóny z **dostupnost zóny** rozevíracího seznamu, ponechejte zbývající výchozí hodnoty, a Klikněte na tlačítko **OK**.

    ![Vyberte dostupnosti zóny](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. Na stránce Souhrn klikněte na tlačítko **vytvořit** ke spuštění nasazení virtuálního počítače.

7. Virtuální počítač se připne na řídicí panel webu Azure Portal. Po dokončení nasazení se automaticky otevře souhrn virtuálního počítače.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Potvrďte zónu pro spravovaných disků a IP adresy

Po nasazení virtuálního počítače v zóně dostupnosti pro virtuální počítač se spravovaným diskem se vytvoří ve stejné zóně dostupnosti. Ve výchozím nastavení je veřejnou IP adresu vytvořen také v této zóně.

Můžete potvrdit nastavení zón pro tyto prostředky na portálu.  

1. Klikněte na tlačítko **skupiny prostředků** a potom název prostředku skupiny pro virtuální počítač, jako například *myResourceGroup*.

2. Klikněte na název prostředek disku. **Přehled** stránky obsahuje podrobnosti o umístění a dostupnost zóny prostředku.

    ![Dostupnost zóny pro spravované disk](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. Klikněte na název prostředek veřejné IP adresy. **Přehled** stránky obsahuje podrobnosti o umístění a dostupnost zóny prostředku.

    ![Dostupnost zóny pro IP adresu](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>Další postup

V tomto článku jste se naučili vytvoření virtuálního počítače v zóně dostupnosti. Přečtěte si další informace o [oblastech a dostupnosti](regions-and-availability.md) pro virtuální počítače Azure.
