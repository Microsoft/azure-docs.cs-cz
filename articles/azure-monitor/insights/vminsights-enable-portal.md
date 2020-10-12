---
title: Povolit Azure Monitor pro jeden virtuální počítač nebo sadu škálování virtuálního počítače v Azure Portal
description: Naučte se, jak povolit Azure Monitor pro virtuální počítače na jednom virtuálním počítači Azure nebo sadě škálování virtuálních počítačů pomocí Azure Portal.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: ba6ae9262fb0e55c53e1b1421c075e555fae8d98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87328168"
---
# <a name="enable-azure-monitor-for-single-virtual-machine-or-virtual-machine-scale-set-in-the-azure-portal"></a>Povolit Azure Monitor pro jeden virtuální počítač nebo sadu škálování virtuálního počítače v Azure Portal
Tento článek popisuje, jak povolit Azure Monitor pro virtuální počítače pro virtuální počítač nebo sadu škálování virtuálního počítače pomocí Azure Portal. Tento postup lze použít pro následující:

- Virtuální počítač Azure
- Sada škálování virtuálních počítačů Azure
- Hybridní virtuální počítač připojený pomocí ARC Azure

## <a name="prerequisites"></a>Požadavky

- [Vytvořte a nakonfigurujte Log Analytics pracovní prostor](vminsights-configure-workspace.md). Případně můžete vytvořit nový pracovní prostor během tohoto procesu.
- V části [podporované operační systémy](vminsights-enable-overview.md#supported-operating-systems) se ujistěte, že je podporovaný operační systém virtuálního počítače nebo sady škálování virtuálních počítačů, které chcete povolit. 

## <a name="enable-azure-monitor-for-vms"></a>Povolit Azure Monitor pro virtuální počítače

Z Azure Portal vyberte **virtuální počítače**, **Virtual Machine Scale Sets**nebo **počítače – Azure ARC** a vyberte prostředek ze seznamu. V části **monitorování** v nabídce vyberte **přehledy** a pak **Povolit**. Následující příklad ukazuje virtuální počítač Azure, ale nabídka je podobná sadě Azure Virtual Machine Scale set nebo ARC Azure.

![Povolení Azure Monitor pro virtuální počítače pro virtuální počítač](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

Pokud virtuální počítač ještě není připojený k Log Analyticsmu pracovnímu prostoru, zobrazí se výzva k výběru jednoho. Pokud jste ještě nikdy [nevytvořili pracovní prostor](../../azure-monitor/learn/quick-create-workspace.md), můžete vybrat výchozí hodnotu pro umístění, kde je virtuální počítač nebo sada škálování virtuálního počítače nasazené v rámci předplatného. Tento pracovní prostor se vytvoří a nakonfiguruje, pokud ještě neexistuje. Pokud vyberete existující pracovní prostor, bude nakonfigurován pro Azure Monitor pro virtuální počítače, pokud ještě nebyl.

> [!NOTE]
> Pokud vyberete pracovní prostor, který nebyl dříve nakonfigurován pro Azure Monitor pro virtuální počítače, do tohoto pracovního prostoru se přidá Management Pack *VMInsights* . Tato možnost bude použita pro všechny agenty, kteří jsou již připojeni k pracovnímu prostoru, bez ohledu na to, zda je povoleno pro Azure Monitor pro virtuální počítače. Data o výkonu se budou shromažďovat z těchto virtuálních počítačů a ukládají se do tabulky *InsightsMetrics* .

![Výběr pracovního prostoru](media/vminsights-configure-workspace/select-workspace.png)

Po provedení konfigurace se zobrazí stavové zprávy.

>[!NOTE]
>Pokud pro sadu škálování virtuálních počítačů používáte ruční model upgradu, upgradujte instance, aby se dokončila instalace. Upgrady můžete zahájit na stránce **instance** v části **Nastavení** .

![Povolení zpracování nasazení Azure Monitor pro virtuální počítače monitorování](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>Další kroky

* Viz téma [použití Azure monitor pro virtuální počítače mapa](vminsights-maps.md) k zobrazení zjištěných závislostí aplikace. 
* V tématu [zobrazení výkonu virtuálních počítačů Azure](vminsights-performance.md) můžete identifikovat kritická místa, celkové využití a výkon vašeho virtuálního počítače.
