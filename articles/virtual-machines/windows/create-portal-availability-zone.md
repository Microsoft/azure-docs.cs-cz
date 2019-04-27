---
title: Vytvoření zóny virtuálního počítače Windows pomocí webu Azure portal | Dokumentace Microsoftu
description: Vytvoření virtuálního počítače s Windows v zóně dostupnosti s využitím webu Azure portal
services: virtual-machines-windows
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/27/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: e813b26a91d25fbaa1298acd455f27d2cac705f6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61403631"
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>Vytvoření virtuálního počítače s Windows v zóně dostupnosti s využitím webu Azure portal

Tento článek obsahuje kroky k vytvoření virtuálního počítače v zóně dostupnosti Azure pomocí webu Azure portal. [Zóna dostupnosti](../../availability-zones/az-overview.md) je fyzicky oddělená zóna v oblasti Azure. Zóny dostupnosti se používají k ochraně aplikací a dat před málo pravděpodobným selháním nebo ztrátou celého datového centra.

Pokud chcete využít zóny dostupnosti, vytvořte virtuální počítač v [podporované oblasti Azure](../../availability-zones/az-overview.md#services-support-by-region).

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure 

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače

1. Klikněte na **Vytvořit prostředek** v levém horním rohu webu Azure Portal.

2. Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**. 

3. Zadejte informace o virtuálním počítači. Uživatelské jméno a heslo, které tady zadáte, se používá k přihlášení k virtuálnímu počítači. Heslo musí obsahovat nejméně 12 znaků a musí splňovat [zadané požadavky na složitost](faq.md#what-are-the-password-requirements-when-creating-a-vm). Vyberte umístění, jako je například USA – východ 2, která podporují zóny dostupnosti. Jakmile budete hotovi, klikněte na **OK**.

    ![Zadání základních informací o virtuálním počítači v okně portálu](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Zvolte velikost virtuálního počítače. Vyberte doporučená velikost nebo filtr na základě funkcí. Potvrďte, že velikost je k dispozici v zóně, ve které chcete použít.

    ![Vyberte velikost virtuálního počítače](./media/create-portal-availability-zone/create-windows-vm-portal-sizes.png)  

5. V části **nastavení** > **vysoké dostupnosti**, vyberte jednu z číslované zóny z **zóna dostupnosti** rozevírací seznam, ponechejte zbývající výchozí hodnoty, a Klikněte na tlačítko **OK**.

    ![Vyberte zónu dostupnosti](./media/create-portal-availability-zone/create-windows-vm-portal-availability-zone.png)

6. Na stránce Souhrn klikněte na tlačítko **vytvořit** spusťte nasazení virtuálního počítače.

7. Virtuální počítač se připne na řídicí panel webu Azure Portal. Po dokončení nasazení se automaticky otevře souhrn virtuálního počítače.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Potvrzení zóny pro spravovaný disk a IP adresa

Po nasazení virtuálního počítače v zóně dostupnosti spravovaného disku pro virtuální počítač se vytvoří ve stejné zóně dostupnosti. Ve výchozím nastavení je vytvořen v této oblasti také veřejnou IP adresu.

Můžete potvrdit nastavení zón pro tyto prostředky na portálu.  

1. Klikněte na tlačítko **skupiny prostředků** a potom název prostředku skupiny pro virtuální počítač, jako například *myResourceGroup*.

2. Klikněte na název prostředku disku. **Přehled** stránka obsahuje podrobné informace o zóně umístění a dostupnost prostředku.

    ![Zóny dostupnosti pro spravovaný disk](./media/create-portal-availability-zone/create-windows-vm-portal-disk.png)

3. Klikněte na název prostředku veřejné IP adresy. **Přehled** stránka obsahuje podrobné informace o zóně umístění a dostupnost prostředku.

    ![Zóna dostupnosti IP adresy](./media/create-portal-availability-zone/create-windows-vm-portal-ip.png)



## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak vytvořit virtuální počítač v zóně dostupnosti. Přečtěte si další informace o [oblastech a dostupnosti](regions-and-availability.md) pro virtuální počítače Azure.
