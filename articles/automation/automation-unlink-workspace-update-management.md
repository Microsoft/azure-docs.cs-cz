---
title: Zrušit propojení pracovního prostoru s účtem služby Automation pro Update Management
description: V tomto článku se dozvíte, jak odpojit Log Analytics pracovní prostor od účtu Automation pro Update Management
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 9129d10071a4c8da0376cbad3d64c10cbaceb8b9
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835780"
---
# <a name="unlink-workspace-from-automation-account-for-update-management"></a>Zrušit propojení pracovního prostoru s účtem služby Automation pro Update Management

Během [Update Management](automation-update-management.md) operací se můžete rozhodnout, že váš účet Automation nebudete integrovat s Log Analytics pracovním prostorem. V tomto článku se dozvíte, jak zrušit propojení pracovního prostoru s vaším účtem.

1. Přihlaste se k Azure na adrese https://portal.azure.com .

2. Odeberte Update Management pro vaše virtuální počítače. Viz [Odebrání virtuálních počítačů z Update Management](automation-remove-vms-from-update-management.md).

3. Pokud Update Management zahrnuje starší verze monitorování Azure SQL, mohla by instalační program vytvořit assety automatizace, které byste měli odebrat. V případě Update Management možná budete chtít odebrat následující položky, které už nepotřebujete:

   * Plány aktualizací – každý má název, který odpovídá vytvořenému nasazení aktualizace.
   * Skupiny hybridních pracovních procesů vytvořené pro Update Management – každá je pojmenována podobně jako machine1. contoso. com_9ceb8108-26c9-4051-b6b3-227600d715c8).

4. Otevřete účet Automation a v části **související prostředky** na levé straně vyberte **propojený pracovní prostor** .

5. Na stránce zrušit propojení pracovního prostoru klikněte na **Zrušit propojení pracovního prostoru** a odpovězte na výzvy.

   ![Zrušit propojení stránky pracovního prostoru](media/automation-unlink-workspace-update-management/automation-unlink-workspace-blade.png).

6. I když se Azure Automation pokusí zrušit propojení pracovního prostoru Log Analytics, můžete sledovat průběh v nabídce **oznámení** .

Případně můžete zrušit propojení pracovního prostoru Log Analytics z účtu Automation v rámci pracovního prostoru:

1. V pracovním prostoru vyberte **účet Automation** v části **související prostředky**. 
2. Na stránce účet Automation vyberte zrušit **propojení účtu**.

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak používat tuto funkci, najdete v tématu [Správa aktualizací a oprav pro virtuální počítače Azure](automation-tutorial-update-management.md).
* Informace o řešení chyb funkcí najdete v tématu [řešení potíží s Update Management](troubleshoot/update-management.md).
* Problémy s chybami agenta Windows Update najdete v tématu řešení potíží s [agentem pro Windows Update](troubleshoot/update-agent-issues.md).
* Problémy s chybami agenta aktualizací pro Linux najdete v tématu [řešení potíží s aktualizacemi agenta pro Linux](troubleshoot/update-agent-issues-linux.md).
