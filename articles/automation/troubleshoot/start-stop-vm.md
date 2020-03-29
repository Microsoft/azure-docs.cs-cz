---
title: Poradce při potížích se spouštění mandy a zastavování virtuálních počítače – Azure Automation
description: Tento článek obsahuje informace o řešení potíží se spuštěním a zastavením virtuálních počítačů v Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 97ea98fc38fc8d06dc1bc65ee057241da6f15488
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851380"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Poradce při potížích s řešením spuštění a zastavení virtuálních disponecí mimo pracovní dobu

## <a name="scenario-the-startstop-vm-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Scénář: Řešení virtuálního počítače Start/Stop se nedaří správně nasadit

### <a name="issue"></a>Problém

Při nasazování [spuštění a zastavení virtuálních her během mimo pracovní dobu řešení](../automation-solution-vm-management.md), obdržíte jednu z následujících chyb:

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

Nasazení může selhat z jednoho z následujících důvodů:

1. Ve vybrané oblasti již existuje účet automatizace se stejným názvem.
2. Existuje zásada, která zakazuje nasazení řešení virtuálních počítače Start/Stop.
3. Typy `Microsoft.OperationsManagement` `Microsoft.Insights`, `Microsoft.Automation` nebo prostředky nejsou registrovány.
4. Pracovní prostor Log Analytics má zámek.
5. Máte zastaralou verzi modulů AzureRM nebo řešení Start/Stop.

### <a name="resolution"></a>Řešení

V následujícím seznamu naleznete možná řešení problému nebo míst, která chcete hledat:

