---
title: Přidání rozšířených metrik pro virtuální počítače Azure
description: Tento článek vám pomůže povolit a nakonfigurovat metriky rozšířené diagnostiky pro vaše virtuální počítače Azure.
author: bandersmsft
ms.reviewer: vitavor
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: da2e20e333fe499998fff72b175442650f5f1e28
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100595309"
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Přidání rozšířených metrik pro virtuální počítače Azure

Cloudyn používá data metrik Azure z vašich virtuálních počítačů Azure k zobrazení podrobných informací o prostředcích. Data metrik, označovaná taky jako čítače výkonu, používá Cloudyn ke generování sestav. Cloudyn ale neshromažďuje všechna data metrik Azure z virtuálních počítačů hostů automaticky – musíte povolit shromažďování metrik. Tento článek vám pomůže povolit a nakonfigurovat metriky dodatečné diagnostiky pro vaše virtuální počítače Azure.

Když povolíte shromažďování metrik, získáte tyto možnosti:

- Zjistíte, kdy vaše virtuální počítače dosáhnou maximální velikosti paměti, disku a procesoru.
- Určíte trendy využití a anomálie.
- Budete moct řídit své náklady určením velikosti podle využití.
- Získáte cenově efektivní doporučení pro optimalizaci velikosti ze služby Cloudyn.

Budete například chtít monitorovat procento využití procesoru a paměti ve vašich virtuálních počítačích Azure. Tomu odpovídají metriky virtuálních počítačů Azure _Procento procesoru_ a _\Paměť\% využití potvrzených bajtů paměti_.

> [!NOTE]
> Rozšířené shromažďování dat metrik je podporováno jenom s monitorováním na úrovni hosta Azure. Cloudyn není kompatibilní s [agentem Log Analytics](../../azure-monitor/agents/agents-overview.md).

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="determine-whether-extended-metrics-are-enabled"></a>Zjištění, zda jsou povoleny rozšířené metriky

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
2. V části **Virtuální počítače** vyberte virtuální počítač a v části **Monitorování** vyberte **Metriky**. Zobrazí se seznam dostupných metrik.
3. Vyberte některé metriky a v grafu se zobrazí data.  
    ![Ukázková metrika – procento procesoru hostitele](./media/azure-vm-extended-metrics/metric01.png)

V předchozím příkladu je pro vaše hostitele k dispozici omezená sada standardních metrik, ale metriky paměti tam nejsou. Metriky paměti jsou součástí rozšířených metrik. V tomto případě nejsou rozšířené metriky pro virtuální počítač povolené. Chcete-li povolit rozšířené metriky, je nutné provést několik dalších kroků. Následující informace představují návod k jejich povolení.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Povolení rozšířených metrik na webu Azure Portal

Standardní metriky jsou metriky hostitelských počítačů. Příkladem je _Procento procesoru_. K dispozici jsou také základní metriky pro virtuální počítače hostů, které se také označují jako rozšířené metriky. Mezi příklady rozšířených metrik patří _\Paměť\% využití potvrzených bajtů paměti_ a _\Memory\Bajty k dispozici_.

Povolení rozšířených metrik je jednoduché. Pro každý virtuální počítač povolte monitorování na úrovni hosta. Když povolíte monitorování na úrovni hosta, na virtuálním počítači se nainstaluje agent Azure Diagnostics. Standardně se přidává základní sada rozšířených metrik. Následující postup je stejný pro klasické a běžné virtuální počítače a pro virtuální počítače se systémem Windows i Linux.

Mějte na paměti, že k monitorování na úrovni hosta ve Windows i Linuxu je nutný účet úložiště. Povolením monitorování na úrovni hosta se vám účet úložiště vytvoří, pokud si nevyberete existující.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>Povolení monitorování na úrovni hosta u existujících virtuálních počítačů

1. V části **Virtuální počítače** zobrazte seznam virtuálních počítačů a některý vyberte.
2. V části **Monitorování** vyberte **Nastavení diagnostiky**.
3. Na stránce nastavení diagnostiky klikněte na **Povolit monitorování na úrovni hosta**.  
    ![Povolení monitorování na úrovni hosta na stránce Přehled](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
4. Po několika minutách se ve virtuálním počítači nainstaluje agent diagnostiky Azure. Přidá se základní sada metrik. Aktualizujte stránku. Přidané čítače výkonu se zobrazí na kartě Přehled.
5. V části Monitorování vyberte **Metriky**.
6. V grafu metrik v části **Obor názvů metriky** vyberte **Host (klasický)** .
7. V seznamu Metriky můžete zobrazit všechny dostupné čítače výkonu pro virtuální počítač hosta.  
    ![Ukázka seznamu rozšířených metrik](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Povolení monitorování na úrovni hosta u nových virtuálních počítačů

Když vytváříte nové virtuální počítače, vyberte na kartě Správa možnost **Zapnuto** pro **Diagnostiku hosta operačního systému**.

![Zapnutí diagnostiky hosta operačního systému](./media/azure-vm-extended-metrics/new-enable-diag.png)

Další informace o povolení rozšířených metrik pro virtuální počítače Azure najdete v tématu [Principy a použití agenta Azure Linux](../../virtual-machines/extensions/agent-linux.md) a [Přehled agenta virtuálního počítače Azure](../../virtual-machines/extensions/agent-windows.md).

## <a name="resource-manager-credentials"></a>Přihlašovací údaje služby Resource Manager

Jakmile povolíte rozšířené metriky, ujistěte se, že má služba Cloudyn přístup k [přihlašovacím údajům služby Resource Manager](./activate-subs-accounts.md). Aby služba Cloudyn mohla shromažďovat a zobrazovat údaje o výkonu vašich virtuálních počítačů, jsou nutné vaše přihlašovací údaje. Používají se také k vytváření doporučení pro optimalizaci nákladů. Služba Cloudyn potřebuje data o výkonu instance alespoň za tři dny, aby mohla určit, zda se jedná o kandidáta pro doporučené zmenšení velikosti.

## <a name="enable-vm-metrics-with-a-script"></a>Povolení metrik virtuálních počítačů pomocí skriptu

Metriky virtuálních počítačů můžete povolit pomocí skriptů Azure PowerShell. Pokud máte mnoho virtuálních počítačů, na kterých chcete povolit metriky, můžete k automatizaci procesu použít skript. Ukázkové skripty jsou na GitHubu na adrese [Azure Enable Diagnostics](https://github.com/Cloudyn/azure-enable-diagnostics).

## <a name="view-azure-performance-metrics"></a>Zobrazení metrik výkonu Azure

Pokud chcete zobrazit metriky výkonu instancí Azure na portálu Cloudyn, přejděte na **Assets** > **Compute** > **Instance Explorer** (Prostředky > Výpočty > Průzkumník instancí). V seznamu instancí virtuálních počítačů rozbalte instanci a potom rozbalením prostředku zobrazte podrobnosti.

![Ukázkové informace zobrazené v průzkumníku instancí](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>Další kroky

- Pokud jste ještě nepovolili přístup rozhraní API Azure Resource Manager pro vaše účty, pokračujte k článku [Aktivace účtů a předplatných Azure](./activate-subs-accounts.md).