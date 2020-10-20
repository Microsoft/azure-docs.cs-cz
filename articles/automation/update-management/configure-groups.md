---
title: Použití dynamických skupin s Azure Automation Update Management
description: V tomto článku se dozvíte, jak používat dynamické skupiny s Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: a2df2489e4b216b4b33524e35691bf93468edda0
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92222157"
---
# <a name="use-dynamic-groups-with-update-management"></a>Použití dynamických skupin s Update Managementem

Update Management umožňuje cílit na dynamickou skupinu virtuálních počítačů Azure nebo mimo Azure pro nasazení aktualizací. Použití dynamické skupiny vám umožní upravit nasazení a aktualizovat počítače.

> [!NOTE]
> Dynamické skupiny nefungují s klasickými virtuálními počítači.

V Azure Portal můžete definovat dynamické skupiny pro počítače Azure nebo mimo Azure ze **správy aktualizací** . Viz [Správa aktualizací pro virtuální počítače](manage-updates-for-vm.md).

Dynamická skupina je definována dotazem, který Azure Automation vyhodnocuje v době nasazení. I když dotaz dynamické skupiny načte velký počet počítačů, Azure Automation může najednou zpracovat maximálně 1000 počítačů. Viz [Limity, kvóty a omezení předplatného a služeb Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md#update-management).

> [!NOTE]
> Pokud očekáváte, že aktualizujete více než 1000 počítačů, doporučujeme rozdělit aktualizace mezi více plánů aktualizací. 

## <a name="define-dynamic-groups-for-azure-machines"></a>Definování dynamických skupin pro počítače Azure

Při definování dynamického dotazu skupiny pro počítače Azure můžete použít následující položky k naplnění dynamické skupiny:

* Předplatné
* Skupiny prostředků
* Umístění
* Značky

![Vybrat skupiny](./media/configure-groups/select-groups.png)

Pokud chcete zobrazit náhled výsledků dotazu dynamické skupiny, klikněte na **Náhled**. Ve verzi Preview se zobrazuje členství ve skupině v aktuálním čase. V tomto příkladu hledáme počítače, které mají značku `Role` pro skupinu **BackendServer**. Pokud je tato značka přidána na více počítačů, přidají se do jakéhokoli budoucího nasazení v této skupině.

![skupiny náhledu](./media/configure-groups/preview-groups.png)

## <a name="define-dynamic-groups-for-non-azure-machines"></a>Definování dynamických skupin pro počítače mimo Azure

Dynamická skupina pro počítače mimo Azure používá uložená hledání, označované taky jako skupiny počítačů. Informace o tom, jak vytvořit uložené hledání, najdete v tématu [Vytvoření skupiny počítačů](../../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Po vytvoření uloženého hledání ho můžete vybrat ze seznamu uložených hledání ve **správě aktualizací** v Azure Portal. Kliknutím na **Náhled** zobrazíte v uložených hledáních počítače.

![Snímek obrazovky se zobrazí stránka vybrat skupiny pro jiný než Azure (Preview) a podokno náhledu na pravé straně.](./media/configure-groups/select-groups-2.png)

## <a name="next-steps"></a>Další kroky

Můžete zadat [dotaz na protokoly Azure monitor](query-logs.md) , abyste mohli analyzovat posouzení aktualizací, nasazení a další související úlohy správy. Obsahuje předem definované dotazy, které vám pomůžou začít.
