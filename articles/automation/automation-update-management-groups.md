---
title: Použití dynamických skupin s Azure Update Management
description: Popisuje, jak dynamické skupiny pracují s Update Management
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: edc4384be0f1dc73f2e7e098114080d304d92ce8
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377743"
---
# <a name="use-dynamic-groups-with-update-management"></a>Použití dynamických skupin s Update Management

Update Management poskytuje možnost cílit na dynamickou skupinu virtuálních počítačů Azure nebo mimo Azure pro nasazení aktualizací. Tyto skupiny jsou vyhodnocovány v době nasazení, takže není nutné upravovat nasazení, aby bylo možné přidat počítače.

## <a name="azure-machines"></a>Počítače Azure

Tyto skupiny jsou definovány dotazem, když je zahájeno nasazení aktualizace, členové této skupiny budou vyhodnocováni. Dynamické skupiny nefungují s klasickými virtuálními počítači. Při definování dotazu lze společně použít následující položky k naplnění dynamické skupiny:

* Předplatné
* Skupiny prostředků
* Umístění
* Značky

![Vybrat skupiny](./media/automation-update-management/select-groups.png)

Chcete-li zobrazit náhled výsledků dynamické skupiny, klikněte na tlačítko **Náhled** . V této verzi Preview se v této době zobrazuje členství ve skupině. v tomto příkladu hledáme počítače s **rolí** značek, které se rovnají **BackendServer**. Pokud se tato značka přidá do dalších počítačů, přidají se do všech budoucích nasazení v této skupině.

![skupiny náhledu](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Počítače mimo Azure

Pro počítače, které nejsou v Azure, se při vytváření dynamické skupiny používají taky uložená hledání, která se označují jako skupiny počítačů. Informace o tom, jak vytvořit uložené hledání, najdete v tématu [Vytvoření skupiny počítačů](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Po vytvoření skupiny ji můžete vybrat ze seznamu uložených hledání. Kliknutím na **Náhled** zobrazíte v daném čase počítače v uloženém hledání.

![Vybrat skupiny](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Další kroky

Po vytvoření dynamické skupiny můžete [vytvořit nasazení aktualizace](automation-tutorial-update-management.md) .
