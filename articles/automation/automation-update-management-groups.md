---
title: Použití dynamických skupin se správou aktualizací Azure Automation
description: Tento článek popisuje, jak dynamické skupiny pracují se správou aktualizací Azure Automation.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 29a72eb1fe7b8be18cd2160fc63160e408378585
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617445"
---
# <a name="use-dynamic-groups-with-update-management"></a>Použití dynamických skupin s Update Managementem

Správa aktualizací umožňuje cílit na dynamickou skupinu virtuálních počítačů Azure nebo virtuálních počítačích, které nejsou azure, pro nasazení aktualizací. Tyto skupiny, definované dotazy, jsou vyhodnocovány v době nasazení, takže není třeba upravovat nasazení přidat počítače.

## <a name="azure-machines"></a>Počítače Azure

Dynamické skupiny nefungují s klasickými virtuálními virtuálními min. Při definování dotazu lze k naplnění dynamické skupiny společně použít následující položky:

* Předplatné
* Skupiny prostředků
* Umístění
* Značky

![Vybrat skupiny](./media/automation-update-management/select-groups.png)

Pokud chcete zobrazit náhled výsledků dynamické skupiny, klikněte na **Náhled**. Náhled zobrazuje členství ve skupině v aktuálním čase. V příkladu hledáme počítače, které mají `Role` značku pro skupinu **BackendServer**. Pokud více počítačů má tuto značku přidána, jsou přidány do jakékoli budoucí nasazení proti této skupině.

![skupiny náhledů](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Počítače, které nejsou azure

Pro počítače jiného výrobce Azure se k vytvoření dynamické skupiny používají uložená hledání, označovaná také jako skupiny počítačů. Informace o vytvoření uloženého hledání naleznete [v tématu Vytvoření skupiny počítačů](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Po vytvoření skupiny ji můžete vybrat ze seznamu uložených hledání. Kliknutím na **Náhled** zobrazíte náhled počítačů v uloženém hledání v té době.

![Vybrat skupiny](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Další kroky

Po vytvoření dynamické skupiny můžete [vytvořit nasazení aktualizace](automation-tutorial-update-management.md).
