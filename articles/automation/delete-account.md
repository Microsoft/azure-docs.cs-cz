---
title: Odstranit účet Azure Automation
description: Tento článek popisuje, jak odstranit váš účet Automation v různých scénářích konfigurace.
services: automation
ms.service: automation
ms.subservice: process-automation
ms.date: 04/15/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fe2d99a610be3877b4a347e4bd0dd17df53ba326
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834151"
---
# <a name="how-to-delete-your-azure-automation-account"></a>Jak odstranit účet Azure Automation

Když povolíte účet Azure Automation, který vám usnadní automatizaci IT nebo obchodního procesu, nebo povolíte jeho jiným funkcím, aby podporoval správu provozu počítačů s Azure a mimo Azure, jako je například Update Management, můžete se rozhodnout přerušit používání účtu Automation. Pokud jste povolili funkce, které závisejí na integraci s Azure Monitor pracovním prostorem Log Analytics, je nutné provést další kroky pro dokončení této akce.

Odebrání účtu Automation se dá udělat pomocí jedné z následujících metod založených na podporovaných modelech nasazení:

* Odstraňte skupinu prostředků obsahující účet Automation.
* Odstraňte skupinu prostředků obsahující účet Automation a pracovní prostor propojeného Azure Monitor Log Analytics, pokud:

    * Účet a pracovní prostor jsou vyhrazené pro podporu Update Management, Change Tracking a inventáře nebo Start/Stop VMs during off-hours.
    * Účet je vyhrazený pro zpracování automatizace a integrovaný do pracovního prostoru pro odesílání datových proudů úloh a datových proudů úloh sady Runbook.

* Odpojte pracovní prostor Log Analytics z účtu Automation a odstraňte účet Automation.
* Odstraňte tuto funkci z připojeného pracovního prostoru, odpojte účet z pracovního prostoru a potom účet Automation odstraňte.

V tomto článku se dozvíte, jak úplně odebrat účet Automation prostřednictvím Azure Portal, pomocí Azure PowerShell, rozhraní příkazového řádku Azure nebo REST API.

> [!NOTE]
> Než budete pokračovat, ověřte, že v předplatném, skupině prostředků nebo prostředku nejsou použité žádné [Správce prostředků zámky](../azure-resource-manager/management/lock-resources.md) , které zabrání nechtěnému odstranění nebo změně důležitých prostředků. Pokud jste nasadili Start/Stop VMs during off-hours řešení, nastaví úroveň zámku tak, aby se **CanNotDelete** na několik závislých prostředků v účtu Automation (konkrétně v sadách Runbook a proměnné). Než budete moct účet Automation odstranit, musíte odebrat všechny zámky.

## <a name="delete-the-dedicated-resource-group"></a>Odstranit vyhrazenou skupinu prostředků

Pokud chcete odstranit svůj účet Automation a taky Log Analytics pracovní prostor, který je propojený s účtem, vytvoří se ve stejné skupině prostředků, která je pro tento účet vyhrazená, postupujte podle kroků uvedených v článku [Azure Resource Manager skupiny prostředků a odstraňování prostředků](../azure-resource-manager/management/delete-resource-group.md) .

## <a name="delete-a-standalone-automation-account"></a>Odstranění samostatného účtu Automation

Pokud váš účet Automation není propojený s pracovním prostorem Log Analytics, odstraňte ho pomocí následujících kroků.

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

