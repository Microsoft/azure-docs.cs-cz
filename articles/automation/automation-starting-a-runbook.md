---
title: Spuštění runbooku ve službě Azure Automation
description: Shrnuje různé metody, které lze použít ke spuštění sady runbook ve službě Azure Automation a poskytuje podrobné informace o použití webu Azure portal a prostředí Windows PowerShell.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d2aea370d7de063805eb584cd7d90395ca725b4c
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275483"
---
# <a name="starting-a-runbook-in-azure-automation"></a>Spuštění runbooku ve službě Azure Automation
Následující tabulka vám pomůže určit metodu pro spuštění sady runbook ve službě Azure Automation, která je nejvhodnější pro váš konkrétní scénář. Tento článek obsahuje podrobnosti o spuštění sady runbook pomocí webu Azure portal a pomocí Windows Powershellu. Podrobnosti o dalších metodách jsou uvedeny v jiné dokumentaci, která se dá dostat z níže uvedených odkazů.

| **– METODA** | **VLASTNOSTI** |
| --- | --- |
| [Azure Portal](#starting-a-runbook-with-the-azure-portal) |<li>Nejjednodušší způsob s interaktivní uživatelské rozhraní.<br> <li>Zadejte hodnoty parametrů jednoduchého formuláře.<br> <li>Jednoduše sledujte stav úlohy.<br> <li>Přístup k ověření pomocí přihlášení Azure. |
| [Windows PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure/start-azureautomationrunbook) |<li>Volání z příkazového řádku pomocí rutin prostředí Windows PowerShell.<br> <li>Mohou být součástí automatizované řešení s více kroky.<br> <li>Žádost se ověřuje pomocí certifikátů nebo OAuth uživatele nebo instančního objektu instančního objektu.<br> <li>Zadejte hodnoty parametrů jednoduché i složité.<br> <li>Sledovat stav úlohy.<br> <li>Klient potřebné k podpoře rutin prostředí PowerShell. |
| [Rozhraní API služby Azure Automation](https://msdn.microsoft.com/library/azure/mt662285.aspx) |<li>Nejflexibilnější, ale také většina komplexní.<br> <li>Volání z žádný vlastní kód, který umí vytvářet požadavky HTTP.<br> <li>Žádost o ověření pomocí certifikátu nebo Oauth uživatele nebo instančního objektu instančního objektu.<br> <li>Zadejte hodnoty parametrů jednoduché i složité. *Při volání runbooku v Pythonu pomocí rozhraní API, se musí serializovat datovou část JSON.*<br> <li>Sledovat stav úlohy. |
| [Webhooky](automation-webhooks.md) |<li>Spuštění runbooku z jednoho požadavku HTTP.<br> <li>Ověření pomocí tokenu zabezpečení v adrese URL.<br> <li>Klienta nejde přepsat hodnoty parametrů zadali při vytváření webhooku. Sada Runbook může definovat jeden parametr, který je naplněn podrobnosti požadavku HTTP.<br> <li>Žádná možnost sledovat stav úlohy prostřednictvím adresy URL webhooku. |
| [Reagovat na upozornění Azure](../log-analytics/log-analytics-alerts.md) |<li>Spusťte sadu runbook v reakci na upozornění Azure.<br> <li>Konfigurace webhooku pro sadu runbook a odkaz na upozornění.<br> <li>Ověření pomocí tokenu zabezpečení v adrese URL. |
| [Plán](automation-schedules.md) |<li>Automaticky spusťte runbook na hodinové, denní, týdenní nebo měsíční plán.<br> <li>Manipulace s plánu prostřednictvím webu Azure portal, rutin prostředí PowerShell nebo rozhraní API služby Azure.<br> <li>Zadejte hodnoty parametrů pro použití s plánem. |
| [Z jiného Runbooku](automation-child-runbooks.md) |<li>Pomocí sady runbook jako aktivita v jiné sady runbook.<br> <li>Užitečné pro funkce, které používá více sad runbook.<br> <li>Zadejte hodnoty parametrů pro podřízené sady runbook a výstup použít v nadřazené sady runbook. |

Následující obrázek ukazuje podrobný postup krok za krokem v životním cyklu sady runbook. Zahrnuje různé způsoby spuštění sady runbook ve službě Azure Automation, součásti vyžadované pro Hybrid Runbook Worker ke spuštění sady runbook Azure Automation a interakce mezi různými součástmi. Další informace o spouštění runbooků služeb automatizace ve vašem datovém centru, najdete v tématu [procesy hybrid runbook Worker](automation-hybrid-runbook-worker.md)

![Architektura sady Runbook](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="starting-a-runbook-with-the-azure-portal"></a>Spuštění runbooku pomocí webu Azure portal
1. Na webu Azure Portal, vyberte **automatizace** a pak klikněte na název účtu automation.
2. V nabídce centra vyberte **sady Runbook**.
3. Na **sady Runbook** stránky, vyberte sadu runbook a klikněte na **Start**.
4. Pokud má runbook parametry, zobrazí se výzva k zadání jejich hodnot textové pole pro každý parametr. Zobrazit [parametry Runbooku](#Runbook-parameters) níže pro další podrobnosti o parametrech.
5. Na **úlohy** stránky, můžete zobrazit stav úlohy runbooku.

## <a name="starting-a-runbook-with-windows-powershell"></a>Spuštění runbooku pomocí prostředí Windows PowerShell
Můžete použít [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) spuštění runbooku pomocí prostředí Windows PowerShell. Následující vzorový kód spustí runbook s názvem Test-Runbook.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

Start-AzureRmAutomationRunbook vrátí objekt úlohy, který můžete použít ke sledování jeho stavu po spuštění runbooku. Pak můžete použít tento objekt úlohy v rutině [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) k určení stavu úlohy a [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) k získání jejího výstupu. Následující vzorový kód spustí runbook s názvem Test-Runbook, počká na dokončení a potom zobrazí jeho výstup.

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

Pokud runbook vyžaduje parametry, pak je potřeba je zadat jako [zatřiďovací tabulky](https://technet.microsoft.com/library/hh847780.aspx) kde klíč zatřiďovací tabulky odpovídá názvu parametru a hodnota je hodnota tohoto parametru. Následující příklad ukazuje spuštění runbooku se dvěma řetězcovými parametry s názvy FirstName a LastName, celočíselným parametrem s názvem RepeatCount a logickým parametrem s názvem Show. Další informace o parametrech najdete v tématu [parametry Runbooku](#Runbook-parameters) níže.

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="runbook-parameters"></a>Parametry Runbooku
Při spuštění runbooku z webu Azure portal nebo prostředí Windows PowerShell, instrukce se posílá prostřednictvím webové služby Azure Automation. Tato služba nepodporuje parametry s komplexními datovými typy. Pokud je potřeba zadat hodnotu komplexního parametru, pak je nutné ji volat z jiného runbooku jak je popsáno v [podřízené runbooky ve službě Azure Automation](automation-child-runbooks.md).

Webové služby Azure Automation nabízí zvláštní funkce pro parametry pomocí určitých datových typů, jak je popsáno v následujících částech:

### <a name="named-values"></a>Pojmenované hodnoty
Pokud je parametr datového typu [object], pak můžete pomocí následujícího formátu JSON do něj poslat seznam pojmenovaných hodnot: *{Name1: "Hodnota1", Name2: "Hodnota2", nazev3: 'Hodnota3'}*. Jednoduché typy musejí být tyto hodnoty. Runbook obdrží parametr jako [PSCustomObject](https://msdn.microsoft.com/library/system.management.automation.pscustomobject%28v=vs.85%29.aspx) s vlastnostmi, které odpovídají každé pojmenované hodnotě.

Vezměte v úvahu následující testovací runbook, který přijme parametr s názvem uživatele.

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

Následující text by bylo použít pro tento parametr.

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
Pokud je parametr pole, jako třeba [array] nebo [string []], můžete do něj poslat seznam hodnot pomocí následujícího formátu JSON: *[hodnota1, hodnota2, hodnota3]*. Jednoduché typy musejí být tyto hodnoty.

Zkuste použít následující testovací runbook, který přijme parametr s názvem *uživatele*.

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

Následující text by bylo použít pro tento parametr.

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
Pokud je parametr datového typu **PSCredential**, pak můžete zadat název služby Azure Automation [asset přihlašovacích údajů](automation-credentials.md). Runbook načte přihlašovací údaje, které s vámi určeným názvem.

Vezměte v úvahu následující testovací runbook, který přijme parametr s názvem přihlašovacích údajů.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

Následující text by mohly být použity parametr user za předpokladu, že by existovat Asset přihlašovacích údajů volá *Moje přihlašovací údaje*.

```input
My Credential
```

Uživatelské jméno v přihlašovacích údajích bylo *jsmith*, výsledkem je následující výstup:

```output
jsmith
```

## <a name="next-steps"></a>Další postup
* Architektura sady runbook v aktuální článek poskytuje podrobný přehled runbooky ke správě prostředků v Azure a místně pomocí funkce Hybrid Runbook Worker. Další informace o spouštění runbooků služeb automatizace ve vašem datovém centru, najdete v tématu [procesy Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).
* Další informace o vytváření modulární runbooky se použije jako další sady runbook pro konkrétní nebo běžné funkce, najdete v tématu [podřízené Runbooky](automation-child-runbooks.md).

