---
title: Řešení potíží s virtuálními aplikacemi Start/Stop během mimo pracovní dobu
description: Tento článek obsahuje informace o řešení potíží s řešením virtuálních počítačích Start/Stop.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 003c2c5a2c09957e7a3a4ac0a26b87a9ac43dace
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679153"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Poradce při potížích s řešením spuštění a zastavení virtuálních disponecí mimo pracovní dobu

Tento článek obsahuje informace o řešení problémů, které vznikají při práci s start/stop virtuálních počítačích během mimo pracovní dobu řešení.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](../automation-update-azure-modules.md).

## <a name="scenario-the-startstop-vms-during-off-hours-solution-fails-to-properly-deploy"></a><a name="deployment-failure"></a>Scénář: Řešení Start/Stop virtuálních počítače během mimo pracovní dobu se nepodaří správně nasadit

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

1. Ve vybrané oblasti již existuje účet Automation se stejným názvem.
2. Zásada zakáže nasazení spuštění a zastavení virtuálních počítače během mimo pracovní dobu řešení.
3. Typ `Microsoft.OperationsManagement` `Microsoft.Insights`, `Microsoft.Automation` nebo prostředek není registrován.
4. Pracovní prostor Log Analytics je uzamčen.
5. Máte zastaralou verzi modulů AzureRM nebo řešení Start/Stop virtuálních počítače během mimo pracovní dobu.

### <a name="resolution"></a>Řešení

Zkontrolujte následující opravy možných řešení problému:

* Účty automatizace musí být jedinečné v rámci oblasti Azure, i když jsou v různých skupinách prostředků. Zkontrolujte existující účty automatizace v cílové oblasti.
* Existující zásady brání prostředek, který je vyžadován pro spuštění a zastavení virtuálních her během mimo pracovní dobu řešení, které mají být nasazeny. Přejděte na přiřazení zásad na webu Azure portal a zkontrolujte, zda máte přiřazení zásad, které zakazuje nasazení tohoto prostředku. Další informace naleznete v [tématu RequestDisallowedByPolicy](../../azure-resource-manager/templates/error-policy-requestdisallowedbypolicy.md).
* Chcete-li nasadit řešení start/stop virtuálních počítačích, vaše předplatné musí být registrované do následujících oborů názvů prostředků Azure:

    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Další informace o chybách při registraci zprostředkovatelů najdete v [tématu Řešení chyb při registraci](../../azure-resource-manager/templates/error-register-resource-provider.md) zprostředkovatelů.
