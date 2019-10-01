---
title: Přidání rozšířené metriky pro virtuální počítače Azure | Microsoft Docs
description: Tento článek vám pomůže zapnout a nakonfigurovat metriky rozšířené diagnostiky pro vaše virtuální počítače Azure.
services: cost-management
keywords: ''
author: bandersmsft
manager: vitavor
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management
ms.custom: seodec18
ms.openlocfilehash: e1d0beb6ced0d582166d556c1ae2fc17b375dddf
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695370"
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Přidání rozšířené metriky pro virtuální počítače Azure

Cloudyn používá data metrik Azure z vašich virtuálních počítačů Azure k zobrazení podrobných informací o svých prostředcích. Data metriky, označované taky jako čítače výkonu, používá Cloudyn ke generování sestav. Cloudyn ale automaticky neshromažďuje všechna data metrik Azure z virtuálních počítačů hosta – musíte povolit shromažďování metrik. Tento článek vám pomůže povolit a nakonfigurovat další diagnostické metriky pro vaše virtuální počítače Azure.

Po povolení shromažďování metrik můžete:

- Zjistěte, kdy vaše virtuální počítače dosáhnou maximální velikosti paměti, disku a procesoru.
- Detekuje trendy využití a anomálie.
- Řiďte své náklady určením velikosti podle využití.
- Získejte cenově efektivní doporučení Optimalizace velikosti z Cloudyn.

Můžete například chtít monitorovat procesor% a paměť% vašich virtuálních počítačů Azure. Metriky virtuálních počítačů Azure odpovídají _procentuální hodnotě CPU_ a _\Memory @ No__t-2 potvrzených bajtů_.

> [!NOTE]
> Rozšířená shromažďování dat metriky se podporuje jenom s monitorováním na úrovni hosta Azure. Cloudyn není kompatibilní s [agentem Log Analytics](../azure-monitor/platform/agents-overview.md). 

## <a name="determine-whether-extended-metrics-are-enabled"></a>Určení, zda jsou povoleny rozšířené metriky

1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.
2. V části **virtuální počítače**vyberte virtuální počítač a potom v části **monitorování**vyberte **metriky**. Zobrazí se seznam dostupných metrik.
3. Vyberte některé metriky a v grafu se zobrazí data.  
    ![Ukázková metrika – procentuální hodnota CPU hostitele](./media/azure-vm-extended-metrics/metric01.png)

V předchozím příkladu jsou pro vaše hostitele k dispozici omezená sada standardních metrik, ale metriky paměti nejsou. Metriky paměti jsou součástí rozšířených metrik. V takovém případě nejsou rozšířené metriky pro virtuální počítač povolené. Chcete-li povolit rozšířené metriky, je nutné provést několik dalších kroků. Následující informace vás provedou tím, že je povolíte.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Povolit rozšířenou metriku v Azure Portal

Standardní metriky jsou metriky hostitelských počítačů. Procento metriky _procesoru_ je jedním příkladem. K dispozici jsou také základní metriky pro virtuální počítače hosta a označují se také jako rozšířené metriky. Mezi příklady rozšířených metrik patří _\Memory @ no__t-1 používané Potvrzené bajty_ a _\Memory\Available bajty_.

Povolení rozšířených metrik je jednoduché. Pro každý virtuální počítač povolte monitorování na úrovni hosta. Když povolíte monitorování na úrovni hosta, na virtuálním počítači se nainstaluje agent Azure Diagnostics. Standardně se přidávají základní sada rozšířených metrik. Následující postup je stejný pro klasický a pravidelný virtuální počítač a stejný pro virtuální počítače se systémem Windows a Linux.

Mějte na paměti, že monitorování na úrovni hosta v Azure i Linux vyžaduje účet úložiště. Pokud povolíte monitorování na úrovni hosta, nebudete-li si vybírat existující účet úložiště, vytvoří se pro vás jeden.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>Povolit monitorování na úrovni hosta u existujících virtuálních počítačů

1. V **Virtual Machines**zobrazte seznam virtuálních počítačů a potom vyberte virtuální počítač.
2. V části **monitorování**vyberte **nastavení diagnostiky**.
3. Na stránce nastavení diagnostiky klikněte na **Povolit monitorování na úrovni hosta**.  
    monitorování úrovně hosta @no__t 0Enable na stránce přehledu @ no__t-1
4. Po několika minutách se na virtuálním počítači nainstaluje agent diagnostiky Azure. Přidávají se základní sada metrik. Aktualizujte stránku. Přidané čítače výkonu se zobrazí na kartě Přehled.
5. V části monitorování vyberte **metriky**.
6. V grafu metriky pod **oborem názvů metrika**vyberte **Host (Classic)** .
7. V seznamu metriky můžete zobrazit všechny dostupné čítače výkonu pro virtuální počítač hosta.  
    ![seznam příkladů rozšířených metrik](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Povolit monitorování na úrovni hosta na nových virtuálních počítačích

Když vytváříte nové virtuální počítače, vyberte na kartě Správa možnost **zapnuto** pro **diagnostiku hosta operačního systému**.

![nastavit diagnostiku hostovaného operačního systému na zapnuto](./media/azure-vm-extended-metrics/new-enable-diag.png)

Další informace o povolení rozšířených metrik pro virtuální počítače Azure najdete v tématu [Principy a použití agenta Azure Linux](../virtual-machines/extensions/agent-linux.md) a [agenta virtuálního počítače Azure](../virtual-machines/extensions/agent-windows.md).

## <a name="resource-manager-credentials"></a>Správce prostředků přihlašovací údaje

Po povolení rozšířených metrik zajistěte, aby Cloudyn měl přístup k vašim [přihlašovacím údajům správce prostředků](activate-subs-accounts.md). Aby Cloudyn mohli shromažďovat a zobrazovat údaje o výkonu pro vaše virtuální počítače, jsou vyžadovány vaše přihlašovací údaje. Používají se také k vytváření doporučení pro optimalizaci nákladů. Cloudyn potřebuje alespoň tři dny dat výkonu z instance, aby bylo možné zjistit, zda se jedná o kandidáta na možnost doporučení.

## <a name="enable-vm-metrics-with-a-script"></a>Povolení metrik virtuálních počítačů pomocí skriptu

Metriky virtuálních počítačů můžete povolit pomocí Azure PowerShell skriptů. Pokud máte mnoho virtuálních počítačů, na kterých chcete povolit metriky, můžete použít skript k automatizaci procesu. Ukázkové skripty jsou na GitHubu v [Azure povolení diagnostiky](https://github.com/Cloudyn/azure-enable-diagnostics).

## <a name="view-azure-performance-metrics"></a>Zobrazit metriky výkonu Azure

Pokud chcete zobrazit metriky výkonu na instancích Azure na portálu Cloudyn, přejděte na **assets** > **COMPUTE** > **instance Explorer**. V seznamu instancí virtuálních počítačů rozbalte instanci a potom rozbalte prostředek pro zobrazení podrobností.

![Příklady informací zobrazených v Průzkumníkovi instancí](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>Další kroky

- Pokud jste ještě nepovolili Azure Resource Manager přístup k rozhraní API pro vaše účty, přejděte k možnostem [Aktivace účtů a předplatných Azure](activate-subs-accounts.md).
