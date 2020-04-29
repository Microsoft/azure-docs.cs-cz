---
title: Řešení potíží s řešením spuštění/zastavení virtuálních počítačů v době mimo špičku
description: Tento článek poskytuje informace o řešení potíží s řešením spuštění/zastavení virtuálního počítače.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 003c2c5a2c09957e7a3a4ac0a26b87a9ac43dace
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679153"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Řešení potíží s řešením spuštění/zastavení virtuálních počítačů v době mimo špičku

Tento článek poskytuje informace o řešení problémů, které vznikají při práci s virtuálními počítači spustit/zastavit během nepracovních řešení.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci nástroje AZ Module Hybrid Runbook Worker najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pro váš účet Automation můžete aktualizovat moduly na nejnovější verzi pomocí [postupu aktualizace modulů Azure PowerShell v Azure Automation](../automation-update-azure-modules.md).

## <a name="scenario-the-startstop-vms-during-off-hours-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Scénář: řešení spouštění a zastavování virtuálních počítačů v době mimo špičku se nepodařilo správně nasadit.

### <a name="issue"></a>Problém

Při nasazení [virtuálních počítačů spustit/zastavit v době mimo špičku](../automation-solution-vm-management.md)se zobrazí jedna z následujících chyb:

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

1. V vybrané oblasti již existuje účet Automation se stejným názvem.
2. Zásada nepovoluje nasazení virtuálních počítačů spustit/zastavit během nepracovních řešení.
3. Typ `Microsoft.OperationsManagement`prostředku `Microsoft.Insights`, nebo `Microsoft.Automation` není zaregistrován.
4. Váš pracovní prostor Log Analytics je uzamčený.
5. Máte zastaralou verzi modulů AzureRM nebo spustit nebo zastavit virtuální počítače v době mimo špičku.

### <a name="resolution"></a>Řešení

V následujících opravách si můžete přečíst možná řešení vašeho problému:

