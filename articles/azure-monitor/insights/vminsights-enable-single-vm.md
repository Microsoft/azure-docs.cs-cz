---
title: Povolit Azure Monitor pro virtuální počítače v Azure Portal
description: Přečtěte si, jak vyhodnotit Azure Monitor pro virtuální počítače na jednom virtuálním počítači Azure nebo v sadě škálování virtuálního počítače.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: d4f14280f38a2389809477b84461eb3174909316
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323889"
---
# <a name="enable-azure-monitor-for-single-vm-or-vmss-in-the-azure-portal"></a>Povolit Azure Monitor pro jeden virtuální počítač nebo VMSS v Azure Portal
Tento článek popisuje, jak povolit Azure Monitor pro virtuální počítače pro jeden virtuální počítač nebo sadu škálování virtuálního počítače pomocí Azure Portal. Tento postup lze použít pro následující:

- Virtuální počítač Azure
- Sada škálování virtuálních počítačů Azure
- Počítač ARC Azure

Než začnete, zkontrolujte [požadavky](vminsights-enable-overview.md) a ujistěte se, že vaše předplatné a prostředky splňují požadavky.  

## <a name="enable-azure-monitor-for-vms"></a>Povolit Azure Monitor pro virtuální počítače

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyberte **virtuální počítače**, **sady škálování virtuálních počítačů**nebo **počítače – Azure ARC**.

1. Vyberte prostředek ze seznamu.

1. V části **monitorování** v nabídce vyberte **přehledy** a pak **Povolit**. Následující příklad ukazuje virtuální počítač Azure, ale nabídka je podobná pro Azure VMSS nebo Azure ARC.

    ![Povolení Azure Monitor pro virtuální počítače pro virtuální počítač](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Pokud virtuální počítač ještě není připojený k Log Analyticsmu pracovnímu prostoru, zobrazí se výzva k výběru jednoho. Pokud jste ještě předtím [nevytvořili pracovní prostor](../learn/quick-create-workspace.md), můžete vybrat výchozí hodnotu pro umístění, kde je virtuální počítač nebo VMSS nasazený v předplatném. Tento pracovní prostor se vytvoří a nakonfiguruje, pokud ještě neexistuje.

2. Po provedení konfigurace se zobrazí stavové zprávy.

    >[!NOTE]
    >Pokud pro sadu škálování používáte ruční model upgradu, upgradujte instance, aby se dokončila instalace. Upgrady můžete zahájit na stránce **instance** v části **Nastavení** .

    ![Povolení zpracování nasazení Azure Monitor pro virtuální počítače monitorování](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>Další kroky

* Pokud chcete zobrazit zjištěné závislosti aplikací, přečtěte si téma [použití Azure monitor pro virtuální počítače mapa](vminsights-maps.md). 
* Pokud chcete zjistit kritické body, celkové využití a výkon vašeho virtuálního počítače, přečtěte si téma [zobrazení výkonu virtuálních počítačů Azure](vminsights-performance.md).

