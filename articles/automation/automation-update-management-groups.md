---
title: Použití dynamických skupin s Azure Automation Update Management
description: Tento článek popisuje, jak dynamické skupiny pracují s Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 29a72eb1fe7b8be18cd2160fc63160e408378585
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617445"
---
# <a name="use-dynamic-groups-with-update-management"></a>Použití dynamických skupin s Update Managementem

Update Management umožňuje cílit na dynamickou skupinu virtuálních počítačů Azure nebo mimo Azure pro nasazení aktualizací. Tyto skupiny definované dotazy jsou vyhodnocovány v době nasazení, takže není nutné upravovat nasazení, aby bylo možné přidat počítače.

## <a name="azure-machines"></a>Počítače Azure

Dynamické skupiny nefungují s klasickými virtuálními počítači. Při definování dotazu lze společně použít následující položky k naplnění dynamické skupiny:

* Předplatné
* Skupiny prostředků
* Umístění
* Značky

![Vybrat skupiny](./media/automation-update-management/select-groups.png)

Pokud chcete zobrazit náhled výsledků dynamické skupiny, klikněte na **Náhled**. Ve verzi Preview se zobrazuje členství ve skupině v aktuálním čase. V tomto příkladu hledáme počítače, které mají značku `Role` pro skupinu **BackendServer**. Pokud je tato značka přidána na více počítačů, přidají se do jakéhokoli budoucího nasazení v této skupině.

![skupiny náhledu](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Počítače mimo Azure

Pro počítače, které nejsou v Azure, se při vytváření dynamické skupiny používají taky uložená hledání, označovaná jako skupiny počítačů. Informace o tom, jak vytvořit uložené hledání, najdete v tématu [Vytvoření skupiny počítačů](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Vytvořenou skupinu můžete vybrat ze seznamu uložených hledání. Kliknutím na **Náhled** zobrazíte v daném čase počítače v uloženém hledání.

![Vybrat skupiny](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Další kroky

Po vytvoření dynamické skupiny můžete [vytvořit nasazení aktualizace](automation-tutorial-update-management.md).