* Pokud máte zámek v pracovním prostoru Log Analytics, přejděte do pracovního prostoru na webu Azure Portal a odeberte všechny zámky na prostředek.
* Pokud výše uvedená řešení problém nevyřeší, postupujte podle pokynů v části [Aktualizace řešení](../automation-solution-vm-management.md#update-the-solution) znovu nasadit řešení Start/Stop.

## <a name="scenario-all-vms-fail-to-start-or-stop"></a><a name="all-vms-fail-to-startstop"></a>Scénář: Všechny virtuální počítače se nepodaří spustit nebo zastavit

### <a name="issue"></a>Problém

Nakonfigurovali jste řešení Spuštění/Zastavení virtuálních počítače během mimo pracovní dobu, ale nespustí se ani nezastaví všechny virtuální počítače.

### <a name="cause"></a>Příčina

Tato chyba může být způsobena jedním z následujících důvodů:

1. Plán není správně nakonfigurován.
2. Účet Spustit jako nemusí být správně nakonfigurován.
3. Runbook může mít dojít k chybám.
4. Virtuální společnosti mohly být vyloučeny.

### <a name="resolution"></a>Řešení

V následujícím seznamu naleznete možná řešení problému:

* Zkontrolujte, zda jste správně nakonfigurovali plán pro řešení Spuštění a zastavení virtuálních počítače mimo pracovní dobu. Informace o konfiguraci plánu naleznete v článku [Plány.](../automation-schedules.md)

* Zkontrolujte [datové proudy úloh](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) a vyhledejte případné chyby. Hledejte úlohy z jedné z následujících sad runbooků:

  * **AutoStop_CreateAlert_Child**
  * **AutoStop_CreateAlert_Parent**
  * **AutoStop_Disable**
  * **AutoStop_VM_Child**
  * **ScheduledStartStop_Base_Classic**
  * **ScheduledStartStop_Child_Classic**
  * **ScheduledStartStop_Child**
  * **ScheduledStartStop_Parent**
  * **SequencedStartStop_Parent**

* Ověřte, že [váš účet Spustit jako](../manage-runas-account.md) má správná oprávnění k virtuálním účtům, které se pokoušíte spustit nebo zastavit. Informace o kontrole oprávnění k prostředku najdete v [tématu Úvodní příručka: Zobrazení rolí přiřazených uživateli pomocí portálu Azure](../../role-based-access-control/check-access.md). Budete muset zadat ID aplikace pro instanční objekt používaný účtem Spustit jako. Tuto hodnotu můžete načíst tak, že přejdete na svůj účet Automation na webu Azure Portal, vyberete **Spustit jako účty** v části Nastavení **účtu**a kliknete na příslušný účet Spustit jako.

* Virtuální ho disponitelů nemusí být spuštěné nebo zastavené, pokud jsou explicitně vyloučeny. Vyloučené virtuální chody `External_ExcludeVMNames` jsou nastaveny v proměnné v účtu automatizace, do kterého se nasadí řešení. Následující příklad ukazuje, jak můžete dotaz na tuto hodnotu s PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="scenario-some-of-my-vms-fail-to-start-or-stop"></a><a name="some-vms-fail-to-startstop"></a>Scénář: Některé z mých virtuálních počítače se nedaří spustit nebo zastavit

### <a name="issue"></a>Problém

Nakonfigurovali jste řešení Spuštění a zastavení virtuálních počítače během mimo pracovní doby, ale nespustí se ani nezastaví některé nakonfigurované virtuální počítače.

### <a name="cause"></a>Příčina

Tato chyba může být způsobena jedním z následujících důvodů:

1. V sekvenčním scénáři může značka chybět nebo nesprávná.
2. Virtuální virtuální může být vyloučeno.
3. Účet Spustit jako nemusí mít dostatečná oprávnění pro virtuální počítač.
4. Virtuální ho může mít problém, který zastavil jeho spuštění nebo zastavení.

### <a name="resolution"></a>Řešení

V následujícím seznamu naleznete možná řešení problému nebo míst, která chcete hledat:

* Při použití [scénáře sekvence](../automation-solution-vm-management.md) spuštění a zastavení virtuálních počítače během mimo pracovní dobu řešení, musíte se ujistit, že každý virtuální počítače, který chcete spustit nebo zastavit má správnou značku. Ujistěte se, že virtuální chod, `sequencestart` které chcete spustit, mají značku `sequencestop` a virtuální chod, které chcete zastavit, mají značku. Obě značky vyžadují kladnou hodnotu celéčíslo. Pomocí dotazu podobného následujícímu příkladu můžete vyhledat všechny virtuální aplikace se značkami a jejich hodnotami.

  ```powershell-interactive
  Get-AzResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Virtuální virtuální chod nemusí být spuštěnnebo zastaven, pokud jsou explicitně vyloučeny. Vyloučené virtuální chody `External_ExcludeVMNames` jsou nastaveny v proměnné v účtu automatizace, do kterého se nasadí řešení. Následující příklad ukazuje, jak můžete dotaz na tuto hodnotu s PowerShell.

  ```powershell-interactive
  Get-AzAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Chcete-li spustit a zastavit virtuální chod, musí mít účet Spustit jako pro účet automatizace příslušná oprávnění k virtuálnímu virtuálnímu účtu. Informace o kontrole oprávnění k prostředku najdete v [tématu Úvodní příručka: Zobrazení rolí přiřazených uživateli pomocí portálu Azure](../../role-based-access-control/check-access.md). Budete muset zadat ID aplikace pro instanční objekt používaný účtem Spustit jako. Tuto hodnotu můžete načíst tak, že přejdete na svůj účet Automation na webu Azure Portal, vyberete **Spustit jako účty** v části Nastavení **účtu** a kliknete na příslušný účet Spustit jako.

* Pokud virtuální počítač má problém se spuštěním nebo zrušením přidělení, může být problém na samotném virtuálním počítači. Například aktualizace se používá, když se virtuální ms pokouší vypnout, služba přestane reagovat a další. Přejděte k prostředku virtuálního počítače a zkontrolujte **protokoly aktivit,** abyste zjistili, jestli v protokolech nejsou nějaké chyby. Můžete se také pokusit přihlásit do virtuálního virtuálního mísy a zjistit, zda jsou nějaké chyby v protokolech událostí. Další informace o řešení potíží s virtuálním počítačem najdete [v tématu Řešení potíží s virtuálními počítači Azure.](../../virtual-machines/troubleshooting/index.yml)

* Zkontrolujte [datové proudy úloh](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) a vyhledejte případné chyby. Na portálu přejděte na svůj účet Automatizace a vyberte **úlohy** v části **Automatizace procesů**.

## <a name="scenario-my-custom-runbook-fails-to-start-or-stop-my-vms"></a><a name="custom-runbook"></a>Scénář: Vlastní runbook se nezdaří spustit nebo zastavit virtuální počítače

### <a name="issue"></a>Problém

Vytvořili jste vlastní runbook nebo jste ji stáhli z Galerie prostředí PowerShell a nefunguje správně.

### <a name="cause"></a>Příčina

Může existovat mnoho příčin selhání. Přejděte na svůj účet Automation na portálu Azure a v části **Automatizace procesů**vyberte **Úlohy** . Na stránce Úlohy vyhledejte úlohy z runbooku a zobrazte všechny chyby úloh.

### <a name="resolution"></a>Řešení

Doporučuje se:

* Pomocí [řešení Start/Stop VM během mimo pracovní dobu](../automation-solution-vm-management.md) můžete virtuální počítače spustit a zastavit v Azure Automation. Toto řešení je autorem společnosti Microsoft. 

* Uvědomte si, že společnost Microsoft nepodporuje vlastní sady Runbook. Řešení pro vlastní runbook můžete najít z [řešení potíží se systémem Runbook](runbooks.md). Zkontrolujte [datové proudy úloh](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) a vyhledejte případné chyby. 

## <a name="scenario-vms-dont-start-or-stop-in-the-correct-sequence"></a><a name="dont-start-stop-in-sequence"></a>Scénář: Virtuální počítače se nespouštějí ani nezastavují ve správném pořadí

### <a name="issue"></a>Problém

Virtuální počítače, které jste nakonfigurovali v řešení nespustí nebo zastavit ve správném pořadí.

### <a name="cause"></a>Příčina

Tento problém je způsoben nesprávným označováním na virtuálních počítačích.

### <a name="resolution"></a>Řešení

Postupujte podle následujících kroků, abyste zajistili, že je řešení správně nakonfigurováno.

1. Ujistěte se, že všechny virtuální `sequencestart` počítače, které mají být spuštěny nebo zastaveny, mají značku nebo `sequencestop` značku, v závislosti na vaší situaci. Tyto značky potřebují jako hodnotu kladné celé číslo. Virtuální počítači jsou zpracovávány ve vzestupném pořadí na základě této hodnoty.
2. Ujistěte se, že skupiny prostředků pro virtuální počítače, které mají být spuštěny nebo zastaveny, jsou v proměnných `External_Start_ResourceGroupNames` nebo `External_Stop_ResourceGroupNames` v závislosti na vaší situaci.
3. Otestujte změny spuštěním `SequencedStartStop_Parent` sady `WHATIF` Runbook s parametrem nastaveným na hodnotu True, abyste zobrazili náhled změn.
4. Další informace o použití řešení ke spuštění a zastavení virtuálních her v pořadí najdete [v tématu Spuštění a zastavení virtuálních her v pořadí](../automation-solution-vm-management.md).

## <a name="scenario-startstop-vms-during-off-hours-job-fails-with-403-forbidden-error"></a><a name="403"></a>Scénář: Spuštění a zastavení virtuálních počítače během mimo pracovní dobu úloha se nezdaří s 403 zakázanou chybou

### <a name="issue"></a>Problém

Můžete najít úlohy, `403 forbidden` které se nezdařily s chybou pro spuštění a zastavení virtuálních účtů během mimo pracovní dobu sady runbooků řešení.

### <a name="cause"></a>Příčina

Tento problém může být způsoben nesprávně nakonfigurovaným nebo vypršela platnost účtu Spustit jako. Může to být také z důvodu nedostatečných oprávnění k prostředkům virtuálního účtu pomocí účtu Spustit jako.

### <a name="resolution"></a>Řešení

Pokud chcete ověřit, že je váš účet Run As správně nakonfigurovaný, přejděte na svůj účet Automation na webu Azure portal a v části **Nastavení účtu**vyberte **Spustit jako účty** . Pokud je účet Spustit jako nesprávně nakonfigurován nebo vypršela jeho platnost, stav zobrazí podmínku.

Pokud je váš účet Spustit jako nesprávně nakonfigurován, měli byste odstranit a znovu vytvořit účet Spustit jako. Viz [Správa azure automatizace spustit jako účty](../manage-runas-account.md).

Pokud platnost certifikátu vypršela pro váš účet Spustit jako, přečtěte si postup obnovení certifikátu [podepsaný svým držitelem](../manage-runas-account.md#cert-renewal) a obnovte certifikát.

Pokud chybí oprávnění, [přečtěte si článek Úvodní příručka: Zobrazení rolí přiřazených uživateli pomocí webu Azure Portal](../../role-based-access-control/check-access.md). Je nutné zadat ID aplikace pro instanční objekt používaný účtem Spustit jako. Tuto hodnotu můžete načíst tak, že přejdete na svůj účet Automation na webu Azure Portal, vyberete **Spustit jako účty** v části Nastavení **účtu**a kliknete na příslušný účet Spustit jako.

## <a name="scenario-my-problem-isnt-listed-above"></a><a name="other"></a>Scénář: Můj problém není uveden výše

### <a name="issue"></a>Problém

Při použití řešení Start/Stop v době mimo pracovní dobu, které není uvedeno na této stránce, dochází k problému nebo neočekávanému výsledku.

### <a name="cause"></a>Příčina

Mnohokrát chyby mohou být způsobeny pomocí staré a zastaralé verze řešení.

> [!NOTE]
> Řešení Start/Stop VM během mimo pracovní dobu bylo testováno s moduly Azure, které se importují do vašeho účtu Automation při nasazení řešení. Řešení aktuálně nefunguje s novějšími verzemi modulu Azure. To má vliv pouze na účet automatizace, který slouží ke spuštění spuštění virtuálních účtů Start/Stop během mimo pracovní dobu řešení. Novější verze modulu Azure můžete dál používat v jiných účtech Automation, jak je popsáno v [části Jak aktualizovat moduly Azure PowerShell u Azure Automation.](../automation-update-azure-modules.md)

### <a name="resolution"></a>Řešení

Chcete-li vyřešit mnoho chyb, doporučujeme odebrat a [aktualizovat řešení Spuštění a zastavení virtuálních připojení mimo pracovní dobu](../automation-solution-vm-management.md#update-the-solution). Kromě toho můžete zkontrolovat [datové proudy úloh](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) a vyhledat případné chyby. 

## <a name="next-steps"></a>Další kroky

Pokud problém nevidíte výše nebo nemůžete problém vyřešit, vyzkoušejte další podporu jedním z následujících kanálů:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure .](https://azure.microsoft.com/support/forums/)
* Spojte [@AzureSupport](https://twitter.com/azuresupport)se s oficiálním účtem Microsoft Azure a vylepšete tak zákaznickou zkušenost propojením komunity Azure se správnými prostředky: odpověďmi, podporou a odborníky.
* Soubor incidentu podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.