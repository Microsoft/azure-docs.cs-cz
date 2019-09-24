---
title: Řešení potíží při spouštění a zastavování virtuálních počítačů pomocí Azure Automation
description: Tento článek poskytuje informace o řešení potíží při spouštění a zastavování virtuálních počítačů v Azure Automation
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fe4317c193e8aa6c6723556ef36d6111df6f51cd
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240850"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Řešení potíží s řešením spuštění/zastavení virtuálních počítačů v době mimo špičku

## <a name="deployment-failure"></a>Případě Řešení pro spuštění/zastavení virtuálního počítače se nepodařilo správně nasadit.

### <a name="issue"></a>Problém

Při nasazení [virtuálních počítačů spustit/zastavit v době mimo špičku](../automation-solution-vm-management.md)se zobrazí jedna z následujících chyb:

```error
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```error
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]”.
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
2. Je zavedena zásada, která zakáže nasazení řešení spustit nebo zastavit virtuální počítače.
3. Typy prostředků `Microsoft.Insights` ,nebo`Microsoft.Automation`nejsouregistrovány. `Microsoft.OperationsManagement`
4. Váš pracovní prostor Log Analytics má na svém počítači zámek.
5. Máte zastaralou verzi modulů AzureRM nebo řešení spustit/zastavit.

### <a name="resolution"></a>Řešení

Projděte si následující seznam, kde můžete najít možná řešení vašeho problému nebo umístit tyto problémy:

1. Účty Automation musí být jedinečné v rámci oblasti Azure, a to i v případě, že jsou v různých skupinách prostředků. Projděte si existující účty služby Automation v cílové oblasti.
2. Existující zásady zabraňují nasazení řešení pro spuštění nebo zastavení virtuálního počítače. V Azure Portal přejít na přiřazení zásad a ověřte, jestli máte přiřazení zásady, které nepovoluje nasazení tohoto prostředku. Další informace najdete v tématu [RequestDisallowedByPolicy](../../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md).
3. Aby bylo možné nasadit řešení pro spuštění/zastavení virtuálního počítače, musí být vaše předplatné zaregistrované v následujících oborech názvů prostředků Azure:
    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Další informace o chybách při registraci zprostředkovatelů najdete v tématu [řešení chyb pro registraci poskytovatele prostředků](../../azure-resource-manager/resource-manager-register-provider-errors.md) .
