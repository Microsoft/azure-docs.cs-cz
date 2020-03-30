---
title: Spuštění runbooku ve službě Azure Automation
description: Shrnuje různé metody, které se dá použít ke spuštění sady Runbook v Azure Automation a poskytuje podrobnosti o používání portálu Azure a Windows PowerShell.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: 71dd83db02537ed12dc2e711127e32d90603af6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252646"
---
# <a name="start-a-runbook-in-azure-automation"></a>Spuštění runbooku ve službě Azure Automation

Následující tabulka vám pomůže určit metodu spuštění sady Runbook v Azure Automation, která je nejvhodnější pro váš konkrétní scénář. Tento článek obsahuje podrobnosti o spuštění sady Runbook s portálem Azure a windows powershellem. Podrobnosti o ostatních metodách jsou uvedeny v jiné dokumentaci, ke které máte přístup z níže uvedených odkazů.

| **Metoda** | **Vlastnosti** |
| --- | --- |
| [Portál Azure](#start-a-runbook-with-the-azure-portal) |<li>Nejjednodušší metoda s interaktivním uživatelským rozhraním.<br> <li>Formulář pro poskytnutí jednoduchých hodnot parametrů.<br> <li>Snadno sledovat stav úlohy.<br> <li>Přístup ověřený pomocí přihlášení do Azure. |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Volání z příkazového řádku pomocí rutin prostředí Windows PowerShell<br> <li>Může být součástí automatizovaného řešení s více kroky.<br> <li>Požadavek je ověřen pomocí certifikátu nebo uživatelského objektu /instančního objektu OAuth.<br> <li>Poskytněte jednoduché a složité hodnoty parametrů.<br> <li>Sledovat stav úlohy.<br> <li>Klient je vyžadován pro podporu rutin prostředí PowerShell. |
| [Rozhraní API azure automatizace](/rest/api/automation/) |<li>Nejflexibilnější metoda, ale také nejsložitější.<br> <li>Volání z libovolného vlastního kódu, který může vytvářet požadavky HTTP.<br> <li>Požadavek ověřený pomocí certifikátu nebo oauth user instanční ho .<br> <li>Poskytněte jednoduché a složité hodnoty parametrů. *Pokud voláte runbook Pythonu pomocí rozhraní API, musí být serializována datová část JSON.*<br> <li>Sledovat stav úlohy. |
| [Webhooky](automation-webhooks.md) |<li>Spusťte runbook z jednoho požadavku HTTP.<br> <li>Ověřeno pomocí tokenu zabezpečení v adrese URL.<br> <li>Klient nemůže přepsat hodnoty parametrů zadané při vytvoření webhooku. Runbook můžete definovat jeden parametr, který je naplněn podrobnosti požadavku HTTP.<br> <li>Žádná možnost sledovat stav úlohy prostřednictvím adresy URL webhooku. |
| [Reakce na výstrahu Azure](../log-analytics/log-analytics-alerts.md) |<li>Spusťte runbook v reakci na výstrahu Azure.<br> <li>Konfigurace webhooku pro runbook a odkaz na výstrahu.<br> <li>Ověřeno pomocí tokenu zabezpečení v adrese URL. |
| [Plán](automation-schedules.md) |<li>Automaticky spouštět runbook podle hodinového, denního, týdenního nebo měsíčního plánu.<br> <li>Manipulujte s plánem prostřednictvím portálu Azure, rutin powershellu nebo rozhraní Azure API.<br> <li>Zadejte hodnoty parametrů, které mají být použity s plánem. |
| [Z jiného runbooku](automation-child-runbooks.md) |<li>Použijte runbook jako aktivitu v jiném runbooku.<br> <li>Užitečné pro funkce používané více runbooky.<br> <li>Zadejte hodnoty parametrů pro podřízený runbook a použijte výstup v nadřazené runbooku. |

Následující obrázek znázorňuje podrobný postup v životním cyklu runbooku. Zahrnuje různé způsoby spuštění sady Runbook v Azure Automation, které součásti potřebné pro hybridní runbook worker ke spuštění runbooků Azure Automation a interakce mezi různými součástmi. Informace o provádění runbooků Automation ve vašem datovém centru najdete v [hybridních pracovnících runbooku.](automation-hybrid-runbook-worker.md)

![Architektura runbooku](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="start-a-runbook-with-the-azure-portal"></a>Spuštění runbooku s portálem Azure

1. Na webu Azure Portal vyberte **Automatizace** a klikněte na název účtu automatizace.
2. V nabídce Hub vyberte **Runbook .**
3. Na stránce **Runbook** vyberte runbook a klikněte na **Start**.
4. Pokud má runbook parametry, budete vyzváni k zadání hodnot s textovým polem pro každý parametr. Další informace o parametrech naleznete v tématu [Parametry knihy Runbook](#runbook-parameters).
5. Na stránce **Úloha** můžete zobrazit stav úlohy runbooku.

## <a name="start-a-runbook-with-powershell"></a>Spuštění runbooku s PowerShellem

Pomocí sady [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) můžete spustit runbook s prostředím Windows PowerShell. Následující ukázkový kód spustí Runbook s názvem Test-Runbook.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

Start-AzureRmAutomationRunbook vrátí objekt úlohy, který můžete použít ke sledování jeho stavu po spuštění sady Runbook. Potom můžete použít tento objekt úlohy s [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) k určení stavu úlohy a [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) získat jeho výstup. Následující vzorový kód spustí Runbook s názvem Test-Runbook, počká na jeho dokončení a potom zobrazí jeho výstup.

```azurepowershell-interactive
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzureRmAutomationRunbook –AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzureRmAutomationJob –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzureRmAutomationJobOutput –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup –Stream Output
```

Pokud runbook vyžaduje parametry, musíte je zadat jako [hodnotitelnou hodnotu hash .](https://technet.microsoft.com/library/hh847780.aspx) Klíč hodnoty hashtable musí odpovídat názvu parametru a hodnota je hodnota parametru. Následující příklad ukazuje spuštění Runbooku se dvěma řetězcovými parametry s názvy FirstName a LastName, celočíselným parametrem s názvem RepeatCount a logickým parametrem s názvem Show. Další informace o parametrech najdete v tématu [Parametry runbooku](#runbook-parameters) níže.

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="runbook-parameters"></a>Parametry runbooku

Když spustíte runbook z portálu Azure nebo Windows PowerShell, instrukce se odešle prostřednictvím webové služby Azure Automation. Tato služba nepodporuje parametry se složitými datovými typy. Pokud potřebujete zadat hodnotu pro komplexní parametr, musíte jej volat vřádkem z jiné sady Runbook, jak je popsáno v [podřízených runbookech v Azure Automation](automation-child-runbooks.md).

Webová služba Azure Automation poskytuje speciální funkce pro parametry pomocí určitých datových typů, jak je popsáno v následujících částech:

### <a name="named-values"></a>Pojmenované hodnoty

Pokud je parametrem datový typ [objekt], můžete jej použít v následujícím formátu JSON k odeslání seznamu pojmenovaných hodnot: *{Name1:'Value1', Name2:'Value2', Name3:'Value3'}*. Tyto hodnoty musí být jednoduché typy. Runbook obdrží parametr jako [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject) s vlastnostmi, které odpovídají každé pojmenované hodnotě.

Zkuste použít následující testovací Runbook, který přijme parametr s názvem user.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][object]$user
   )
    $userObject = $user | ConvertFrom-JSON
    if ($userObject.Show) {
        foreach ($i in 1..$userObject.RepeatCount) {
            $userObject.FirstName
            $userObject.LastName
        }
    }
}
```

Pro tento parametr můžete použít následující text.

```json
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

Výsledkem je následující výstup:

```output
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>Pole

Pokud je parametrem pole jako [array] nebo [string[]], můžete jej odeslat pomocí následujícího formátu JSON a odeslat mu seznam hodnot: *[Value1, Value2, Value3]*. Tyto hodnoty musí být jednoduché typy.

Zkuste použít následující testovací Runbook, který přijme parametr s názvem *user*.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][array]$user
   )
    if ($user[3]) {
        foreach ($i in 1..$user[2]) {
            $ user[0]
            $ user[1]
        }
    }
}
```

Pro tento parametr můžete použít následující text.

```input
["Joe","Smith",2,true]
```

Výsledkem je následující výstup:

```output
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>Přihlašovací údaje

Pokud je parametrem datový typ **PSCredential**, můžete zadat název [datového prostředku](automation-credentials.md)Azure Automation . Runbook načte pověření s názvem, který zadáte.

Zkuste použít následující testovací Runbook, který přijme parametr s názvem credential.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

Následující text lze použít pro parametr uživatele za předpokladu, že existuje datový zdroj pověření s názvem *Moje pověření*.

```input
My Credential
```

Za předpokladu, že uživatelské jméno v pověření bylo *jsmith*, výsledkem je následující výstup:

```output
jsmith
```

## <a name="next-steps"></a>Další kroky

* Architektura sady Runbook v aktuálním článku poskytuje podrobný přehled runbooků, které spravují prostředky v Azure a místně pomocí hybridního pracovníka runbooku. Další informace o provádění runbooků Automatizace v datovém centru najdete [v hybridních pracovnících runbooku](automation-hybrid-runbook-worker.md).
* Další informace o vytváření modulárních runbooků, které mají používat jiné sady Runbook pro specifické nebo běžné funkce, naleznete v [tématu Child Runbook](automation-child-runbooks.md).
* Další informace o prostředí PowerShell, včetně jazykových odkazů a výukových modulů, najdete v dokumentu [PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
