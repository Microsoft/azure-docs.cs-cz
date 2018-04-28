---
title: Přidání rozšířené metriky pro virtuální počítače Azure | Microsoft Docs
description: Tento článek vám umožňuje povolit a konfigurovat metriky rozšířené diagnostiky pro virtuální počítače Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 4b00baba44a4724ce8f6a45a80692f7f566a35ed
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Přidání rozšířené metriky pro virtuální počítače Azure

Náklady správy používá Azure metriky data z virtuálních počítačů Azure zobrazíte podrobné informace o jejich prostředky. Data metriky, označované taky jako čítače výkonu se používá ve náklady na správu ke generování sestav. Však náklady na správu není shromažďovat automaticky všechny Azure metriky data z virtuálních počítačů hostovaného – je nutné povolit metriky kolekce. Tento článek vám umožňuje povolit a nakonfigurovat další diagnostiky metriky pro virtuální počítače Azure.

Jakmile povolíte metriky kolekce, můžete:

- Vědět, když jsou virtuální počítače dosažení jejich paměti, disku a limity využití procesoru.
- Zjištění trendů využití a anomálií.
- Náklady na řídit nastavení velikosti podle využití.
- Získáte náklady efektivní změna velikosti optimalizace doporučení z náklady na správu.

Můžete například chtít sledovat % využití procesoru a paměti % virtuální počítače Azure. Metriky virtuálního počítače Azure odpovídají _[hostitel] procento procesoru_ a _procento paměti [hosta]_.

## <a name="verify-that-metrics-are-enabled-on-vms"></a>Ověřte, zda jsou povolena metriky na virtuálních počítačích

1. Přihlaste se k webu Azure Portal na adrese http://portal.azure.com.
2. V části **virtuální počítače**, vyberte virtuální počítač a potom v části **monitorování**, vyberte **metriky**. Se zobrazí seznam dostupných metriky.
3. Vyberte některé metriky a graf zobrazuje data pro ně.  
    ![Příklad metrika – procento procesoru hostitele](./media/azure-vm-extended-metrics/metric01.png)

V předchozím příkladu omezenou sadu standardní metriky, které jsou k dispozici pro hostitele, ale nejsou paměti metriky. Metriky paměti jsou součástí rozšířené metriky. Musíte provést některé další kroky pro rozšířené metriky povolit. Následující informace vás provede povolením.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Povolit rozšířené metriky na portálu Azure

Standardní metriky jsou metriky počítače hostitele. _[Hostitel] procento procesoru_ metrika je jedním z příkladů. Existují také základní metriky pro hostované virtuální počítače a také nazývají rozšířené metriky. Příklady rozšířené metriky _procento paměti [hosta]_ a _dostupné paměti [hosta]_.

Povolení rozšířené metriky je jednoduchá. Pro každý virtuální počítač povolte monitorování na úrovni hosta. Když povolíte monitorování na úrovni hosta, je na virtuálním počítači nainstalován agent nástroje Azure diagnostics. Následující postup je stejný pro virtuální počítače classic a běžné a stejný pro systém Windows a virtuální počítače s Linuxem.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>Povolit monitorování na úrovni hosta na existující virtuální počítače

1. V **virtuální počítače**, zobrazení seznamu virtuálních počítačů a potom vyberte virtuální počítač.
2. V části **monitorování**, vyberte **metriky**.
3. Klikněte na tlačítko **nastavení pro diagnostiku**.
4. Na stránce nastavení diagnostiky, klikněte na tlačítko **povolit sledování na úrovni hosta**. Virtuální počítače s Linuxem vyžadují stávající účet úložiště. Pokud nemáte účet úložiště pro virtuální počítač s Windows, jeden vytvoří se pro vás.  
    ![Povolit monitorování na úrovni hosta](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
5. Po několika minutách instalaci agenta Azure diagnostics ve virtuálním počítači. Aktualizujte stránku a metriky hosta se aktualizuje seznam dostupné metriky.  
    ![Rozšířené metriky](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Povolit monitorování na úrovni hosta na nové virtuální počítače

Při vytváření nové virtuální počítače, ujistěte se, že jste vybrali **hostovaného operačního systému diagnostiky**. Virtuální počítače s Linuxem vyžadují stávající účet úložiště. Pokud nemáte účet úložiště pro virtuální počítač s Windows, jeden vytvoří se pro vás.

![Povolte diagnostiku hostovaného operačního systému](./media/azure-vm-extended-metrics/new-enable-diag.png)

## <a name="resource-manager-credentials"></a>Přihlašovací údaje správce prostředků

Jakmile povolíte rozšířené metriky, zajistěte, aby náklady na správu měl přístup k vaší [přihlašovací údaje správce prostředků](activate-subs-accounts.md). Vaše přihlašovací údaje jsou nutná pro náklady na správu ke sběru a zobrazte údaje o výkonu pro virtuální počítače. Používají se také k vytvoření doporučení optimalizace nákladů. Cenově musí alespoň tři dny údaje o výkonu z instance chcete-li zjistit, jestli je kandidátem na downsizing doporučení.

## <a name="enable-vm-metrics-with-a-script"></a>Povolit metriky virtuálního počítače pomocí skriptu

Můžete povolit metriky virtuálního počítače pomocí skriptů prostředí Azure PowerShell. Pokud máte mnoho virtuálních počítačů, které chcete povolit metriky na, můžete použít skript pro automatizaci procesu. Příklad skriptů jsou na webu GitHub na [Azure Diagnostics povolit](https://github.com/Cloudyn/azure-enable-diagnostics).

## <a name="view-azure-performance-metrics"></a>Metriky výkonu Azure zobrazení

Chcete-li zobrazit metriky výkonu na vaší instancí Azure na portálu Cloudyn, přejděte na **prostředky** > **výpočetní** > **instanci Průzkumníka**. V seznamu instancí virtuálních počítačů rozbalte instance a poté rozbalte prostředků k zobrazení podrobností.

![Instance Explorer](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>Další postup

- Pokud jste ještě nepovolili přístup pomocí rozhraní API Správce prostředků Azure pro vaše účty, pokračujte [aktivovat Azure předplatných a účtů](activate-subs-accounts.md)