1. Přihlaste se k Azure na adrese [https://portal.azure.com](https://portal.azure.com) .

2. V Azure Portal přejděte na **účty Automation**.

3. Otevřete účet Automation a v nabídce vyberte **Odstranit** .

I když jsou informace ověřené a účet je odstraněný, můžete sledovat průběh v části **oznámení**, kterou zvolíte v nabídce.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Tento příkaz odebere účet služby Automation bez zobrazení výzvy k ověření.

```powershell
Remove-AzAutomationAccount -Name "automationAccountName" -Force -ResourceGroupName "resourceGroupName"
```

---

## <a name="delete-a-standalone-automation-account-linked-to-workspace"></a>Umožňuje odstranit samostatný účet Automation propojený s pracovním prostorem.

Pokud je váš účet Automation propojený s pracovním prostorem Log Analytics za účelem shromažďování datových proudů úloh a protokolů úloh, pomocí následujícího postupu účet odstraňte.

Existují dvě možnosti, jak odpojit Log Analytics pracovní prostor od svého účtu Automation. Tento postup můžete provést z účtu Automation nebo z připojeného pracovního prostoru.

Pokud chcete odpojit svůj účet Automation, proveďte následující kroky.

1. V Azure Portal přejděte na **účty Automation**.

2. Otevřete účet Automation a v části **související prostředky** na levé straně vyberte **propojený pracovní prostor** .

3. Na stránce zrušit **propojení pracovního prostoru** vyberte zrušit **propojení pracovního prostoru** a odpovězte na výzvy.

   ![Zrušit propojení stránky pracovního prostoru](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Při pokusu o odpojení pracovního prostoru Log Analytics můžete sledovat průběh v nabídce **oznámení** .

Chcete-li zrušit propojení s pracovním prostorem, proveďte následující kroky.

1. V Azure Portal přejděte na **Log Analytics pracovní prostory**.

2. V pracovním prostoru vyberte **účet Automation** v části **související prostředky**.

3. Na stránce účet Automation vyberte zrušit **propojení účtu** a odpovězte na výzvy.

Při pokusu o odpojení účtu Automation můžete sledovat průběh v nabídce **oznámení** .

Po úspěšném odpojení účtu Automation z pracovního prostoru proveďte kroky v části [samostatný účet Automation](#delete-a-standalone-automation-account) a účet odstraňte.

## <a name="delete-a-shared-capability-automation-account"></a>Umožňuje odstranit účet Automation Shared Capability.

Pokud chcete odstranit účet Automation propojený s pracovním prostorem Log Analytics v podpoře Update Management, Change Tracking a inventarizace a Start/Stop VMs during off-hours, proveďte následující kroky.

### <a name="step-1-delete-the-solution-from-the-linked-workspace"></a>Krok 1. Odstranění řešení z propojeného pracovního prostoru

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

1. Přihlaste se k Azure na adrese [https://portal.azure.com](https://portal.azure.com) .

2. Přejděte do svého účtu Automation a v části **související prostředky** vyberte **propojený pracovní prostor** .

3. Vyberte **Přejít k pracovnímu prostoru**.

4. V části **Obecné** klikněte na **řešení** .

5. Na stránce řešení vyberte jednu z následujících funkcí založenou na funkcích nasazených v účtu:

    * V Start/Stop VMs during off-hours vyberte **Start-Stop-VM [název pracovního prostoru]**.
    * V Update Management vyberte **aktualizace (název pracovního prostoru)**.
    * V případě Change Tracking a inventáře vyberte **sledování změn ve (název pracovního prostoru)**.

6. Na stránce **řešení** vyberte z nabídky **Odstranit** . Pokud se do účtu Automation a propojeného pracovního prostoru nasadí více než jedna z výše uvedených funkcí, je nutné před pokračováním vybrat a odstranit každou z nich.

7. I když jsou informace ověřené a funkce se odstraní, můžete sledovat průběh v části **oznámení**, kterou zvolíte v nabídce. Po odebrání se vrátíte na stránku řešení.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

K odebrání nainstalovaného řešení pomocí Azure PowerShell použijte rutinu [Remove-AzMonitorLogAnalyticsSolution](/powershell/module/az.monitoringsolutions/remove-azmonitorloganalyticssolution) .

```powershell
Remove-AzMonitorLogAnalyticsSolution -ResourceGroupName "resourceGroupName" -Name "solutionName"
```

---

### <a name="step-2-unlink-workspace-from-automation-account"></a>Krok 2. Odpojení pracovního prostoru od účtu Automation

Existují dvě možnosti, jak odpojit Log Analytics pracovní prostor od svého účtu Automation. Tento postup můžete provést z účtu Automation nebo z připojeného pracovního prostoru.

Pokud chcete odpojit svůj účet Automation, proveďte následující kroky.

1. V Azure Portal přejděte na **účty Automation**.

2. Otevřete účet Automation a v části **související prostředky** na levé straně vyberte **propojený pracovní prostor** .

3. Na stránce zrušit **propojení pracovního prostoru** vyberte zrušit **propojení pracovního prostoru** a odpovězte na výzvy.

   ![Zrušit propojení stránky pracovního prostoru](media/automation-solution-vm-management-remove/automation-unlink-workspace-blade.png)

    Při pokusu o odpojení pracovního prostoru Log Analytics můžete sledovat průběh v nabídce **oznámení** .

Chcete-li zrušit propojení s pracovním prostorem, proveďte následující kroky.

1. V Azure Portal přejděte na **Log Analytics pracovní prostory**.

2. V pracovním prostoru vyberte **účet Automation** v části **související prostředky**.

3. Na stránce účet Automation vyberte zrušit **propojení účtu** a odpovězte na výzvy.

Při pokusu o odpojení účtu Automation můžete sledovat průběh v nabídce **oznámení** .

### <a name="step-3-delete-automation-account"></a>Krok 3. Odstranit účet Automation

Po úspěšném odpojení účtu Automation z pracovního prostoru proveďte kroky v části [samostatný účet Automation](#delete-a-standalone-automation-account) a účet odstraňte.

## <a name="next-steps"></a>Další kroky

Pokud chcete vytvořit účet Automation z Azure Portal, přečtěte si téma [vytvoření samostatného účtu Azure Automation](automation-create-standalone-account.md). Pokud dáváte přednost vytvoření účtu pomocí šablony, přečtěte si téma [Vytvoření účtu Automation pomocí šablony Azure Resource Manager](quickstart-create-automation-account-template.md).
