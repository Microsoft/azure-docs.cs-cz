---
title: Zrušení propojení pracovního prostoru s účtem služby Automation pro Change Tracking a inventář
description: V tomto článku se dozvíte, jak odpojit Log Analytics pracovní prostor od účtu Automation pro Change Tracking a inventáře.
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: df2de44c2c8831fa4319b80484a119052434f8fb
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749106"
---
# <a name="unlink-workspace-from-automation-account-for-change-tracking-and-inventory"></a>Zrušení propojení pracovního prostoru s účtem služby Automation pro Change Tracking a inventář

Při povolování operací [Change Tracking a inventarizace](change-tracking.md) se můžete rozhodnout, že váš účet Automation nebudete integrovat s Log Analytics pracovním prostorem. V tomto článku se dozvíte, jak zrušit propojení pracovního prostoru s vaším účtem.

1. Přihlaste se k Azure na adrese https://portal.azure.com .

2. Odeberte Update Management pro vaše virtuální počítače. Viz [Odebrání virtuálních počítačů z Change Tracking a inventáře](automation-remove-vms-from-change-tracking.md).

3. Pokud Change Tracking a inventáře obsahují starší verze monitorování Azure SQL, může instalační program vytvořit assety automatizace, které byste měli odebrat. Vyhledejte tyto prostředky a odstraňte je.

4. Otevřete účet Automation a v části **související prostředky** na levé straně vyberte **propojený pracovní prostor** .

5. Na stránce zrušit propojení pracovního prostoru klikněte na **Zrušit propojení pracovního prostoru** a odpovězte na výzvy.

   ![Zrušit propojení stránky pracovního prostoru](media/automation-unlink-workspace-change-tracking/automation-unlink-workspace-blade.png).

6. I když se Azure Automation pokusí zrušit propojení pracovního prostoru Log Analytics, můžete sledovat průběh v nabídce **oznámení** .

Případně můžete zrušit propojení pracovního prostoru Log Analytics z účtu Automation v rámci pracovního prostoru:

1. V pracovním prostoru vyberte **účet Automation** v části **související prostředky**. 
2. Na stránce účet Automation vyberte zrušit **propojení účtu**.

## <a name="next-steps"></a>Další kroky

* [Správa Change Tracking a inventáře](change-tracking-file-contents.md)
* [Odebrání virtuálních počítačů z Change Tracking a inventáře](automation-remove-vms-from-change-tracking.md)
* [Řešení potíží se změnami na virtuálním počítači Azure](automation-tutorial-troubleshoot-changes.md)
* [Řešení problémů s Change Tracking a inventářem](troubleshoot/change-tracking.md)
