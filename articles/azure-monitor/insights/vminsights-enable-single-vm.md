---
title: Povolení Azure Monitoru pro virtuální počítače na webu Azure Portal
description: Zjistěte, jak vyhodnotit Azure Monitor pro virtuální počítače na jednom virtuálním počítači Azure nebo na škálovací sadě virtuálních strojů.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 45bc8f16a547d4a95820f9dcd02132844b3be83c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480704"
---
# <a name="enable-azure-monitor-for-vms-in-the-azure-portal"></a>Povolení Azure Monitoru pro virtuální počítače na webu Azure Portal

Tento článek popisuje, jak povolit Azure Monitor pro virtuální počítače na malém počtu virtuálních počítačů Azure (VM) pomocí portálu Azure. Vaším cílem je sledovat vaše virtuální počítače a zjistit problémy s výkonem nebo dostupností. 

Než začnete, zkontrolujte [požadavky](vminsights-enable-overview.md) a ujistěte se, že vaše předplatné a prostředky splňují požadavky.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Povolení monitorování pro jeden virtuální počítač Azure
Povolení monitorování virtuálního počítače Azure:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Vyberte **virtuální počítače**.

1. V seznamu vyberte virtuální počítač.

1. Na stránce Virtuální počítač v části **Monitorování** vyberte **Přehledy** a pak **Povolit**.

    ![Povolení Azure Monitoru pro virtuální počítače pro virtuální počítač](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Na stránce **Registrace přehledů monitorování Azure,** pokud máte existující pracovní prostor Log Analytics ve stejném předplatném, vyberte ho v rozevíracím seznamu.  

    Seznam předem vybere výchozí pracovní prostor a umístění, kde se virtuální jazyk nasadí v předplatném. 

    >[!NOTE]
    >Pokud chcete vytvořit nový pracovní prostor Analýzy protokolů pro ukládání dat monitorování z virtuálního aplikace, přečtěte [si tématu Vytvoření pracovního prostoru Analýzy protokolů](../../azure-monitor/learn/quick-create-workspace.md). Pracovní prostor Log Analytics musí patřit do jedné z [podporovaných oblastí](vminsights-enable-overview.md#log-analytics).

6. Při provádění konfigurace obdržíte stavové zprávy.

    ![Povolení zpracování nasazení monitorování Azure Monitor pro virtuální počítače](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Povolení monitorování pro váhovou sadu jednoho virtuálního počítače

Povolení monitorování škálovací sady virtuálních strojů Azure:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

2. Vyberte **škálovací sady virtuálních strojů**.

3. Ze seznamu vyberte škálovací sadu virtuálních strojů.

4. Na stránce škálovací sady virtuálního počítače včásti **Monitorování** vyberte **Přehledy** a pak **Povolit**.

5. Pokud chcete na stránce **Přehledy** použít existující pracovní prostor Analýzy protokolů, vyberte ho v rozevíracím seznamu.

    Seznam předem vybere výchozí pracovní prostor a umístění, které virtuální jazyk se nasadí do v předplatném. 

    ![Povolení Azure Monitoru pro virtuální počítače pro škálovací sadu virtuálních strojů](media/vminsights-enable-single-vm/enable-vminsights-vmss-portal.png)

    >[!NOTE]
    >Pokud chcete vytvořit nový pracovní prostor Log Analytics pro ukládání dat monitorování z škálovací sady virtuálních strojů, přečtěte si informace [o vytvoření pracovního prostoru Analýzy protokolů](../learn/quick-create-workspace.md). Pracovní prostor Log Analytics musí patřit do jedné z [podporovaných oblastí](vminsights-enable-overview.md#log-analytics).

6. Při provádění konfigurace obdržíte stavové zprávy.

    >[!NOTE]
    >Pokud pro škálovací sadu použijete model ručního upgradu, upgradujte instance k dokončení instalace. Upgrady můžete spustit ze stránky **Instance** v části **Nastavení.**
    
    ![Povolení zpracování nasazení monitorování Azure Monitor pro virtuální počítače](media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status.png)

Teď, když jste povolili monitorování pro škálovací sadu virtuálních počítačů nebo virtuálních strojů, jsou informace o monitorování dostupné pro analýzu ve Službě Azure Monitor pro virtuální počítače. 

## <a name="next-steps"></a>Další kroky

* Informace o zjištěných závislostech aplikací najdete [v tématu Použití programu Azure Monitor for VMS Map](vminsights-maps.md). 
* Informace o problémových místech, celkovém využití a výkonu virtuálního počítače najdete v [tématu Zobrazení výkonu virtuálních počítačích Azure](vminsights-performance.md).
