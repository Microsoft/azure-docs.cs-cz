---
title: Použití dynamických skupin se správou aktualizací Azure
description: Tento článek popisuje, jak dynamické skupiny pracují se správou aktualizací Azure Automation.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 678b3f361e4456a2c482896f7d7dc20d530b917b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75420399"
---
# <a name="use-dynamic-groups-with-update-management"></a>Použití dynamických skupin s Update Managementem

Správa aktualizací umožňuje cílit na dynamickou skupinu virtuálních počítačů Azure nebo virtuálních počítačích, které nejsou azure, pro nasazení aktualizací. Tyto skupiny jsou vyhodnocovány v době nasazení, takže není třeba upravovat nasazení přidat počítače.

## <a name="azure-machines"></a>Počítače Azure

Tyto skupiny jsou definovány dotazem, když začne nasazení aktualizace, členové této skupiny jsou vyhodnoceny. Dynamické skupiny nefungují s klasickými virtuálními virtuálními min. Při definování dotazu lze k naplnění dynamické skupiny společně použít následující položky:

* Předplatné
* Skupiny prostředků
* Umístění
* Značky

![Vybrat skupiny](./media/automation-update-management/select-groups.png)

Chcete-li zobrazit náhled výsledků dynamické skupiny, klepněte na tlačítko **Náhled.** Tento náhled ukazuje členství ve skupině v té době, v tomto příkladu hledáme počítače s tag **role** se rovná **BackendServer**. Pokud tuto značku přidá více počítačů, budou přidány do všech budoucích nasazení proti této skupině.

![skupiny náhledů](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Počítače, které nejsou azure

Pro počítače jiného výrobce Azure se uložená hledání označovaná také jako skupiny počítačů používají k vytvoření dynamické skupiny. Informace o vytvoření uloženého hledání naleznete [v tématu Vytvoření skupiny počítačů](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Po vytvoření skupiny ji můžete vybrat ze seznamu uložených vyhledávání. Kliknutím na **Náhled** zobrazíte náhled počítačů v uloženém hledání v té době.

![Vybrat skupiny](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Další kroky

Po vytvoření dynamické skupiny můžete [vytvořit nasazení aktualizace](automation-tutorial-update-management.md)
