---
title: Povolit Azure Monitor pro jeden virtuální počítač nebo sadu škálování virtuálního počítače v Azure Portal
description: Naučte se, jak povolit přehledy o virtuálních počítačích na jednom virtuálním počítači Azure nebo v sadě škálování virtuálních počítačů pomocí Azure Portal.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: 076fcab7b0747a7993407edd65f9d08efc27309f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035581"
---
# <a name="enable-azure-monitor-for-single-virtual-machine-or-virtual-machine-scale-set-in-the-azure-portal"></a>Povolit Azure Monitor pro jeden virtuální počítač nebo sadu škálování virtuálního počítače v Azure Portal
Tento článek popisuje, jak povolit službu VM Insights pro virtuální počítač nebo sadu škálování virtuálního počítače pomocí Azure Portal. Tento postup lze použít pro následující:

- Virtuální počítač Azure
- Sada škálování virtuálních počítačů Azure
- Hybridní virtuální počítač připojený pomocí ARC Azure

## <a name="prerequisites"></a>Požadavky

- [Vytvořte a nakonfigurujte Log Analytics pracovní prostor](./vminsights-configure-workspace.md). Případně můžete vytvořit nový pracovní prostor během tohoto procesu.
- V části [podporované operační systémy](./vminsights-enable-overview.md#supported-operating-systems) se ujistěte, že je podporovaný operační systém virtuálního počítače nebo sady škálování virtuálních počítačů, které chcete povolit. 

## <a name="enable-vm-insights"></a>Povolit přehledy virtuálních počítačů

Z Azure Portal vyberte **virtuální počítače**, **Virtual Machine Scale Sets** nebo **servery – Azure ARC** a vyberte prostředek ze seznamu. V části **monitorování** v nabídce vyberte **přehledy** a pak **Povolit**. Následující příklad ukazuje virtuální počítač Azure, ale nabídka je podobná sadě Azure Virtual Machine Scale set nebo ARC Azure.

![Povolení přehledů virtuálních počítačů pro virtuální počítač](media/vminsights-enable-portal/enable-vminsights-vm-portal.png)

Pokud virtuální počítač ještě není připojený k Log Analyticsmu pracovnímu prostoru, zobrazí se výzva k výběru jednoho. Pokud jste ještě nikdy [nevytvořili pracovní prostor](../logs/quick-create-workspace.md), můžete vybrat výchozí hodnotu pro umístění, kde je virtuální počítač nebo sada škálování virtuálního počítače nasazené v rámci předplatného. Tento pracovní prostor se vytvoří a nakonfiguruje, pokud ještě neexistuje. Pokud vyberete existující pracovní prostor, nakonfiguruje se pro přehledy virtuálních počítačů, pokud ještě není.

> [!NOTE]
> Pokud vyberete pracovní prostor, který nebyl dříve nakonfigurovaný pro službu VM Insights, *VMInsights* Management Pack se přidá do tohoto pracovního prostoru. Tato možnost se použije na libovolného agenta, který už je připojený k pracovnímu prostoru, a to bez ohledu na to, jestli je povolený pro službu VM Insights. Data o výkonu se budou shromažďovat z těchto virtuálních počítačů a ukládají se do tabulky *InsightsMetrics* .

![Výběr pracovního prostoru](media/vminsights-enable-portal/select-workspace.png)

Po provedení konfigurace se zobrazí stavové zprávy.

>[!NOTE]
>Pokud pro sadu škálování virtuálních počítačů používáte ruční model upgradu, upgradujte instance, aby se dokončila instalace. Upgrady můžete zahájit na stránce **instance** v části **Nastavení** .

![Povolit zpracování nasazení monitorování pro službu VM Insights](media/vminsights-enable-portal/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>Další kroky

* Podívejte se na téma [použití mapy virtuálních počítačů](vminsights-maps.md) k zobrazení zjištěných závislostí aplikací. 
* V tématu [zobrazení výkonu virtuálních počítačů Azure](vminsights-performance.md) můžete identifikovat kritická místa, celkové využití a výkon vašeho virtuálního počítače.
