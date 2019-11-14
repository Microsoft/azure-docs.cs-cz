---
title: Vytvoření virtuálního počítače s Windows v zóně pomocí Azure Portal
description: Vytvoření virtuálního počítače s Windows v zóně dostupnosti s Azure Portal
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: c6f843693ccfa16f9b31027ba370242e0462b138
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74033897"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>Vytvoření virtuálního počítače s Windows v zóně dostupnosti s Azure Portal

Tento článek popisuje použití Azure Portal k vytvoření virtuálního počítače v zóně dostupnosti Azure. [Zóna dostupnosti](../../availability-zones/az-overview.md) je fyzicky oddělená zóna v oblasti Azure. Zóny dostupnosti se používají k ochraně aplikací a dat před málo pravděpodobným selháním nebo ztrátou celého datového centra.

Pokud chcete využít zóny dostupnosti, vytvořte virtuální počítač v [podporované oblasti Azure](../../availability-zones/az-overview.md#services-support-by-region).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.

2. Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**. 

3. Zadejte informace o virtuálním počítači. Uživatelské jméno a heslo, které tady zadáte, se používá k přihlášení k virtuálnímu počítači. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](faq.md#what-are-the-password-requirements-when-creating-a-vm). Vyberte umístění, jako je Východní USA 2, které podporuje zóny dostupnosti. Jakmile budete hotovi, klikněte na **OK**.

    ![Zadání základních informací o virtuálním počítači v okně portálu](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Zvolte velikost virtuálního počítače. Vyberte doporučenou velikost nebo filtrujte na základě funkcí. Potvrďte, že velikost je k dispozici v zóně, kterou chcete použít.

    ![Vyberte velikost virtuálního počítače.](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. V části **nastavení** > **Vysoká dostupnost**vyberte jednu z očíslovaných zón v rozevíracím seznamu **zóna dostupnosti** , zbývající výchozí hodnoty a klikněte na **OK**.

    ![Vyberte zónu dostupnosti.](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. Na stránce Souhrn kliknutím na tlačítko **vytvořit** spusťte nasazení virtuálního počítače.

7. Virtuální počítač se připne na řídicí panel webu Azure Portal. Po dokončení nasazení se automaticky otevře souhrn virtuálního počítače.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Potvrďte zónu pro spravovaný disk a IP adresu.

Když je virtuální počítač nasazený v zóně dostupnosti, vytvoří se spravovaný disk pro virtuální počítač ve stejné zóně dostupnosti. Ve výchozím nastavení se v této zóně vytvoří také veřejná IP adresa.

Nastavení zóny pro tyto prostředky můžete potvrdit na portálu.  

1. Klikněte na **skupiny prostředků** a pak na název skupiny prostředků pro virtuální počítač, například *myResourceGroup*.

2. Klikněte na název prostředku disku. Stránka **Přehled** obsahuje podrobnosti o umístění a zóně dostupnosti daného prostředku.

    ![Zóna dostupnosti pro spravovaný disk](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. Klikněte na název prostředku veřejné IP adresy. Stránka **Přehled** obsahuje podrobnosti o umístění a zóně dostupnosti daného prostředku.

    ![Zóna dostupnosti pro IP adresu](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak vytvořit virtuální počítač v zóně dostupnosti. Přečtěte si další informace o [dostupnosti](availability.md) pro virtuální počítače Azure.
