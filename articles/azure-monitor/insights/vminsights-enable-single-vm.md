---
title: Povolit Azure Monitor pro virtuální počítače (Preview) pro vyhodnocení | Microsoft Docs
description: Přečtěte si, jak vyhodnotit Azure Monitor pro virtuální počítače na jednom virtuálním počítači Azure nebo v sadě škálování virtuálního počítače.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: 3b79eaeed420426fe79b2b6caf19ba1d1be61939
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77664607"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>Povolit Azure Monitor pro virtuální počítače (Preview) pro vyhodnocení

Můžete vyhodnotit Azure Monitor pro virtuální počítače (Preview) na malém počtu virtuálních počítačů Azure nebo na jednom virtuálním počítači nebo sadě škálování virtuálních počítačů. Nejjednodušší a nejbezpečnější způsob, jak povolit monitorování, je Azure Portal. Vaším cílem je monitorovat virtuální počítače a zjišťovat případné problémy s výkonem nebo dostupností. 

Než začnete, zkontrolujte [požadavky](vminsights-enable-overview.md) a ujistěte se, že vaše předplatné a prostředky splňují požadavky.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Povolení monitorování pro jeden virtuální počítač Azure
Pokud chcete povolit monitorování virtuálního počítače Azure:

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com).

1. Vyberte **Virtual Machines**.

1. V seznamu vyberte virtuální počítač.

1. Na stránce virtuální počítač v části **monitorování** vyberte **přehledy (Preview)** .

1. Na stránce **přehledy (Preview)** vyberte **vyzkoušet nyní**.

    ![Povolit monitorování Azure pro virtuální počítače pro virtuální počítač](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Pokud máte v rámci stejného předplatného pracovní prostor Log Analytics, v rozevíracím seznamu vyberte na stránce pro **registraci Azure monitor Insights** .  

    Seznam předchází výchozí pracovní prostor a umístění, kde je virtuální počítač nasazený v předplatném. 

    >[!NOTE]
    >Informace o vytvoření nového pracovního prostoru Log Analytics pro uložení dat monitorování z virtuálního počítače najdete v tématu [Vytvoření pracovního prostoru Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Váš pracovní prostor Log Analytics musí patřit do jedné z [podporovaných oblastí](vminsights-enable-overview.md#log-analytics).

Po povolení monitorování možná budete muset počkat asi 10 minut, než budete moct zobrazit metriky stavu virtuálního počítače.

![Povolit Azure Monitor pro monitorování zpracování nasazení virtuálních počítačů](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Povolit monitorování pro jednu sadu škálování virtuálního počítače

Pokud chcete povolit monitorování sady škálování virtuálních počítačů Azure:

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com).

2. Vyberte **Virtual Machine Scale Sets**.

3. V seznamu vyberte sadu škálování virtuálního počítače.

4. Na stránce sada škálování virtuálního počítače v části **monitorování** vyberte **přehledy (Preview)** .

5. Pokud chcete použít existující Log Analytics pracovní prostor, na stránce **přehledy (Preview)** ho vyberte v rozevíracím seznamu.

    Seznam předchází výchozí pracovní prostor a umístění, do kterého se virtuální počítač nasadí v rámci předplatného. 

    ![Povolení Azure Monitor pro virtuální počítače pro sadu škálování virtuálního počítače](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal.png)

    >[!NOTE]
    >Postup vytvoření nového pracovního prostoru Log Analytics pro uložení dat monitorování ze sady škálování virtuálního počítače najdete v tématu [Vytvoření pracovního prostoru Log Analytics](../learn/quick-create-workspace.md). Váš pracovní prostor Log Analytics musí patřit do jedné z [podporovaných oblastí](vminsights-enable-overview.md#log-analytics).

Po povolení monitorování možná budete muset počkat asi 10 minut, než budete moct zobrazit data monitorování pro sadu škálování.

>[!NOTE]
>Pokud pro sadu škálování používáte ruční model upgradu, upgradujte instance, aby se dokončila instalace. Upgrady můžete zahájit na stránce **instance** v části **Nastavení** .

![Povolit Azure Monitor pro monitorování zpracování nasazení virtuálních počítačů](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

Teď, když jste povolili monitorování pro virtuální počítač nebo sadu škálování virtuálních počítačů, jsou informace o monitorování k dispozici pro analýzu v Azure Monitor pro virtuální počítače. 

## <a name="next-steps"></a>Další kroky

* Pokud chcete zobrazit zjištěné závislosti aplikací, přečtěte si téma [použití Azure monitor pro virtuální počítače mapa](vminsights-maps.md). 
* Pokud chcete zjistit kritické body, celkové využití a výkon vašeho virtuálního počítače, přečtěte si téma [zobrazení výkonu virtuálních počítačů Azure](vminsights-performance.md).
