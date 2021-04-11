---
title: Správa disků virtuálních počítačů v grafickém procesoru Azure Stack Edge pro R, Mini R prostřednictvím Azure Portal
description: Přečtěte si, jak spravovat disky, včetně přidání nebo odpojení datového disku na virtuálních počítačích, které jsou nasazené v grafickém procesoru Azure Stack Edge pro, Azure Stack Edge pro R a Azure Stackch Mini R prostřednictvím Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to manage disks on a VM running on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: dff3f4bafdb35d89e8f1792c1aca68824a9bc685
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080201"
---
# <a name="use-the-azure-portal-to-manage-disks-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Pomocí Azure Portal můžete spravovat disky na virtuálních počítačích na procesorovém GPU Azure Stack Edge pro.

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Disky na virtuálních počítačích nasazených na zařízeních Azure Stack Edge pro můžete zřídit pomocí Azure Portal. Disky se zřídí na zařízení přes místní Azure Resource Manager a využívají kapacitu zařízení. Operace, jako je přidání disku a odpojení disku, se dají provádět prostřednictvím Azure Portal, která zase volá místní Azure Resource Manager k zajištění úložiště. 

Tento článek vysvětluje, jak přidat datový disk do existujícího virtuálního počítače, odpojit datový disk a nakonec změnit velikost samotného virtuálního počítače prostřednictvím Azure Portal. 

        
## <a name="about-disks-on-vms"></a>O discích na virtuálních počítačích

Váš virtuální počítač může mít disk s operačním systémem a datový disk. Každý virtuální počítač nasazený v zařízení má jeden připojený disk s operačním systémem. Tento disk s operačním systémem má předem instalovaný operační systém, který byl vybrán při vytvoření virtuálního počítače. Tento disk obsahuje spouštěcí svazek.

> [!NOTE]
> U virtuálního počítače na vašem zařízení nemůžete změnit velikost disku operačního systému. Velikost disku s operačním systémem se určuje podle velikosti virtuálního počítače, kterou jste vybrali. 


Datový disk na druhé straně je spravovaný disk připojený k VIRTUÁLNÍmu počítači běžícímu na vašem zařízení. Datový disk se používá k ukládání dat aplikace. Datovými disky jsou obvykle jednotky SCSI. Velikost virtuálního počítače určuje, kolik datových disků můžete připojit k virtuálnímu počítači. Ve výchozím nastavení se k hostování disků používá Premium Storage.

Virtuální počítač nasazený v zařízení může někdy obsahovat dočasný disk. Dočasný disk poskytuje krátkodobé úložiště pro aplikace a procesy a je určen pouze k ukládání dat, jako jsou například stránky nebo soubory odkládacích souborů. Data na dočasném disku mohou být ztracena během události údržby nebo při opětovném nasazení virtuálního počítače. Během úspěšného standardního restartování virtuálního počítače se uchovávají data na dočasném disku. 


## <a name="prerequisites"></a>Požadavky

Než začnete spravovat disky na virtuálních počítačích, které běží na vašem zařízení, prostřednictvím Azure Portal, ujistěte se, že:


1. Máte přístup k aktivovanému zařízení GPU Azure Stack Edge pro. Také jste povolili síťové rozhraní pro výpočty na zařízení. Tato akce vytvoří na VIRTUÁLNÍm počítači virtuální přepínač na tomto síťovém rozhraní. 
    1. V místním uživatelském rozhraní zařízení, pokračujte na **COMPUTE**. Vyberte síťové rozhraní, které budete používat k vytvoření virtuálního přepínače.

        > [!IMPORTANT] 
        > Pro výpočetní výkon můžete nakonfigurovat jenom jeden port.

    1. Povolte výpočetní prostředky v síťovém rozhraní. Grafický procesor Azure Stack Edge pro vytváří a spravuje virtuální přepínač odpovídající tomuto síťovému rozhraní.

1. V zařízení máte nasazený aspoň jeden virtuální počítač. Pokud chcete vytvořit tento virtuální počítač, přečtěte si pokyny v tématu [nasazení virtuálního počítače na Azure Stack Edge pro prostřednictvím Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).



## <a name="add-a-data-disk"></a>Přidat datový disk

Postupujte podle těchto kroků a přidejte disk do virtuálního počítače nasazeného v zařízení. 

1. Přejít do virtuálního počítače, do kterého chcete přidat datový disk, a pak přejít na stránku **Přehled** . Vyberte **Disky**.
    
    ![Výběr disků na stránce s přehledem](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-1.png)

1. V okně **disky** v části **datové disky** vyberte **vytvořit a připojit nový disk**.

    ![Vytvoření a připojení nového disku](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-2.png)