4. Pokud máte zámek v pracovním prostoru Log Analytics, v Azure Portal vyberte svůj pracovní prostor a odeberte všechny zámky v prostředku.
5. Pokud výše uvedená řešení problém nevyřeší, postupujte podle pokynů v části [aktualizace řešení](../automation-solution-vm-management.md#update-the-solution) pro opětovné nasazení řešení spustit/zastavit.

## <a name="all-vms-fail-to-startstop"></a>Případě Nepodařilo se spustit nebo zastavit všechny virtuální počítače.

### <a name="issue"></a>Problém

Nakonfigurovali jste řešení spuštění/zastavení virtuálního počítače, ale nespustí se nebo zastaví všechny virtuální počítače nakonfigurované.

### <a name="cause"></a>Příčina

Tato chyba může být způsobena jedním z následujících důvodů:

1. Plán není správně nakonfigurovaný.
2. Účet RunAs možná není správně nakonfigurovaný.
3. Sada Runbook možná běžela s chybami
4. Možná byly vyloučeny virtuální počítače.

### <a name="resolution"></a>Řešení

Projděte si následující seznam, kde můžete najít možná řešení vašeho problému nebo umístit tyto problémy:

* Ověřte, že jste správně nakonfigurovali plán pro řešení spuštění/zastavení virtuálního počítače. Informace o tom, jak nakonfigurovat plán, najdete v článku [plány](../automation-schedules.md) .

* Zkontrolujte [streamy úlohy](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) a vyhledejte případné chyby. Na portálu přejdete do svého účtu Automation a v části **Automatizace procesu**vyberete **úlohy** . Na stránce **úlohy** vyhledejte úlohy z jedné z následujících sad Runbook:

  * AutoStop_CreateAlert_Child
  * AutoStop_CreateAlert_Parent
  * AutoStop_Disable
  * AutoStop_VM_Child
  * ScheduledStartStop_Base_Classic
  * ScheduledStartStop_Child_Classic
  * ScheduledStartStop_Child
  * ScheduledStartStop_Parent
  * SequencedStartStop_Parent

* Ověřte, že [účet RunAs](../manage-runas-account.md) má správná oprávnění k virtuálním počítačům, které se pokoušíte spustit nebo zastavit. Informace o tom, jak kontrolovat oprávnění k prostředku, najdete v [tématu rychlý Start: Umožňuje zobrazit role přiřazené uživateli pomocí Azure Portal](../../role-based-access-control/check-access.md). Bude nutné zadat ID aplikace pro instanční objekt používaný účtem spustit jako. Tuto hodnotu můžete načíst tak, že přejdete do účtu Automation v Azure Portal, vyberete **účty Spustit jako** v části **Nastavení účtu** a kliknete na příslušný účet Spustit jako.

* Pokud jsou virtuální počítače explicitně vyloučené, nemusíte je spustit ani zastavit. Vyloučené virtuální počítače jsou uvedené v proměnné **External_ExcludeVMNames** v účtu Automation, do kterého se řešení nasazuje. Následující příklad ukazuje, jak můžete zadat dotaz na tuto hodnotu pomocí PowerShellu.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="some-vms-fail-to-startstop"></a>Případě Některé z mých virtuálních počítačů se nepodařilo spustit nebo zastavit.

### <a name="issue"></a>Problém

Nakonfigurovali jste řešení pro spuštění nebo zastavení virtuálního počítače, ale nespustí se nebo nezastaví některé virtuální počítače nakonfigurované.

### <a name="cause"></a>Příčina

Tato chyba může být způsobena jedním z následujících důvodů:

1. Pokud používáte scénář sekvence, značka může chybět nebo být nesprávná.
2. Virtuální počítač může být vyloučený.
3. Účet RunAs pravděpodobně nemá dostatečná oprávnění k virtuálnímu počítači.
4. Virtuální počítač může mít něco, co ho zastavilo od spuštění nebo zastavení.

### <a name="resolution"></a>Řešení

Projděte si následující seznam, kde můžete najít možná řešení vašeho problému nebo umístit tyto problémy:

* Pokud používáte [scénář sekvence](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags) virtuálního počítače spustit/zastavit v době mimo špičku, musíte se ujistit, že každý virtuální počítač, který chcete spustit nebo zastavit, má správnou značku. Ujistěte se, že virtuální počítače, které chcete spustit, `sequencestart` mají značku a virtuální počítače, které chcete zastavit, `sequencestop` mají značku. Obě značky vyžadují kladnou celočíselnou hodnotu. Dotaz podobný následujícímu příkladu můžete použít k vyhledání všech virtuálních počítačů, které mají značky a jejich hodnoty.

  ```powershell-interactive
  Get-AzureRmResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Pokud jsou virtuální počítače explicitně vyloučené, nemusíte je spustit ani zastavit. Vyloučené virtuální počítače jsou uvedené v proměnné **External_ExcludeVMNames** v účtu Automation, do kterého se řešení nasazuje. Následující příklad ukazuje, jak můžete zadat dotaz na tuto hodnotu pomocí PowerShellu.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Aby bylo možné spouštět a zastavovat virtuální počítače, musí mít účet RunAs pro účet Automation příslušná oprávnění k virtuálnímu počítači. Informace o tom, jak kontrolovat oprávnění k prostředku, najdete v [tématu rychlý Start: Umožňuje zobrazit role přiřazené uživateli pomocí Azure Portal](../../role-based-access-control/check-access.md). Bude nutné zadat ID aplikace pro instanční objekt používaný účtem spustit jako. Tuto hodnotu můžete načíst tak, že přejdete do účtu Automation v Azure Portal, vyberete **účty Spustit jako** v části **Nastavení účtu** a kliknete na příslušný účet Spustit jako.

* Pokud má virtuální počítač potíže se spouštěním nebo navrácením, může to být způsobeno problémem na samotném virtuálním počítači. Některé příklady nebo potenciální problémy jsou při pokusu o vypnutí nebo zablokování služby a dalších potížích aplikovány aktualizace. Přejděte k prostředku virtuálního počítače a zkontrolujte **protokoly aktivit** , abyste viděli, jestli jsou v protokolech nějaké chyby. Můžete se taky pokusit přihlásit k virtuálnímu počítači a zjistit, jestli v protokolech událostí nejsou nějaké chyby. Další informace o řešení potíží s vaším VIRTUÁLNÍm počítačem najdete v tématu [řešení potíží s virtuálními počítači Azure](../../virtual-machines/troubleshooting/index.md) .

* Zkontrolujte [streamy úlohy](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) a vyhledejte případné chyby. Na portálu přejdete do svého účtu Automation a v části **Automatizace procesu**vyberete **úlohy** .

## <a name="custom-runbook"></a>Případě Můj vlastní Runbook nedokáže spustit nebo zastavit moje virtuální počítače

### <a name="issue"></a>Problém

Vytvořili jste vlastní Runbook nebo jste si ho stáhli ze Galerie prostředí PowerShell a nefunguje správně.

### <a name="cause"></a>Příčina

Příčinou selhání může být jedna z mnoha věcí. V Azure Portal klikněte na účet Automation a v části **Automatizace procesu**vyberte **úlohy** . Na stránce **úlohy** vyhledejte úlohy z Runbooku a zobrazte případné selhání úloh.

### <a name="resolution"></a>Řešení

Při spouštění a zastavování virtuálních počítačů v Azure Automation se doporučuje používat [virtuální počítače spustit/zastavit během nepracovních hodin](../automation-solution-vm-management.md) . Toto řešení je vytvořené Microsoftem. Microsoft nepodporuje vlastní Runbooky. Řešení pro vlastní sadu Runbook můžete najít v článku [věnovaném řešení potíží](runbooks.md) se sadou Runbook. Tento článek poskytuje obecné pokyny a řešení potíží pro Runbooky všech typů. Zkontrolujte [streamy úlohy](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) a vyhledejte případné chyby. Na portálu přejdete do svého účtu Automation a v části **Automatizace procesu**vyberete **úlohy** .

## <a name="dont-start-stop-in-sequence"></a>Případě Virtuální počítače se nespouštějí ani nezastaví ve správném pořadí.

### <a name="issue"></a>Problém

Virtuální počítače, které jste nakonfigurovali v řešení, se nespustí ani neukončí ve správném pořadí.

### <a name="cause"></a>Příčina

To je způsobeno nesprávným označením na virtuálních počítačích.

### <a name="resolution"></a>Řešení

Proveďte následující kroky, aby bylo zajištěno, že je řešení správně nakonfigurováno.

1. Zajistěte, aby všechny virtuální počítače byly `sequencestart` `sequencestop` spuštěny nebo zastaveny, v závislosti na vaší situaci. Tyto značky vyžadují jako hodnotu kladné celé číslo. Virtuální počítače jsou zpracovávány vzestupném pořadím na základě této hodnoty.
2. Ujistěte se, že skupiny prostředků pro virtuální počítače, které se mají spustit nebo zastavit `External_Start_ResourceGroupNames` , `External_Stop_ResourceGroupNames` jsou v proměnných nebo, podle toho, jakou situaci máte.
3. Otestujte provedené změny `SequencedStartStop_Parent` spuštěním sady Runbook s parametrem WHATIF nastaveným na hodnotu true a zobrazte náhled změn.

Podrobnější informace a další pokyny k používání řešení ke spouštění a zastavování virtuálních počítačů v sekvenci najdete v tématu [spuštění a zastavení virtuálních počítačů v sekvenci](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags).

## <a name="403"></a>Případě Úloha spuštění/zastavení virtuálního počítače se nezdařila, stav 403 zakázán

### <a name="issue"></a>Problém

Vyhledáte úlohy, které selhaly s `403 forbidden` chybou pro virtuální počítače spustit/zastavit v době mimo provozní Runbooky řešení.

### <a name="cause"></a>Příčina

Tento problém může být způsoben nesprávně konfigurovaným nebo vydaným účtem spustit jako s vypršelou platností. Důvodem může být také nedostatečná oprávnění k prostředkům virtuálních počítačů pomocí účtu Automation účtů spustit jako.

### <a name="resolution"></a>Řešení

Pokud chcete ověřit, jestli je účet Spustit jako správně nakonfigurovaný, Azure Portal v části nastavení účtu vyberte účet Automation a vyberte účty Spustit jako. Tady se zobrazí stav účtů spustit jako, pokud je účet Spustit jako nesprávně nakonfigurovaný nebo vypršela jeho platnost. stav se zobrazí.

Pokud je váš účet Spustit jako [špatně nakonfigurovaný](../manage-runas-account.md#misconfiguration), měli byste účet Spustit jako odstranit a znovu vytvořit.

Pokud platnost certifikátu pro účet Spustit jako vypršela, postupujte podle kroků uvedených v části [obnovení certifikátu podepsané svým držitelem](../manage-runas-account.md#cert-renewal) a obnovte certifikát.

K tomuto problému může dojít v důsledku chybějících oprávnění. Informace o tom, jak kontrolovat oprávnění k prostředku, najdete v [tématu rychlý Start: Umožňuje zobrazit role přiřazené uživateli pomocí Azure Portal](../../role-based-access-control/check-access.md). Bude nutné zadat ID aplikace pro instanční objekt používaný účtem spustit jako. Tuto hodnotu můžete načíst tak, že přejdete do účtu Automation v Azure Portal, vyberete **účty Spustit jako** v části **Nastavení účtu** a kliknete na příslušný účet Spustit jako.

## <a name="other"></a>Případě Můj problém není uvedený výše.

### <a name="issue"></a>Problém

Při použití virtuálních počítačů spustit/zastavit v době výpadku, která není na této stránce uvedená, dochází k problému nebo neočekávanému výsledku.

### <a name="cause"></a>Příčina

V mnoha případech může být chyba způsobena použitím staré a zastaralé verze řešení.

> [!NOTE]
> Virtuální počítače spustit/zastavit v době mimo špičku byly testovány pomocí modulů Azure, které jsou importovány do účtu Automation při nasazení řešení. Řešení aktuálně nefunguje s novějšími verzemi modulu Azure. To má vliv jenom na účet Automation, který používáte ke spouštění virtuálních počítačů spustit/zastavit v době mimo špičku. V dalších účtech Automation můžete dál používat novější verze modulu Azure, jak je popsáno v tématu [Postup aktualizace Azure PowerShellch modulů v Azure Automation](../automation-update-azure-modules.md)

### <a name="resolution"></a>Řešení

Chcete-li vyřešit mnoho chyb, doporučujeme řešení odebrat a aktualizovat. Informace o tom, jak řešení aktualizovat, najdete v tématu [aktualizace virtuálních počítačů spustit/zastavit během nepracovních řešení](../automation-solution-vm-management.md#update-the-solution). Navíc můžete zkontrolovat [streamy úloh](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) a vyhledat případné chyby. Na portálu přejdete do svého účtu Automation a v části **Automatizace procesu**vyberete **úlohy** .

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
