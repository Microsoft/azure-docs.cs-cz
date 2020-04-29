---
title: Povolit Azure Monitor pro virtuální počítače v Azure Portal
description: Přečtěte si, jak vyhodnotit Azure Monitor pro virtuální počítače na jednom virtuálním počítači Azure nebo v sadě škálování virtuálního počítače.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 45bc8f16a547d4a95820f9dcd02132844b3be83c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79480704"
---
# <a name="enable-azure-monitor-for-vms-in-the-azure-portal"></a>Povolit Azure Monitor pro virtuální počítače v Azure Portal

Tento článek popisuje, jak povolit Azure Monitor pro virtuální počítače na malém počtu virtuálních počítačů Azure pomocí Azure Portal. Vaším cílem je monitorovat virtuální počítače a zjišťovat případné problémy s výkonem nebo dostupností. 

Než začnete, zkontrolujte [požadavky](vminsights-enable-overview.md) a ujistěte se, že vaše předplatné a prostředky splňují požadavky.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Povolení monitorování pro jeden virtuální počítač Azure
Pokud chcete povolit monitorování virtuálního počítače Azure:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Vyberte **Virtual Machines**.

1. V seznamu vyberte virtuální počítač.

1. Na stránce virtuální počítač v části **monitorování** vyberte **přehledy** a pak **Povolit**.

    ![Povolení Azure Monitor pro virtuální počítače pro virtuální počítač](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Pokud máte v rámci stejného předplatného pracovní prostor Log Analytics, v rozevíracím seznamu vyberte na stránce pro **registraci Azure monitor Insights** .  

    Seznam předchází výchozí pracovní prostor a umístění, kde je virtuální počítač nasazený v předplatném. 

    >[!NOTE]
    >Informace o vytvoření nového pracovního prostoru Log Analytics pro uložení dat monitorování z virtuálního počítače najdete v tématu [Vytvoření pracovního prostoru Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Váš pracovní prostor Log Analytics musí patřit do jedné z [podporovaných oblastí](vminsights-enable-overview.md#log-analytics).

6. Po provedení konfigurace se zobrazí stavové zprávy.

    ![Povolení zpracování nasazení Azure Monitor pro virtuální počítače monitorování](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Povolit monitorování pro jednu sadu škálování virtuálního počítače

Pokud chcete povolit monitorování sady škálování virtuálních počítačů Azure:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyberte **Virtual Machine Scale Sets**.

3. V seznamu vyberte sadu škálování virtuálního počítače.

4. Na stránce sada škálování virtuálního počítače v části **monitorování** vyberte **přehledy** a pak **Povolit**.

5. Pokud chcete použít existující Log Analytics pracovní prostor, na stránce **přehledy** vyberte v rozevíracím seznamu.

    Seznam předchází výchozí pracovní prostor a umístění, do kterého se virtuální počítač nasadí v rámci předplatného. 

    ![Povolení Azure Monitor pro virtuální počítače pro sadu škálování virtuálního počítače](media/vminsights-enable-single-vm/enable-vminsights-vmss-portal.png)

    >[!NOTE]
    >Postup vytvoření nového pracovního prostoru Log Analytics pro uložení dat monitorování ze sady škálování virtuálního počítače najdete v tématu [Vytvoření pracovního prostoru Log Analytics](../learn/quick-create-workspace.md). Váš pracovní prostor Log Analytics musí patřit do jedné z [podporovaných oblastí](vminsights-enable-overview.md#log-analytics).

6. Po provedení konfigurace se zobrazí stavové zprávy.

    >[!NOTE]
    >Pokud pro sadu škálování používáte ruční model upgradu, upgradujte instance, aby se dokončila instalace. Upgrady můžete zahájit na stránce **instance** v části **Nastavení** .
    
    ![Povolení zpracování nasazení Azure Monitor pro virtuální počítače monitorování](media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status.png)

Teď, když jste povolili monitorování pro virtuální počítač nebo sadu škálování virtuálních počítačů, jsou informace o monitorování k dispozici pro analýzu v Azure Monitor pro virtuální počítače. 

## <a name="next-steps"></a>Další kroky

* Pokud chcete zobrazit zjištěné závislosti aplikací, přečtěte si téma [použití Azure monitor pro virtuální počítače mapa](vminsights-maps.md). 
* Pokud chcete zjistit kritické body, celkové využití a výkon vašeho virtuálního počítače, přečtěte si téma [zobrazení výkonu virtuálních počítačů Azure](vminsights-performance.md).