* Účty Automation musí být jedinečné v rámci oblasti Azure, a to i v případě, že jsou v různých skupinách prostředků. Projděte si existující účty služby Automation v cílové oblasti.
* Stávající zásady zabrání prostředku, který je vyžadován pro virtuální počítače spustit/zastavit během nepracovních řešení, které se mají nasadit. V Azure Portal přejít na přiřazení zásad a ověřte, jestli máte přiřazení zásady, které nepovoluje nasazení tohoto prostředku. Další informace najdete v tématu [RequestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
* Pokud chcete nasadit řešení pro spouštění a zastavování virtuálních počítačů, musí být vaše předplatné zaregistrované v následujících oborech názvů prostředků Azure:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Další informace o chybách při registraci zprostředkovatelů najdete v tématu [řešení chyb pro registraci poskytovatele prostředků](../../azure-resource-manager/templates/error-register-resource-provider.md) .
* Pokud máte zámek v pracovním prostoru Log Analytics, v Azure Portal vyberte svůj pracovní prostor a odeberte všechny zámky v prostředku.
* Pokud výše uvedená řešení problém nevyřeší, postupujte podle pokynů v části [aktualizace řešení](../automation-solution-vm-management.md#update-the-solution) pro opětovné nasazení řešení spustit/zastavit.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Scénář: Nepodařilo se spustit nebo zastavit všechny virtuální počítače.

### <a name="issue"></a>Problém

Nakonfigurovali jste virtuální počítače spustit/zastavit během nepracovních řešení, ale nespustí nebo zastaví všechny virtuální počítače.

### <a name="cause"></a>Příčina

Tato chyba může být způsobena jedním z následujících důvodů:

1. Plán není správně nakonfigurovaný.
2. Účet Spustit jako možná není správně nakonfigurovaný.
3. Sada Runbook mohla vést k chybám.
4. Pravděpodobně byly vyloučeny virtuální počítače.

### <a name="resolution"></a>Řešení

Podívejte se na následující seznam a vyhledejte možná řešení vašeho problému:

* Ověřte, že jste správně nakonfigurovali plán pro virtuální počítače spustit/zastavit v době mimo špičku. Informace o tom, jak nakonfigurovat plán, najdete v článku [plány](../automation-schedules.md) .

* Zkontrolujte [streamy úlohy](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) a vyhledejte případné chyby. Vyhledejte úlohy z jedné z následujících sad Runbook:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Ověřte, že váš [účet Spustit jako](../manage-runas-account.md) má správná oprávnění k virtuálním počítačům, které se pokoušíte spustit nebo zastavit. Informace o tom, jak kontrolovat oprávnění k prostředku, najdete v tématu [rychlý Start: Zobrazení rolí přiřazených uživateli pomocí Azure Portal](../../role-based-access-control/check-access.md). Bude nutné zadat ID aplikace pro instanční objekt používaný účtem spustit jako. Tuto hodnotu můžete načíst tak, že přejdete do účtu Automation v Azure Portal, vyberete **účty Spustit jako** v části **Nastavení účtu**a kliknete na příslušný účet Spustit jako.

* Pokud jsou virtuální počítače explicitně vyloučené, nemusíte je spustit ani zastavit. Vyloučené virtuální počítače se nastavují v `External_ExcludeVMNames` proměnné v účtu Automation, na který se řešení nasadí. Následující příklad ukazuje, jak můžete zadat dotaz na tuto hodnotu pomocí PowerShellu.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Scénář: některé z mých virtuálních počítačů se nepodařilo spustit nebo zastavit.

### <a name="issue"></a>Problém

Nakonfigurovali jste virtuální počítače spustit/zastavit v době mimo špičku, ale nespustí se nebo zastavují některé virtuální počítače nakonfigurované.

### <a name="cause"></a>Příčina

Tato chyba může být způsobena jedním z následujících důvodů:

1. Ve scénáři sekvence může být značka chybějící nebo nesprávná.
2. Virtuální počítač může být vyloučený.
3. Účet Spustit jako možná nemá dostatečná oprávnění k virtuálnímu počítači.
4. Virtuální počítač může mít problém, který ho zastavil při spuštění nebo zastavení.

### <a name="resolution"></a>Řešení

Projděte si následující seznam, kde můžete najít možná řešení vašeho problému nebo umístit tyto problémy:

* Pokud používáte [scénář sekvence](../automation-solution-vm-management.md) virtuálních počítačů spustit/zastavit v době mimo špičku, musíte se ujistit, že každý virtuální počítač, který chcete spustit nebo zastavit, má správnou značku. Ujistěte se, že virtuální počítače, které chcete spustit, `sequencestart` mají značku a virtuální počítače, které chcete zastavit, `sequencestop` mají značku. Obě značky vyžadují kladnou celočíselnou hodnotu. Dotaz podobný následujícímu příkladu můžete použít k vyhledání všech virtuálních počítačů, které mají značky a jejich hodnoty.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Pokud jsou virtuální počítače explicitně vyloučené, nemusí se spustit nebo zastavit. Vyloučené virtuální počítače se nastavují v `External_ExcludeVMNames` proměnné v účtu Automation, na který se řešení nasadí. Následující příklad ukazuje, jak můžete zadat dotaz na tuto hodnotu pomocí PowerShellu.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Aby bylo možné spouštět a zastavovat virtuální počítače, musí mít účet Spustit jako pro účet Automation příslušná oprávnění k tomuto virtuálnímu počítači. Informace o tom, jak kontrolovat oprávnění k prostředku, najdete v tématu [rychlý Start: Zobrazení rolí přiřazených uživateli pomocí Azure Portal](../../role-based-access-control/check-access.md). Bude nutné zadat ID aplikace pro instanční objekt používaný účtem spustit jako. Tuto hodnotu můžete načíst tak, že přejdete do účtu Automation v Azure Portal, vyberete **účty Spustit jako** v části **Nastavení účtu** a kliknete na příslušný účet Spustit jako.

* Pokud má virtuální počítač potíže se spouštěním nebo navrácením, může dojít k potížím na samotném virtuálním počítači. Například se používá aktualizace, když se virtuální počítač pokouší vypnout, služba přestane reagovat a další. Přejděte k prostředku virtuálního počítače a zkontrolujte **protokoly aktivit** , abyste viděli, jestli jsou v protokolech nějaké chyby. Můžete se taky pokusit o přihlášení k virtuálnímu počítači, abyste viděli, jestli v protokolech událostí nejsou nějaké chyby. Další informace o řešení potíží s vaším VIRTUÁLNÍm počítačem najdete v tématu [řešení potíží s virtuálními počítači Azure](../../virtual-machines/troubleshooting/index.yml) .

* Zkontrolujte [streamy úlohy](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) a vyhledejte případné chyby. Na portálu přejdete do svého účtu Automation a v části **Automatizace procesu**vyberete **úlohy** .

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Scénář: můj vlastní Runbook nedokáže spustit nebo zastavit moje virtuální počítače

### <a name="issue"></a>Problém

Vytvořili jste vlastní Runbook nebo jste si ho stáhli ze Galerie prostředí PowerShell a nefunguje správně.

### <a name="cause"></a>Příčina

Může se jednat o mnoho příčin selhání. V Azure Portal klikněte na účet Automation a v části **Automatizace procesu**vyberte **úlohy** . Na stránce úlohy vyhledejte úlohy z Runbooku a zobrazte případné selhání úloh.

### <a name="resolution"></a>Řešení

Doporučuje se:

* Pomocí [řešení spustit nebo zastavit virtuální počítače v době mimo špičku](../automation-solution-vm-management.md) můžete spouštět a zastavovat virtuální počítače v Azure Automation. Toto řešení je vytvořené Microsoftem. 

* Uvědomte si, že Microsoft nepodporuje vlastní Runbooky. Můžete najít řešení pro vlastní Runbook z [řešení potíží](runbooks.md)se sadou Runbook. Zkontrolujte [streamy úlohy](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) a vyhledejte případné chyby. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Scénář: virtuální počítače se nespustí ani nezastaví ve správném pořadí.

### <a name="issue"></a>Problém

Virtuální počítače, které jste nakonfigurovali v řešení, se nespustí ani neukončí ve správném pořadí.

### <a name="cause"></a>Příčina

K tomuto problému dochází v důsledku nesprávného označování na virtuálních počítačích.

### <a name="resolution"></a>Řešení

Proveďte následující kroky, aby bylo zajištěno, že je řešení správně nakonfigurováno.

1. Zajistěte, aby všechny virtuální počítače byly `sequencestart` `sequencestop` spuštěny nebo zastaveny, v závislosti na vaší situaci. Tyto značky vyžadují jako hodnotu kladné celé číslo. Virtuální počítače jsou zpracovávány vzestupném pořadím na základě této hodnoty.
2. Ujistěte se, že skupiny prostředků pro virtuální počítače, které se mají spustit nebo zastavit `External_Start_ResourceGroupNames` , `External_Stop_ResourceGroupNames` jsou v proměnných nebo, podle toho, jakou situaci máte.
3. Otestujte provedené změny spuštěním `SequencedStartStop_Parent` sady Runbook s `WHATIF` parametrem nastaveným na hodnotu true a zobrazte náhled změn.
4. Další informace o použití řešení ke spouštění a zastavování virtuálních počítačů v sekvenci najdete v tématu [spuštění a zastavení virtuálních počítačů v sekvenci](../automation-solution-vm-management.md).

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Scénář: úloha spuštění/zastavení virtuálních počítačů v době mimo špičku se nezdařila s chybou 403 zakázáno

### <a name="issue"></a>Problém

Vyhledáte úlohy, které selhaly s `403 forbidden` chybou pro virtuální počítače spustit/zastavit v době mimo špičku řešení Runbook.

### <a name="cause"></a>Příčina

Tento problém může být způsoben nesprávně konfigurovaným nebo vydaným účtem spustit jako s vypršelou platností. Důvodem může být také nedostatečná oprávnění k prostředkům virtuálních počítačů pomocí účtu Spustit jako.

### <a name="resolution"></a>Řešení

Pokud chcete ověřit, že je váš účet Spustit jako správně nakonfigurovaný, Azure Portal v části **Nastavení účtu**vyberte účet Automation a vyberte **účty Spustit jako** . Pokud je účet Spustit jako nesprávně nakonfigurovaný nebo vypršela jeho platnost, stav zobrazuje podmínku.

Pokud je váš účet Spustit jako špatně nakonfigurovaný, měli byste účet Spustit jako odstranit a znovu vytvořit. Viz [Správa účtů spustit jako Azure Automation](../manage-runas-account.md).

Pokud vypršela platnost certifikátu pro účet Spustit jako, přečtěte si postup obnovení certifikátu v tématu Postup [obnovení certifikátu podepsaného svým držitelem](../manage-runas-account.md#cert-renewal) .

Pokud chybí oprávnění, přečtěte si téma [rychlý Start: Zobrazení rolí přiřazených uživateli pomocí Azure Portal](../../role-based-access-control/check-access.md). Je nutné zadat ID aplikace pro instanční objekt používaný účtem spustit jako. Tuto hodnotu můžete načíst tak, že přejdete do účtu Automation v Azure Portal, vyberete **účty Spustit jako** v části **Nastavení účtu**a kliknete na příslušný účet Spustit jako.

## <a name="scenario-my-problem-isnt-listed-above"></a><a name="other"></a>Scénář: můj problém není uvedený výše

### <a name="issue"></a>Problém

Při používání virtuálních počítačů spustit/zastavit v době nečinnosti, která není uvedená na této stránce, dochází k problému nebo neočekávanému výsledku.

### <a name="cause"></a>Příčina

V mnoha případech může být chyba způsobena použitím staré a zastaralé verze řešení.

> [!NOTE]
> Řešení spouštění a zastavování virtuálních počítačů v době mimo špičku bylo testováno pomocí modulů Azure, které jsou importovány do účtu Automation při nasazení řešení. Řešení momentálně nefunguje s novějšími verzemi modulu Azure. To má vliv jenom na účet Automation, který používáte ke spuštění virtuálních počítačů spustit/zastavit během nepracovních řešení. V dalších účtech Automation můžete dál používat novější verze modulu Azure, jak je popsáno v tématu [Postup aktualizace Azure PowerShellch modulů v Azure Automation](../automation-update-azure-modules.md)

### <a name="resolution"></a>Řešení

Chcete-li vyřešit mnoho chyb, je doporučeno odebrat a [Aktualizovat virtuální počítače spustit/zastavit během nepracovních řešení](../automation-solution-vm-management.md#update-the-solution). Navíc můžete zkontrolovat [streamy úloh](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) a vyhledat případné chyby. 

## <a name="next-steps"></a>Další kroky

Pokud nevidíte výše uvedený problém nebo nemůžete problém vyřešit, zkuste pro další podporu použít jeden z následujících kanálů:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Připojte se [@AzureSupport](https://twitter.com/azuresupport)k, oficiální Microsoft Azure účet pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.
* Zasouborové incidenty podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.