1. Účty automatizace musí být jedinečné v rámci oblasti Azure, i když jsou v různých skupinách prostředků. Zkontrolujte existující účty automatizace v cílové oblasti.
2. Existující zásady brání prostředek, který je vyžadován pro řešení spuštění a zastavení virtuálního aplikace, které má být nasazeno. Přejděte na přiřazení zásad na webu Azure portal a zkontrolujte, zda máte přiřazení zásad, které zakazuje nasazení tohoto prostředku. Další informace naleznete v [tématu RequestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
3. Aby bylo možné nasadit řešení start/stop virtuálních počítačích, musí být vaše předplatné zaregistrováno do následujících oborů názvů prostředků Azure:
    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Další informace o chybách při registraci zprostředkovatelů naleznete v tématu [Resolve errors for resource provider registration.](../../azure-resource-manager/templates/error-register-resource-provider.md)
4. Pokud máte zámek v pracovním prostoru Log Analytics, přejděte do pracovního prostoru na webu Azure Portal a odeberte všechny zámky na prostředek.
5. Pokud výše uvedená řešení problém nevyřeší, postupujte podle pokynů v části [Aktualizace řešení](../automation-solution-vm-management.md#update-the-solution) znovu nasadit řešení Start/Stop.

## <a name="scenario-all-vms-fail-to-startstop"></a><a name="all-vms-fail-to-startstop"></a>Scénář: Všechny virtuální počítače se nepodaří spustit nebo zastavit

### <a name="issue"></a>Problém

Nakonfigurovali jste řešení virtuálního počítače Start/Stop, ale nespustí se ani nezastaví všechny nakonfigurované virtuální počítače.

### <a name="cause"></a>Příčina

Tato chyba může být způsobena jedním z následujících důvodů:

1. Plán není správně nakonfigurován
2. Účet RunAs nemusí být správně nakonfigurován.
3. Runbook může mít dojít k chybám
4. Virtuální společnosti mohly být vyloučeny.

### <a name="resolution"></a>Řešení

V následujícím seznamu naleznete možná řešení problému nebo míst, která chcete hledat:

* Zkontrolujte, zda jste správně nakonfigurovali plán pro řešení virtuálního počítače Start/Stop. Informace o konfiguraci plánu naleznete v článku [Plány.](../automation-schedules.md)

* Zkontrolujte [datové proudy úloh](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) a vyhledejte případné chyby. Na portálu přejděte na svůj účet automatizace a včásti **Automatizace procesů**vyberte **úlohy** . Na stránce **Úlohy** vyhledejte úlohy z jedné z následujících sad runbooků:

  * AutoStop_CreateAlert_Child
  * AutoStop_CreateAlert_Parent
  * AutoStop_Disable
  * AutoStop_VM_Child
  * ScheduledStartStop_Base_Classic
  * ScheduledStartStop_Child_Classic
  * ScheduledStartStop_Child
  * ScheduledStartStop_Parent
  * SequencedStartStop_Parent

* Ověřte, zda má [váš účet RunAs](../manage-runas-account.md) správná oprávnění k virtuálním účtům, které se pokoušíte spustit nebo zastavit. Informace o kontrole oprávnění k prostředku najdete v [tématu Úvodní příručka: Zobrazení rolí přiřazených uživateli pomocí portálu Azure](../../role-based-access-control/check-access.md). Budete muset zadat ID aplikace pro instanční objekt používaný spustit jako účet. Tuto hodnotu můžete načíst tak, že přejdete na svůj účet Automation na webu Azure Portal, vyberete **Spustit jako účty** v části Nastavení **účtu** a kliknete na příslušný účet Spustit jako.

* Virtuální ho disponitelů nemusí být spuštěné nebo zastavené, pokud jsou explicitně vyloučeny. Vyloučené virtuální chody na nastavené v **proměnné External_ExcludeVMNames** v účtu automatizace, na které se řešení nasazuje. Následující příklad ukazuje, jak můžete dotaz na tuto hodnotu s PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Scénář: Některé z mých virtuálních počítače se nedaří spustit nebo zastavit

### <a name="issue"></a>Problém

Nakonfigurovali jste řešení virtuálního počítače Start/Stop, ale nespustí se ani nezastaví některé nakonfigurované virtuální počítače.

### <a name="cause"></a>Příčina

Tato chyba může být způsobena jedním z následujících důvodů:

1. Při použití sekvenčního scénáře může značka chybět nebo může být nesprávná.
2. Virtuální vod může být vyloučeno
3. Účet RunAs nemusí mít dostatečná oprávnění pro virtuální počítač.
4. Virtuální virtuální ms může mít něco, co mu brání v spuštění nebo zastavení.

### <a name="resolution"></a>Řešení

V následujícím seznamu naleznete možná řešení problému nebo míst, která chcete hledat:

* Při použití [sekvenční scénář](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags) řešení Start/Stop virtuálního počítače během mimo pracovní dobu, musíte se ujistit, že každý virtuální počítače, který chcete spustit nebo zastavit má správnou značku. Ujistěte se, že virtuální chod, `sequencestart` které chcete spustit, mají značku `sequencestop` a virtuální chod, které chcete zastavit, mají značku. Obě značky vyžadují kladnou hodnotu celéčíslo. Pomocí dotazu podobného následujícímu příkladu můžete vyhledat všechny virtuální aplikace se značkami a jejich hodnotami.

  ```powershell-interactive
  Get-AzureRmResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Virtuální ho disponitelů nemusí být spuštěné nebo zastavené, pokud jsou explicitně vyloučeny. Vyloučené virtuální chody na nastavené v **proměnné External_ExcludeVMNames** v účtu automatizace, na které se řešení nasazuje. Následující příklad ukazuje, jak můžete dotaz na tuto hodnotu s PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Chcete-li spustit a zastavit virtuální chod, musí mít účet RunAs pro účet Automation příslušná oprávnění k virtuálnímu virtuálnímu účtu. Informace o kontrole oprávnění k prostředku najdete v [tématu Úvodní příručka: Zobrazení rolí přiřazených uživateli pomocí portálu Azure](../../role-based-access-control/check-access.md). Budete muset zadat ID aplikace pro instanční objekt používaný spustit jako účet. Tuto hodnotu můžete načíst tak, že přejdete na svůj účet Automation na webu Azure Portal, vyberete **Spustit jako účty** v části Nastavení **účtu** a kliknete na příslušný účet Spustit jako.

* Pokud virtuální počítač má potíže se spuštěním nebo zrušením volání, toto chování může být způsobeno problémem na samotném virtuálním počítači. Některé příklady nebo potenciální problémy jsou, aktualizace se používá při pokusu o vypnutí, služba přestane reagovat a další). Přejděte k prostředku virtuálního počítače a zkontrolujte **protokoly aktivit,** abyste zjistili, jestli v protokolech nejsou nějaké chyby. Můžete se také pokusit přihlásit do virtuálního virtuálního mísy a zjistit, zda jsou nějaké chyby v protokolech událostí. Další informace o řešení potíží s virtuálním počítačem najdete [v tématu Řešení potíží s virtuálními počítači Azure.](../../virtual-machines/troubleshooting/index.yml)

* Zkontrolujte [datové proudy úloh](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) a vyhledejte případné chyby. Na portálu přejděte na svůj účet automatizace a včásti **Automatizace procesů**vyberte **úlohy** .

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Scénář: Vlastní runbook se nezdaří spustit nebo zastavit virtuální počítače

### <a name="issue"></a>Problém

Vytvořili jste vlastní runbook nebo jste ji stáhli z Galerie prostředí PowerShell a nefunguje správně.

### <a name="cause"></a>Příčina

Příčinou neúspěchu může být jedna z mnoha věcí. Přejděte na svůj účet Automation na portálu Azure a v části **Automatizace procesů**vyberte **Úlohy** . Na stránce **Úlohy** vyhledejte úlohy z runbooku a zobrazte všechny chyby úloh.

### <a name="resolution"></a>Řešení

Doporučujeme použít řešení [Start/Stop v době vypnutí počítače](../automation-solution-vm-management.md) ke spuštění a zastavení virtuálních počítačů v Azure Automation. Toto řešení je autorem společnosti Microsoft. Vlastní sady Runbook nejsou podporovány společností Microsoft. Řešení pro vlastní runbook můžete najít v článku [řešení potíží s runbookem.](runbooks.md) Tento článek obsahuje obecné pokyny a řešení potíží pro sady Runbook všech typů. Zkontrolujte [datové proudy úloh](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) a vyhledejte případné chyby. Na portálu přejděte na svůj účet automatizace a včásti **Automatizace procesů**vyberte **úlohy** .

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Scénář: Virtuální počítače se nespouštějí ani nezastavují ve správném pořadí

### <a name="issue"></a>Problém

Virtuální počítače, které jste nakonfigurovali v řešení nespustí nebo zastavit ve správném pořadí.

### <a name="cause"></a>Příčina

To je způsobeno nesprávným označováním na virtuálních počítačích.

### <a name="resolution"></a>Řešení

Postupujte podle následujících kroků, abyste zajistili, že je řešení správně nakonfigurováno.

1. Ujistěte se, že všechny virtuální `sequencestart` počítače, které mají být spuštěny nebo zastaveny, mají značku nebo `sequencestop` značku, v závislosti na vaší situaci. Tyto značky potřebují jako hodnotu kladné celé číslo. Virtuální počítači jsou zpracovávány ve vzestupném pořadí na základě této hodnoty.
2. Ujistěte se, že skupiny prostředků pro virtuální počítače, které mají být spuštěny nebo zastaveny, jsou v proměnných `External_Start_ResourceGroupNames` nebo `External_Stop_ResourceGroupNames` v závislosti na vaší situaci.
3. Otestujte změny spuštěním `SequencedStartStop_Parent` sady Runbook s parametrem WHATIF nastaveným na hodnotu True, abyste zobrazili náhled změn.

Podrobnější a další pokyny, jak používat řešení ke spuštění a zastavení virtuálních počítačů v pořadí, najdete [v tématu Spuštění a zastavení virtuálních počítačů v pořadí](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags).

## <a name="scenario-startstop-vm-job-fails-with-403-forbidden-status"></a><a name="403"></a>Scénář: Spuštění a zastavení úlohy virtuálního počítače se nezdaří s 403 zakázanýstav

### <a name="issue"></a>Problém

Můžete najít úlohy, `403 forbidden` které se nezdařily s chybou pro spuštění a zastavení virtuálních účtů během mimo pracovní dobu sady runbooků řešení.

### <a name="cause"></a>Příčina

Tento problém může být způsoben nesprávně nakonfigurovaným nebo vypršela platnost spustit jako účet. Může to být také z důvodu nedostatečných oprávnění k prostředkům virtuálního počítačů podle automatizačních účtů spustit jako účet.

### <a name="resolution"></a>Řešení

Chcete-li zkontrolovat, zda je účet Spustit jako správně nakonfigurovaný, přejděte na portál Azure a v části **Nastavení účtu**vyberte Spustit **jako účty** . Zde uvidíte stav vašeho spuštění jako účtů, pokud je účet Spustit jako nesprávně nakonfigurován nebo vypršela jeho platnost, stav se zobrazí.

Pokud je váš účet Spustit jako nesprávně nakonfigurován, měli byste odstranit a znovu vytvořit účet Spustit jako. Viz [Správa azure automatizace spustit jako účty](../manage-runas-account.md).

Pokud platnost certifikátu vypršela pro váš účet Spustit jako, obnovte certifikát podle pokynů uvedených při [obnovení certifikátu podepsaného svým držitelem.](../manage-runas-account.md#cert-renewal)

Problém může být způsoben chybějícíoprávnění. Informace o kontrole oprávnění k prostředku najdete v [tématu Úvodní příručka: Zobrazení rolí přiřazených uživateli pomocí portálu Azure](../../role-based-access-control/check-access.md). Budete muset zadat ID aplikace pro instanční objekt používaný spustit jako účet. Tuto hodnotu můžete načíst tak, že přejdete na svůj účet Automation na webu Azure Portal, vyberete **Spustit jako účty** v části Nastavení **účtu** a kliknete na příslušný účet Spustit jako.

## <a name="scenario-my-problem-isnt-listed-above"></a><a name="other"></a>Scénář: Můj problém není uveden výše

### <a name="issue"></a>Problém

Při použití řešení Start/Stop v době mimo pracovní dobu, které není uvedeno na této stránce, dochází k problému nebo neočekávanému výsledku.

### <a name="cause"></a>Příčina

Mnohokrát chyby mohou být způsobeny pomocí staré a zastaralé verze řešení.

> [!NOTE]
> Řešení Start/Stop VM během mimopracovní doby bylo testováno pomocí modulů Azure, které se importují do vašeho účtu Automation při nasazování řešení. Řešení aktuálně nefunguje s novějšími verzemi modulu Azure. To má vliv pouze na účet automatizace, který používáte ke spuštění spuštění virtuálních účtů Start/Stop během mimopracovní ho řešení. Novější verze modulu Azure můžete dál používat v jiných účtech automatizace, jak je popsáno v části [Jak aktualizovat moduly Azure PowerShell u Azure Automation.](../automation-update-azure-modules.md)

### <a name="resolution"></a>Řešení

Chcete-li vyřešit mnoho chyb, doporučujeme odebrat a aktualizovat řešení. Informace o tom, jak aktualizovat řešení, najdete [v tématu Aktualizace spuštění a zastavení virtuálních her během mimo pracovní dobu řešení](../automation-solution-vm-management.md#update-the-solution). Kromě toho můžete zkontrolovat [datové proudy úloh](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) a vyhledat případné chyby. Na portálu přejděte na svůj účet automatizace a včásti **Automatizace procesů**vyberte **úlohy** .

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s – oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí propojením komunity Azure se správnými prostředky: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete nastolet incident podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.
