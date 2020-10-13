---
title: Zrušení propojení pracovního prostoru s účtem služby Automation pro Change Tracking a inventář
description: V tomto článku se dozvíte, jak odpojit Log Analytics pracovní prostor od účtu Automation pro Change Tracking a inventáře.
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 2be702ec6e820fe71dd8d2da7aa4cf831b52402e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83828249"
---
# <a name="unlink-workspace-from-automation-account"></a>Odpojení pracovního prostoru od účtu Automation

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

* Pokud chcete pracovat s Change Tracking a inventářem, přečtěte si téma [správa Change Tracking a inventáře](change-tracking-file-contents.md).
* Řešení obecných potíží s funkcemi najdete v tématu [řešení potíží s Change Tracking a inventářem](troubleshoot/change-tracking.md).
