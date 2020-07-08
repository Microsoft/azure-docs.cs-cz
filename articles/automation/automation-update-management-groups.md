---
title: Použití dynamických skupin s Azure Automation Update Management
description: V tomto článku se dozvíte, jak používat dynamické skupiny s Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: c440862f1379983d4644fe99f33207456f747d23
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83830935"
---
# <a name="use-dynamic-groups-with-update-management"></a>Použití dynamických skupin s Update Managementem

Update Management umožňuje cílit na dynamickou skupinu virtuálních počítačů Azure nebo mimo Azure pro nasazení aktualizací. Použití dynamické skupiny vám umožní upravit nasazení a aktualizovat počítače.

> [!NOTE]
> Dynamické skupiny nefungují s klasickými virtuálními počítači.

V Azure Portal můžete definovat dynamické skupiny pro počítače Azure nebo mimo Azure ze **správy aktualizací** . Viz [Správa aktualizací pro několik virtuálních počítačů Azure](manage-update-multi.md).

Dynamická skupina je definována dotazem, který Azure Automation vyhodnocuje v době nasazení. I když dotaz dynamické skupiny načte velký počet počítačů, Azure Automation může najednou zpracovat maximálně 1000 počítačů. Viz [Limity, kvóty a omezení předplatného a služeb Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#update-management). 

> [!NOTE]
> Pokud očekáváte, že aktualizujete více než 1000 počítačů, doporučujeme rozdělit aktualizace mezi více plánů aktualizací. 

## <a name="define-dynamic-groups-for-azure-machines"></a>Definování dynamických skupin pro počítače Azure

Při definování dynamického dotazu skupiny pro počítače Azure můžete použít následující položky k naplnění dynamické skupiny:

* Předplatné
* Skupiny prostředků
* Umístění
* Značky

![Vybrat skupiny](./media/automation-update-management/select-groups.png)

Pokud chcete zobrazit náhled výsledků dotazu dynamické skupiny, klikněte na **Náhled**. Ve verzi Preview se zobrazuje členství ve skupině v aktuálním čase. V tomto příkladu hledáme počítače, které mají značku `Role` pro skupinu **BackendServer**. Pokud je tato značka přidána na více počítačů, přidají se do jakéhokoli budoucího nasazení v této skupině.

![skupiny náhledu](./media/automation-update-management/preview-groups.png)

## <a name="define-dynamic-groups-for-non-azure-machines"></a>Definování dynamických skupin pro počítače mimo Azure

Dynamická skupina pro počítače mimo Azure používá uložená hledání, označované taky jako skupiny počítačů. Informace o tom, jak vytvořit uložené hledání, najdete v tématu [Vytvoření skupiny počítačů](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Po vytvoření uloženého hledání ho můžete vybrat ze seznamu uložených hledání ve **správě aktualizací** v Azure Portal. Kliknutím na **Náhled** zobrazíte v uložených hledáních počítače.

![Vybrat skupiny](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Další kroky

Pokud chcete pracovat s Update Management, přečtěte si téma [Správa aktualizací a oprav pro virtuální počítače Azure](automation-tutorial-update-management.md).
