---
title: Připojit datový disk do virtuálního počítače s Linuxem | Microsoft Docs
description: Připojit nové nebo stávající datový disk do virtuálního počítače s Linuxem pomocí portálu.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: cynthn
ms.openlocfilehash: 4acfe53d68db3192c1f6c3c9e5f91b55bd5df7b8
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
ms.locfileid: "30832496"
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Připojit datový disk do virtuálního počítače s Linuxem pomocí portálu 
Tento článek ukazuje, jak připojit nové i stávající disky pro virtuální počítač s Linuxem pomocí portálu Azure. Můžete také [připojit datový disk k virtuálnímu počítači Windows na portálu Azure](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Než připojíte k virtuálnímu počítači disky, projděte si tyto tipy:

* Velikost virtuálního počítače určuje, kolik datových disků můžete připojit. Podrobnosti najdete v tématu [velikosti virtuálních počítačů](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Chcete-li používat úložiště úrovně Premium, je třeba DS-series nebo GS-series virtuálního počítače. Pomocí těchto virtuálních počítačů můžete použít Premium i standardní disky. Storage úrovně Premium je k dispozici v určité oblasti. Podrobnosti najdete v tématu [úložiště Premium: vysoce výkonné úložiště pro úlohy virtuálních počítačů Azure](../windows/premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Disky připojené k virtuální počítače jsou ve skutečnosti soubory VHD uložených v Azure. Podrobnosti najdete v tématu [o disky a virtuální pevné disky pro virtuální počítače](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="find-the-virtual-machine"></a>Najít virtuální počítač
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. V nabídce vlevo klikněte na tlačítko **virtuální počítače**.
3. Ze seznamu vyberte virtuální počítač.
4. Na virtuální počítače stránky, v **Essentials**, klikněte na tlačítko **disky**.
   
    ![Otevřete nastavení disku](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>Připojit nový disk

1. Na **disky** podokně klikněte na tlačítko **+ přidat datový disk**.
2. Klikněte na rozevírací nabídku pro **název** a vyberte **vytvořit disk**:

    ![Vytvoření Azure spravovaných disků](./media/attach-disk-portal/create-new-md.png)

3. Zadejte název pro spravované disk. Zkontrolujte výchozí nastavení, aktualizovat podle potřeby a pak klikněte na tlačítko **vytvořit**.
   
   ![Zkontrolujte nastavení disku](./media/attach-disk-portal/create-new-md-settings.png)

4. Klikněte na tlačítko **Uložit** k vytvoření spravovaného disku a aktualizaci konfigurace virtuálního počítače:

   ![Uložit nový Disk spravované Azure](./media/attach-disk-portal/confirm-create-new-md.png)

5. Jakmile Azure vytvoří disk a připojí jej k virtuálnímu počítači, na nový disk, je uvedena ve nastavení disku virtuálního počítače v části **datových disků**. Nejvyšší úrovně prostředků jsou spravovaných disků disk se zobrazí v kořenovém adresáři skupiny prostředků:

   ![Disk Azure spravované ve skupině prostředků](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>Připojení stávajícího disku
1. Na **disky** podokně klikněte na tlačítko **+ přidat datový disk**.
2. Klikněte na rozevírací nabídku pro **název** zobrazíte seznam existující spravovaných disků, které jsou přístupné pro vaše předplatné Azure. Vyberte spravované disk připojit:

   ![Připojit stávající Disk spravované Azure](./media/attach-disk-portal/select-existing-md.png)

3. Klikněte na tlačítko **Uložit** připojit stávající disk spravované a aktualizaci konfigurace virtuálního počítače:
   
   ![Uložte aktualizace disku spravované Azure](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. Po Azure připojí disk k virtuálnímu počítači, je uvedena v nastavení disku virtuálního počítače v části **datových disků**.



## <a name="next-steps"></a>Další postup
Můžete také [připojit datový disk](add-disk.md) pomocí rozhraní příkazového řádku Azure.
