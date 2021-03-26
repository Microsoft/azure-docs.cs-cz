---
title: Řešení potíží s Azure Automation Start/Stop VMs during off-hours
description: V tomto článku se dozvíte, jak řešit problémy vznikající při použití funkce Start/Stop VMs during off-hours.
services: automation
ms.subservice: process-automation
ms.date: 04/04/2019
ms.topic: troubleshooting
ms.openlocfilehash: f6d2696a68643f87de0fcaf2e723da9365d9953e
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953846"
---
# <a name="troubleshoot-startstop-vms-during-off-hours-issues"></a>Řešení potíží s Start/Stop VMs during off-hours

Tento článek poskytuje informace o řešení potíží a řešení problémů, které se vyskytnou při nasazení funkce Azure Automation Start/Stop VMs during off-hours na vašich virtuálních počítačích. 

## <a name="scenario-startstop-vms-during-off-hours-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Scénář: Start/Stop VMs during off-hours se nepodařilo správně nasadit

### <a name="issue"></a>Problém

Při nasazení [Start/Stop VMS during off-hours](../automation-solution-vm-management.md)se zobrazí jedna z následujících chyb:

```error
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```error
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]".
```

```error
The subscription is not registered to use namespace 'Microsoft.OperationsManagement'.
```

```error
The subscription is not registered to use namespace 'Microsoft.Insights'.
```

```error
The scope '/subscriptions/000000000000-0000-0000-0000-00000000/resourcegroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView' cannot perform write operation because following scope(s) are locked: '/subscriptions/000000000000-0000-0000-0000-00000000/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView'. Please remove the lock and try again
```

```error
A parameter cannot be found that matches parameter name 'TagName'
```

```error
Start-AzureRmVm : Run Login-AzureRmAccount to login
```

### <a name="cause"></a>Příčina

Nasazení mohou selhat z jednoho z následujících důvodů:

- V vybrané oblasti již existuje účet Automation se stejným názvem.
- Zásada zakáže nasazení Start/Stop VMs during off-hours.
- `Microsoft.OperationsManagement` `Microsoft.Insights` Typ prostředku, nebo `Microsoft.Automation` není zaregistrován.
- Váš pracovní prostor Log Analytics je uzamčený.
- Máte zastaralou verzi modulů AzureRM nebo funkci Start/Stop VMs during off-hours.

### <a name="resolution"></a>Řešení

Podívejte se na následující opravy a vyhledejte možná řešení:

* Účty Automation musí být jedinečné v rámci oblasti Azure, a to i v případě, že jsou v různých skupinách prostředků. Projděte si existující účty služby Automation v cílové oblasti.
* Stávající zásady zabrání prostředku, který je vyžadován pro nasazení Start/Stop VMs during off-hours. V Azure Portal přejít na přiřazení zásad a ověřte, jestli máte přiřazení zásady, které nepovoluje nasazení tohoto prostředku. Další informace najdete v tématu [RequestDisallowedByPolicy chyba](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
* Pokud chcete nasadit Start/Stop VMs during off-hours, musí být vaše předplatné zaregistrované v následujících oborech názvů prostředků Azure:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Další informace o chybách při registraci zprostředkovatelů najdete v tématu [řešení chyb pro registraci poskytovatele prostředků](../../azure-resource-manager/templates/error-register-resource-provider.md).
* Pokud máte zámek v pracovním prostoru Log Analytics, v Azure Portal vyberte svůj pracovní prostor a odeberte všechny zámky v prostředku.
* Pokud tato řešení problém nevyřeší, postupujte podle pokynů v části [aktualizace funkce](../automation-solution-vm-management.md#update-the-feature) pro opětovné nasazení Start/Stop VMS during off-hours.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Scénář: Nepodařilo se spustit nebo zastavit všechny virtuální počítače.

### <a name="issue"></a>Problém

Nakonfigurovali jste Start/Stop VMs during off-hours, ale nespustí ani nezastaví všechny virtuální počítače.

### <a name="cause"></a>Příčina

Tato chyba může být způsobena jedním z následujících důvodů:

- Plán není správně nakonfigurovaný.
- Účet Spustit jako možná není správně nakonfigurovaný.
- Sada Runbook mohla vést k chybám.
- Pravděpodobně byly vyloučeny virtuální počítače.

### <a name="resolution"></a>Řešení

V následujícím seznamu najdete možná řešení:

* Ověřte, že jste správně nakonfigurovali plán pro Start/Stop VMs during off-hours. Informace o tom, jak nakonfigurovat plán, najdete v tématu [plány](../shared-resources/schedules.md).

* Zkontrolujte [streamy úlohy](../automation-runbook-execution.md#job-statuses) a vyhledejte případné chyby. Vyhledejte úlohy z jedné z následujících sad Runbook:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Ověřte, že váš [účet Spustit jako](../automation-security-overview.md#run-as-accounts) má správná oprávnění k virtuálním počítačům, které se pokoušíte spustit nebo zastavit. Informace o tom, jak kontrolovat oprávnění k prostředku, najdete v tématu [rychlý Start: Zobrazení rolí přiřazených uživateli pomocí Azure Portal](../../role-based-access-control/check-access.md). Bude nutné zadat ID aplikace pro instanční objekt používaný účtem spustit jako. Tuto hodnotu můžete načíst tak, že v Azure Portal kliknete na účet Automation. V části **Nastavení účtu** vyberte **účty Spustit jako** a vyberte příslušný účet Spustit jako.

* Pokud jsou virtuální počítače explicitně vyloučené, nemusí se spustit nebo zastavit. Vyloučené virtuální počítače se nastavují v `External_ExcludeVMNames` proměnné v účtu Automation, na který je tato funkce nasazená. Následující příklad ukazuje, jak můžete zadat dotaz na tuto hodnotu pomocí PowerShellu.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Scénář: některé z mých virtuálních počítačů se nepodařilo spustit nebo zastavit.

### <a name="issue"></a>Problém

Nakonfigurovali jste Start/Stop VMs during off-hours, ale nespustí nebo zastaví některé virtuální počítače nakonfigurované.

### <a name="cause"></a>Příčina

Tato chyba může být způsobena jedním z následujících důvodů:

- Ve scénáři sekvence může být značka chybějící nebo nesprávná.
- Virtuální počítač může být vyloučený.
- Účet Spustit jako možná nemá dostatečná oprávnění k virtuálnímu počítači.
- Virtuální počítač může mít problém, který ho zastavil při spuštění nebo zastavení.

### <a name="resolution"></a>Řešení

V následujícím seznamu najdete možná řešení:

* Když použijete [scénář sekvence](../automation-solution-vm-management.md) Start/Stop VMS during off-hours, musíte se ujistit, že každý virtuální počítač, který chcete spustit nebo zastavit, má správnou značku. Ujistěte se, že virtuální počítače, které chcete spustit, mají `sequencestart` značku a virtuální počítače, které chcete zastavit, mají `sequencestop` značku. Obě značky vyžadují kladnou celočíselnou hodnotu. Dotaz podobný následujícímu příkladu můžete použít k vyhledání všech virtuálních počítačů, které mají značky a jejich hodnoty.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Pokud jsou virtuální počítače explicitně vyloučené, nemusí se spustit nebo zastavit. Vyloučené virtuální počítače se nastavují v `External_ExcludeVMNames` proměnné v účtu Automation, na který je tato funkce nasazená. Následující příklad ukazuje, jak můžete zadat dotaz na tuto hodnotu pomocí PowerShellu.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Aby bylo možné spouštět a zastavovat virtuální počítače, musí mít účet Spustit jako pro účet Automation příslušná oprávnění k tomuto virtuálnímu počítači. Informace o tom, jak kontrolovat oprávnění k prostředku, najdete v tématu [rychlý Start: Zobrazení rolí přiřazených uživateli pomocí Azure Portal](../../role-based-access-control/check-access.md). Bude nutné zadat ID aplikace pro instanční objekt používaný účtem spustit jako. Tuto hodnotu můžete načíst tak, že v Azure Portal kliknete na účet Automation. V části **Nastavení účtu** vyberte **účty Spustit jako** a vyberte příslušný účet Spustit jako.
* Pokud má virtuální počítač potíže se spouštěním nebo navrácením, může dojít k potížím na samotném virtuálním počítači. Příkladem je aktualizace, která se používá, když se virtuální počítač snaží vypnout, služba, která zamrzne a další. Přejděte na prostředek virtuálního počítače a zkontrolujte **protokoly aktivit** , abyste viděli, jestli jsou v protokolech nějaké chyby. Můžete se taky pokusit přihlásit k virtuálnímu počítači a zjistit, jestli v protokolech událostí nejsou nějaké chyby. Další informace o řešení potíží s vaším VIRTUÁLNÍm počítačem najdete v tématu [řešení potíží s virtuálními počítači Azure](/troubleshoot/azure/virtual-machines/welcome-virtual-machines).
* Zkontrolujte [streamy úlohy](../automation-runbook-execution.md#job-statuses) a vyhledejte případné chyby. Na portálu přejdete do svého účtu Automation a v části **Automatizace procesu** vyberete **úlohy** .

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Scénář: můj vlastní Runbook nedokáže spustit nebo zastavit moje virtuální počítače

### <a name="issue"></a>Problém

Vytvořili jste vlastní Runbook nebo jste si ho stáhli z Galerie prostředí PowerShell a nefunguje správně.

### <a name="cause"></a>Příčina

Může se jednat o mnoho příčin selhání. V Azure Portal na svém účtu Automation a v části **Automatizace procesu** vyberte **úlohy** . Na stránce **úlohy** vyhledejte úlohy z Runbooku a zobrazte případné selhání úloh.

### <a name="resolution"></a>Řešení

Doporučený postup:

* Pomocí [Start/Stop VMS during off-hours](../automation-solution-vm-management.md) zahajte a zastavte virtuální počítače v Azure Automation. 
* Uvědomte si, že Microsoft nepodporuje vlastní Runbooky. Při [řešení problémů s runbookm](runbooks.md)můžete najít řešení pro vlastní sadu Runbook. Zkontrolujte [streamy úlohy](../automation-runbook-execution.md#job-statuses) a vyhledejte případné chyby. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Scénář: virtuální počítače se nespustí ani nezastaví ve správném pořadí.

### <a name="issue"></a>Problém

Virtuální počítače, které jste povolili pro funkci, se nespustí ani nezastaví ve správném pořadí.

### <a name="cause"></a>Příčina

K tomuto problému dochází v důsledku nesprávného označování na virtuálních počítačích.

### <a name="resolution"></a>Řešení

Pomocí těchto kroků ověřte, zda je funkce správně povolena:

1. Ujistěte se, že všechny virtuální počítače, které mají být spuštěny nebo zastaveny `sequencestart` `sequencestop` , mají značku nebo, v závislosti na vaší situaci. Tyto značky vyžadují jako hodnotu kladné celé číslo. Virtuální počítače jsou zpracovávány vzestupném pořadím na základě této hodnoty.
1. Ujistěte se, že skupiny prostředků pro virtuální počítače, které se mají spustit nebo zastavit, jsou v `External_Start_ResourceGroupNames` `External_Stop_ResourceGroupNames` proměnných nebo, podle toho, jakou situaci máte.
1. Otestujte provedené změny spuštěním sady **SequencedStartStop_Parent** Runbook s `WHATIF` parametrem nastaveným na hodnotu true, abyste zobrazili náhled změn.

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Scénář: Start/Stop VMs during off-hours úloha se nezdařila s chybou 403 zakázáno

### <a name="issue"></a>Problém

Vyhledáte úlohy, které selhaly s `403 forbidden` chybou pro start/stop VMS during off-hours Runbooky.

### <a name="cause"></a>Příčina

Tento problém může být způsoben nesprávně konfigurovaným nebo vydaným účtem spustit jako s vypršelou platností. Důvodem může být také nedostatečná oprávnění k prostředkům virtuálních počítačů pomocí účtu Spustit jako.

### <a name="resolution"></a>Řešení

Pokud chcete ověřit, že je váš účet Spustit jako správně nakonfigurovaný, Azure Portal v části **Nastavení účtu** vyberte účet Automation a vyberte **účty Spustit jako** . Pokud je účet Spustit jako nesprávně nakonfigurovaný nebo vypršela jeho platnost, stav zobrazuje podmínku.

Pokud je váš účet Spustit jako špatně nakonfigurovaný, odstraňte a znovu vytvořte svůj účet Spustit jako. Další informace najdete v tématu [Azure Automation účty Spustit jako](../automation-security-overview.md#run-as-accounts).

Pokud vypršela platnost certifikátu pro svůj účet Spustit jako, obnovte certifikát pomocí postupu v části [obnovení certifikátu podepsaného svým držitelem](../manage-runas-account.md#cert-renewal) .

Pokud chybí oprávnění, přečtěte si téma [rychlý Start: Zobrazení rolí přiřazených uživateli pomocí Azure Portal](../../role-based-access-control/check-access.md). Je nutné zadat ID aplikace pro instanční objekt používaný účtem spustit jako. Tuto hodnotu můžete načíst tak, že v Azure Portal kliknete na účet Automation. V části **Nastavení účtu** vyberte **účty Spustit jako** a vyberte příslušný účet Spustit jako.

## <a name="scenario-my-problem-isnt-listed-here"></a><a name="other"></a>Scénář: můj problém tady není uvedený.

### <a name="issue"></a>Problém

Když použijete Start/Stop VMs during off-hours, která není na této stránce uvedená, dojde k problému nebo neočekávanému výsledku.

### <a name="cause"></a>Příčina

V mnoha případech může být chyba způsobena použitím staré a zastaralé verze funkce.

> [!NOTE]
> Funkce Start/Stop VMs during off-hours byla testována s moduly Azure, které jsou importovány do účtu Automation při nasazení funkce na virtuální počítače. Tato funkce v tuto chvíli nefunguje s novějšími verzemi modulu Azure. Toto omezení ovlivňuje pouze účet Automation, který používáte ke spuštění Start/Stop VMs during off-hours. V dalších účtech Automation můžete dál používat novější verze modulu Azure, jak je popsáno v tématu [aktualizace Azure PowerShell moduly](../automation-update-azure-modules.md).

### <a name="resolution"></a>Řešení

Pokud chcete vyřešit mnoho chyb, odeberte [Start/Stop VMS during off-hours a aktualizujte](../automation-solution-vm-management.md#update-the-feature)je. Můžete také zkontrolovat [streamy úloh](../automation-runbook-execution.md#job-statuses) a vyhledat případné chyby. 

## <a name="next-steps"></a>Další kroky

Pokud tady nevidíte svůj problém nebo nemůžete problém vyřešit, zkuste další podporu vyzkoušet u některého z následujících kanálů:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) oficiálního Microsoft Azure účtu pro zlepšení prostředí pro zákazníky. Podpora Azure spojuje komunitu Azure s odpověďmi, podporou a odborníky.
* Zasouborové incidenty podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/)a vyberte **získat podporu**.