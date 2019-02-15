---
title: Řešení potíží s spuštěním nebo zastavením virtuálních počítačů pomocí Azure Automation
description: Tento článek obsahuje informace o řešení problémů při spouštění a zastavování virtuálních počítačů ve službě Azure Automation
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/13/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d8ef70088d904720a81ac558206a3140d7bbecd6
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56269993"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Řešení potíží s spuštění/zastavení virtuálních počítačů během vypnutí hodin řešení

## <a name="deployment-failure"></a>Scénář: Řešení pro spouštění/zastavování virtuálních počítačů nepodaří správně nasadit.

### <a name="issue"></a>Problém

Při nasazování [operací spustit/zastavit virtuální počítače mimo hodin řešení](../automation-solution-vm-management.md), zobrazí se jedna z následujících chyb:

```
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]”.
```

```
The subscription is not registered to use namespace 'Microsoft.OperationsManagement'.
```

```
The subscription is not registered to use namespace 'Microsoft.Insights'.
```

```
The scope '/subscriptions/000000000000-0000-0000-0000-00000000/resourcegroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView' cannot perform write operation because following scope(s) are locked: '/subscriptions/000000000000-0000-0000-0000-00000000/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView'. Please remove the lock and try again
```

### <a name="cause"></a>Příčina

Nasazení může selhat z jednoho z následujících důvodů:

1. Už existuje účet Automation se stejným názvem ve vybraných oblastech.
2. Zásady je na místě, který zakazuje nasazení řešení pro spouštění/zastavování virtuálních počítačů.
3. `Microsoft.OperationsManagement`, `Microsoft.Insights`, Nebo `Microsoft.Automation` typy prostředků nejsou registrovány.
4. Váš pracovní prostor Log Analytics má zámek na něj.

### <a name="resolution"></a>Řešení

Přečtěte si informace následující možná řešení problému nebo místa pro hledání:

1. Účty Automation musí být jedinečný v rámci oblasti Azure, i když jsou v různých skupinách prostředků. Kontrola existujících účtů Automation v cílové oblasti.
2. Existující zásady zabrání prostředek, který se vyžaduje pro spuštění/zastavení virtuálních počítačů řešení k nasazení. Přejděte na přiřazení zásad na webu Azure Portal a zkontrolujte, jestli máte přiřazení zásady, který zakazuje nasazení tohoto prostředku. Další informace o tom najdete v tématu [RequestDisallowedByPolicy](../../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md).
3. Pokud chcete nasadit řešení spuštění/zastavení virtuálního počítače, vaše předplatné potřebuje k registraci pro následující obory názvů prostředků Azure:
    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Zobrazit, [vyřešit chyby registrace poskytovatele prostředků](../../azure-resource-manager/resource-manager-register-provider-errors.md) Další informace o chybách při registraci poskytovatelů.
4. Pokud máte zámek na váš pracovní prostor Log Analytics, přejděte do pracovního prostoru na webu Azure Portal a odeberte žádné zámky prostředku.

## <a name="all-vms-fail-to-startstop"></a>Scénář: Všechny virtuální počítače se nepodaří spustit/zastavit

### <a name="issue"></a>Problém

Konfiguraci řešení spuštění/zastavení virtuálních počítačů, ale nelze spustit nebo zastavit všechny virtuální počítače nakonfigurované.

### <a name="cause"></a>Příčina

Tuto chybu může způsobovat jednu z následujících důvodů:

1. Plán není správně nakonfigurovaný.
2. Účet Spustit jako není správně nakonfigurován.
3. Sada runbook může mít dochází k chybám
4. Virtuální počítače byly vyloučené

### <a name="resolution"></a>Řešení

Přečtěte si informace následující možná řešení problému nebo místa pro hledání:

* Zkontrolujte, jestli jste správně nakonfigurovaný plán pro spouštění/zastavování virtuálních počítačů řešení. Další informace o konfiguraci plánu, najdete v článku [plány](../automation-schedules.md) článku.

* Zkontrolujte datové proudy úlohy pro sady runbook a vyhledejte všechny chyby. Na portálu přejděte na svůj účet Automation a vyberte **úlohy** pod **automatizace procesů**. Z **úlohy** vzhled stránky pro úlohy z jednoho z následujících sad runbook:

  * AutoStop_CreateAlert_Child
  * AutoStop_CreateAlert_Parent
  * AutoStop_Disable
  * AutoStop_VM_Child
  * ScheduledStartStop_Base_Classic
  * ScheduledStartStop_Child_Classic
  * ScheduledStartStop_Child
  * ScheduledStartStop_Parent
  * SequencedStartStop_Parent