1. V okně **vytvořit nový disk** zadejte následující parametry:

    
    |Pole  |Popis  |
    |---------|---------|
    |Název     | Jedinečný název v rámci skupiny prostředků. Po vytvoření datového disku nelze tento název změnit.     |
    |Velikost| Velikost datového disku v GiB. Maximální velikost datového disku se určuje podle velikosti virtuálního počítače, kterou jste vybrali. Při zřizování disku byste měli také vzít v úvahu skutečné místo na zařízení a další úlohy virtuálních počítačů, které provoz využívají.  |         

    ![Vytvořit nový disk okno](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-3.png)

    Vyberte **OK** a pokračujte.

1. Na stránce **Přehled** v části **disky** se zobrazí položka odpovídající novému disku. Přijměte výchozí hodnotu nebo přiřaďte disku platnou logickou jednotku (LUN) a vyberte **Uložit**. Logická jednotka (LUN) je jedinečný identifikátor pro disk SCSI. Další informace najdete v tématu [co je logická jednotka (LUN)?](../virtual-machines/linux/azure-to-guest-disk-mapping.md#what-is-a-lun).

    ![Nový disk na stránce s přehledem](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-4.png)

1. Zobrazí se oznámení, že probíhá vytváření disku. Po úspěšném vytvoření disku se virtuální počítač aktualizuje. 

    ![Oznámení pro vytvoření disku](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-5.png)

1. Přejděte zpět na stránku **Přehled** . Seznam disků se aktualizuje a zobrazí se nově vytvořený datový disk.

    ![Aktualizovaný seznam datových disků](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-6.png)


## <a name="change-a-data-disk"></a>Změna datového disku

Pomocí těchto kroků můžete změnit disk přidružený k virtuálnímu počítači nasazenému na vašem zařízení.

1. Přejděte na virtuální počítač s datovým diskem, který chcete změnit, a přejděte na stránku **Přehled** . Vyberte **Disky**.

1. V seznamu datových disků vyberte disk, který chcete změnit. V pravém rohu vybraného disku vyberte ikonu pro úpravy (tužka).  

    ![Vyberte disk, který chcete změnit.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-1.png)

1. V okně **změnit disk** můžete změnit pouze velikost disku. Název přidružený k disku nelze po jeho vytvoření změnit. Změňte **Velikost** a uložte změny.

    ![Změnit velikost datového disku](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-2.png)

    > [!NOTE]
    > Můžete rozšířit jenom datový disk, disk nemůžete zmenšit.

1. Na stránce **Přehled** se seznam disků aktualizuje, aby se zobrazil aktualizovaný disk.


## <a name="attach-an-existing-disk"></a>Připojení stávajícího disku

Pomocí těchto kroků připojte existující disk k virtuálnímu počítači nasazenému na vašem zařízení.

1. Přejít na virtuální počítač, ke kterému chcete připojit stávající disk, a pak přejít na stránku **Přehled** . Vyberte **Disky**.
    
    ![Vybrat disky ](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-1.png)

1. V okně **disky** v části **datové disky** vyberte **připojit existující disk**.

    ![Vyberte připojit existující disk.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-1.png)

1. Přijměte výchozí logickou jednotku nebo přiřaďte platnou logickou jednotku. Z rozevíracího seznamu vyberte existující datový disk. Vyberte Uložit.

    ![Vybrat existující disk](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-2.png)

    Vyberte **Uložit** a pokračovat.

1. Zobrazí se oznámení o tom, že je virtuální počítač aktualizovaný. Po aktualizaci virtuálního počítače přejděte zpátky na stránku **Přehled** . Aktualizujte stránku pro zobrazení nově připojeného disku v seznamu datových disků.

    ![Zobrazit aktualizovaný seznam datových disků na stránce s přehledem](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="detach-a-data-disk"></a>Odpojení datového disku

Pomocí těchto kroků můžete odpojit nebo odebrat datový disk přidružený k virtuálnímu počítači nasazenému v zařízení.

> [!NOTE]
> - Datový disk můžete odebrat, když je virtuální počítač spuštěný. Před odpojením z virtuálního počítače se ujistěte, že tento disk aktivně nepoužíváte.
> - Pokud disk odpojíte, automaticky se neodstraní.

1. Přejít na virtuální počítač, ze kterého chcete odpojit datový disk a přejít na stránku **Přehled** . Vyberte **Disky**.

    ![Vybrat disky](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-1.png)

1. V seznamu disků vyberte disk, který chcete odpojit. V pravém rohu vybraného disku vyberte ikonu odpojit (příčně). Vybraná položka bude odpojena. Vyberte **Uložit**. 

    ![Vyberte disk, který se má odpojit.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/detach-data-disk-1.png)

1. Po odpojení disku se virtuální počítač aktualizuje. Aktualizujte stránku **Přehled** a zobrazte aktualizovaný seznam datových disků.

    ![Vybrat Uložit](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="next-steps"></a>Další kroky

Informace o tom, jak nasadit virtuální počítače na zařízení Azure Stack Edge pro, najdete v tématu [nasazení virtuálních počítačů prostřednictvím Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
