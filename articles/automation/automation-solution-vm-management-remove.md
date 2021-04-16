---
title: Přehled odebrání Azure Automation Start/Stop VMs during off-hours
description: Tento článek popisuje, jak odebrat funkci Start/Stop VMs during off-hours a zrušit propojení účtu Automation s pracovním prostorem Log Analytics.
services: automation
ms.subservice: process-automation
ms.date: 04/15/2021
ms.topic: conceptual
ms.openlocfilehash: 9ec76197bfde6bb679f70c44ab01712f9f52bfd2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533955"
---
# <a name="remove-startstop-vms-during-off-hours-from-automation-account"></a>Odebrat Start/Stop VMs during off-hours z účtu Automation

Po povolení funkce Start/Stop VMs during off-hours pro správu stavu spuštění virtuálních počítačů Azure se můžete rozhodnout ji přestat používat. Odebrání této funkce se dá provést pomocí jedné z následujících metod založených na podporovaných modelech nasazení:

* Odstraňte skupinu prostředků obsahující účet Automation a propojenou Azure Monitor Log Analytics pracovním prostoru, které jsou vyhrazené pro podporu této funkce.
* Odpojte pracovní prostor Log Analytics z účtu Automation a odstraňte účet služby Automation vyhrazený pro tuto funkci.
* Odstraňte tuto funkci z účtu Automation a propojeného pracovního prostoru, který podporuje další cíle správy a monitorování.

Odstraněním této funkce se odeberou jenom přidružené Runbooky, neodstraní se plány nebo proměnné, které se vytvořily během nasazování, nebo žádné vlastní definované.

> [!NOTE]
> Než budete pokračovat, ověřte, že v předplatném, skupině prostředků nebo prostředku nejsou použité žádné [Správce prostředků zámky](../azure-resource-manager/management/lock-resources.md) , které zabrání nechtěnému odstranění nebo změně důležitých prostředků. Když nasadíte řešení Start/Stop VMs during off-hours, nastaví úroveň zámku tak, aby se **CanNotDelete** na několik závislých prostředků v účtu Automation (konkrétně v sadách Runbook a proměnné). Než budete moct účet Automation odstranit, musíte odebrat všechny zámky.

## <a name="delete-the-dedicated-resource-group"></a>Odstranit vyhrazenou skupinu prostředků

Pokud chcete odstranit skupinu prostředků, postupujte podle kroků uvedených v článku [Azure Resource Manager skupiny prostředků a odstraňování prostředků](../azure-resource-manager/management/delete-resource-group.md) .

## <a name="delete-the-automation-account"></a>Odstranit účet Automation

Pokud chcete odstranit účet Automation vyhrazený pro Start/Stop VMs during off-hours, proveďte následující kroky.

1. Přihlaste se k Azure na adrese [https://portal.azure.com](https://portal.azure.com) .

2. Přejděte do svého účtu Automation a v části **související prostředky** vyberte **propojený pracovní prostor** .

3. Vyberte **Přejít k pracovnímu prostoru**.

4. V části **Obecné** klikněte na **řešení** .

5. Na stránce řešení vyberte **Start-Stop-VM [pracovní prostor]**.

6. Na stránce **VMManagementSolution [pracovní prostor]** v nabídce vyberte **Odstranit** .

7. I když jsou informace ověřené a funkce se odstraní, můžete sledovat průběh v části **oznámení**, kterou zvolíte v nabídce. Po odebrání se vrátíte na stránku řešení.

### <a name="unlink-workspace-from-automation-account"></a>Odpojení pracovního prostoru od účtu Automation

Existují dvě možnosti, jak odpojit Log Analytics pracovní prostor od svého účtu Automation. Tento postup můžete provést z účtu Automation nebo z připojeného pracovního prostoru.

Pokud chcete odpojit svůj účet Automation, proveďte následující kroky.

1. V Azure Portal vyberte **účty Automation**.

2. Otevřete účet Automation a v části **související prostředky** na levé straně vyberte **propojený pracovní prostor** .

3. Na stránce zrušit **propojení pracovního prostoru** vyberte zrušit **propojení pracovního prostoru** a reagovat na výzvy.

   ![Zrušit propojení stránky pracovního prostoru](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Při pokusu o odpojení pracovního prostoru Log Analytics můžete sledovat průběh v nabídce **oznámení** .

Chcete-li zrušit propojení s pracovním prostorem, proveďte následující kroky.

1. V Azure Portal vyberte **pracovní prostory Log Analytics**.

2. V pracovním prostoru vyberte **účet Automation** v části **související prostředky**.

3. Na stránce účet Automation vyberte zrušit **propojení účtu** a odpovězte na výzvy.

Při pokusu o odpojení účtu Automation můžete sledovat průběh v nabídce **oznámení** .

### <a name="delete-automation-account"></a>Odstranit účet Automation

1. V Azure Portal vyberte **účty Automation**.

2. Otevřete účet Automation a v nabídce vyberte **Odstranit** .

I když jsou informace ověřené a účet je odstraněný, můžete sledovat průběh v části **oznámení**, kterou zvolíte v nabídce.

## <a name="delete-the-feature"></a>Odstranění funkce

Pokud chcete Start/Stop VMs during off-hours odstranit z účtu Automation, proveďte následující kroky. Účet služby Automation a Log Analytics pracovní prostor se v rámci tohoto procesu neodstraní. Pokud nechcete zachovat Log Analytics pracovní prostor, musíte ho ručně odstranit. Další informace o odstranění pracovního prostoru najdete v tématu věnovaném [odstranění a obnovení Azure Log Analytics pracovního prostoru](../azure-monitor/logs/delete-workspace.md).

1. Přejděte do svého účtu Automation a v části **související prostředky** vyberte **propojený pracovní prostor** .

2. Vyberte **Přejít k pracovnímu prostoru**.

3. V části **Obecné** klikněte na **řešení** .

4. Na stránce řešení vyberte **Start-Stop-VM [pracovní prostor]**.

5. Na stránce **VMManagementSolution [pracovní prostor]** v nabídce vyberte **Odstranit** .

    ![Odstranit funkci správy virtuálních počítačů](media/automation-solution-vm-management/vm-management-solution-delete.png)

6. V okně Odstranit řešení potvrďte, že chcete tuto funkci odstranit.

7. I když jsou informace ověřené a funkce se odstraní, můžete sledovat průběh v části **oznámení**, kterou zvolíte v nabídce. Po odebrání se vrátíte na stránku řešení.

8. Pokud nechcete, aby byly [prostředky](automation-solution-vm-management.md#components) vytvořené funkcí nebo později (například proměnné, plány atd.), je nutné je ručně odstranit z účtu.

## <a name="next-steps"></a>Další kroky

Pokud chcete tuto funkci znovu povolit, přečtěte si téma [Povolení spuštění/zastavení v době mimo špičku](automation-solution-vm-management-enable.md).