* Ověřte vaše [účet Spustit jako](../manage-runas-account.md) má správná oprávnění k virtuálním počítačům, které se snažíte spustit nebo zastavit. Zjistěte, jak zkontrolovat oprávnění na prostředek, najdete v článku [rychlý start: Zobrazit role přiřazené uživateli pomocí webu Azure portal](../../role-based-access-control/check-access.md). Budete muset zadat Id aplikace instančního objektu služby používá účet Spustit jako. Tuto hodnotu můžete načíst tak, že přejdete do vašeho účtu Automation na webu Azure Portal, vyberte **účty spustit jako** pod **nastavení účtu** a kliknutím na příslušný účet Spustit jako.

* Virtuální počítače není spuštěna nebo zastaven, pokud jste právě vyloučené explicitně. Vyloučené virtuálních počítačů v sadě v **External_ExcludeVMNames** proměnné v účtu Automation se řešení nasadí do. Následující příklad ukazuje, jak můžete dát dotaz na tuto hodnotu v prostředí PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="some-vms-fail-to-startstop"></a>Scénář: Některé z virtuálních počítačů selhání spuštění nebo zastavení

### <a name="issue"></a>Problém

Nakonfigurujete řešení spuštění/zastavení virtuálních počítačů, ale to nebude spuštění nebo zastavení některé virtuální počítače nakonfigurované.

### <a name="cause"></a>Příčina

Tuto chybu může způsobovat jednu z následujících důvodů:

1. Pokud používáte sekvenci scénář, značky může být chybějící nebo nesprávné
2. Virtuální počítač může být vyloučen.
3. Účet Spustit jako nemusí mít dostatečná oprávnění na virtuálním počítači
4. Virtuální počítač může mít něco, co zastavení spuštění nebo zastavení

### <a name="resolution"></a>Řešení

Přečtěte si informace následující možná řešení problému nebo místa pro hledání:

* Při použití [sekvenci scénář](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags) spuštění/zastavení virtuálního počítače během vypnutí hodin řešení, je nutné jednotlivé virtuální počítače, které chcete spustit nebo zastavit má správné značky. Ujistěte se, že virtuální počítače, které chcete spustit mít `sequencestart` značky a virtuální počítače, které chcete zastavit mají `sequencestop` značky. Obě značky vyžadují kladnou celočíselnou hodnotu. Podobně jako v následujícím příkladu dotaz můžete použít k vyhledání všech virtuálních počítačích s značek a jejich hodnoty.

  ```powershell-interactive
  Get-AzureRmResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Virtuální počítače není spuštěna nebo zastaven, pokud jste právě vyloučené explicitně. Vyloučené virtuálních počítačů v sadě v **External_ExcludeVMNames** proměnné v účtu Automation se řešení nasadí do. Následující příklad ukazuje, jak můžete dát dotaz na tuto hodnotu v prostředí PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Spuštění a zastavení virtuálních počítačů, účet Spustit jako pro účet Automation musí mít příslušná oprávnění k virtuálnímu počítači. Zjistěte, jak zkontrolovat oprávnění na prostředek, najdete v článku [rychlý start: Zobrazit role přiřazené uživateli pomocí webu Azure portal](../../role-based-access-control/check-access.md). Budete muset zadat Id aplikace instančního objektu služby používá účet Spustit jako. Tuto hodnotu můžete načíst tak, že přejdete do vašeho účtu Automation na webu Azure Portal, vyberte **účty spustit jako** pod **nastavení účtu** a kliknutím na příslušný účet Spustit jako.

* Pokud virtuální počítač má problém s počáteční nebo rušení přidělení, toto chování může způsobovat chyby v samotných virtuálních počítačů. Některé příklady nebo potenciální problémy se aktualizace se právě používá při pokusu o ukončení služby. program přestane reagovat a další). Přejděte do prostředku vašeho virtuálního počítače a zkontrolujte, **protokoly aktivit** zobrazíte, pokud nejsou žádné chyby v protokolech. Taky může pokusit přihlásit k virtuálnímu počítači, pokud existují nějaké chyby v protokolech událostí.

## <a name="custom-runbook"></a>Scénář: Moje vlastní sady runbook se nezdaří spuštění nebo zastavení virtuálních počítačů

### <a name="issue"></a>Problém

Jste vytvořili vlastní sady runbook nebo stáhnout z Galerie prostředí PowerShell a nepracuje správně.

### <a name="cause"></a>Příčina

Příčinou selhání může být jednu řadu věcí. Přejděte do vašeho účtu Automation v Azure portal a vyberte **úlohy** pod **automatizace procesů**. Z **úlohy** stránky, vyhledejte úlohy ve své sadě runbook, chcete-li zobrazit případných selhání úloh.

### <a name="resolution"></a>Řešení

Doporučuje se použít [operací spustit/zastavit virtuální počítače mimo řešení hodin](../automation-solution-vm-management.md) spouštět a zastavovat virtuální počítače ve službě Azure Automation. Toto řešení je dílem společnosti Microsoft. Vlastní runbooky nejsou podporované microsoftem. Může pro vás řešení pro vaše vlastní sadu runbook návštěvou [řešení potíží s runbook](runbooks.md) článku. Tento článek obsahuje obecné pokyny a řešení potíží pro sady runbook, všechny typy.

## <a name="dont-start-stop-in-sequence"></a>Scénář: Virtuální počítače není spuštění nebo zastavení ve správném pořadí

### <a name="issue"></a>Problém

Virtuální počítače, které jste nakonfigurovali v řešení nejsou spustit nebo zastavit ve správném pořadí.

### <a name="cause"></a>Příčina

To je způsobeno nesprávnou označení na virtuálních počítačích.

### <a name="resolution"></a>Řešení

Pomocí následujících kroků zkontrolujte, že je správně nakonfigurovaná řešení.

1. Zkontrolujte všechny virtuální počítače, které jde spustit nebo zastavit mají `sequencestart` nebo `sequencestop` značky, v závislosti na vaší situaci. Tyto značky potřebovat kladné celé číslo jako hodnotu. Virtuální počítače se zpracovávají ve vzestupném pořadí na základě této hodnoty.
2. Ujistěte se, že skupiny prostředků pro virtuální počítače ke spuštění nebo zastavení jsou v `External_Start_ResourceGroupNames` nebo `External_Stop_ResourceGroupNames` proměnné, v závislosti na vaší situaci.
3. Otestujte provedené změny spuštěním `SequencedStartStop_Parent` sady runbook s parametrem WHATIF nastavena na hodnotu True pro náhled změn.

Podrobnější a další pokyny o tom, jak spouštět a zastavovat virtuální počítače v pořadí pomocí řešení najdete v tématu [spouštění/zastavování virtuálních počítačů v pořadí](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags).

## <a name="403"></a>Scénář: Úloha spuštění/zastavení virtuálního počítače je neúspěšná stavem 403 Zakázáno 

### <a name="issue"></a>Problém

Najít úlohy, které se nezdařilo `403 forbidden` spuštění/zastavení virtuálních počítačů během vypnutí runbooků řešení hodiny v podrobnostech o chybě.

### <a name="cause"></a>Příčina

Tento problém může být způsobeno nesprávně nakonfigurovaný nebo vypršela její platnost účtu spustit jako. Může být také z důvodu nedostatečné oprávnění k prostředkům virtuálního počítače pomocí účtů spustit jako účtu Automation.

### <a name="resolution"></a>Řešení

Pokud chcete zkontrolovat svůj účet Spustit jako je správně nakonfigurované, přejděte do vašeho účtu Automation v Azure portal a vyberte **účty spustit jako** pod **nastavení účtu**. Tady se zobrazí stav spuštění jako účty, pokud účet Spustit jako je nesprávně nakonfigurovaný nebo vypršela platnost stav se zobrazí toto.

Pokud je váš účet Spustit jako [nesprávně nakonfigurované](../manage-runas-account.md#misconfiguration), měli byste odstranit a znovu vytvořte účet Spustit jako.

Pokud vypršela platnost certifikátu pro účet Spustit jako, postupujte podle kroků uvedených v [podepsaný držitelem obnovení certifikátu](../manage-runas-account.md#cert-renewal) obnovení certifikátu.

Tento problém může být způsobeno chybějící oprávnění. Zjistěte, jak zkontrolovat oprávnění na prostředek, najdete v článku [rychlý start: Zobrazit role přiřazené uživateli pomocí webu Azure portal](../../role-based-access-control/check-access.md). Budete muset zadat Id aplikace instančního objektu služby používá účet Spustit jako. Tuto hodnotu můžete načíst tak, že přejdete do vašeho účtu Automation na webu Azure Portal, vyberte **účty spustit jako** pod **nastavení účtu** a kliknutím na příslušný účet Spustit jako.

## <a name="other"></a>Scénář: Problém není uvedená výše

### <a name="issue"></a>Problém

Při použití spouštění/zastavování virtuálních počítačů špičku, který není na této stránce uvedená zaznamenáte problému nebo neočekávaný výsledek.

### <a name="cause"></a>Příčina

Chyby v mnoha případech může způsobovat používá starý a zastaralou verzi řešení.

### <a name="resolution"></a>Řešení

Chcete-li vyřešit mnoho chyb, se doporučuje odebírat a aktualizovat řešení. Zjistěte, jak aktualizovat řešení, najdete v článku [aktualizace spuštění/zastavení virtuálních počítačů během vypnutí hodin řešení](../automation-solution-vm-management.md#update-the-solution).

## <a name="next-steps"></a>Další postup

Pokud nezobrazila váš problém nebo nelze vyřešit vaše potíže, navštíví některý z následujících kanálů pro další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.
