---
title: Spuštění sady runbook ve službě Azure Automation
description: Shrnuje různé metody, které můžete použít ke spuštění sady runbook ve službě Azure Automation a poskytuje podrobnosti o použití portálu Azure a prostředí Windows PowerShell.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0bc414d42acd665e52f3f76037dffe225344b23d
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195290"
---
# <a name="starting-a-runbook-in-azure-automation"></a>Spuštění sady runbook ve službě Azure Automation
Následující tabulka vám pomůže určit, metoda pro spuštění sady runbook ve službě Azure Automation, který je nejvhodnější k danému scénáři. Tento článek obsahuje informace o spuštění sady runbook pomocí portálu Azure a pomocí prostředí Windows PowerShell. Informace o jiných metod jsou uvedeny v jiných dokumentace, která je přístupné z níže uvedených odkazů.

| **– METODA** | **VLASTNOSTI** |
| --- | --- |
| [Azure Portal](#starting-a-runbook-with-the-azure-portal) |<li>Nejjednodušším způsobem s interaktivní uživatelské rozhraní.<br> <li>Formulář zadat jednoduchý parametr hodnoty.<br> <li>Snadno sledovat stav úlohy.<br> <li>Přístup k ověření pomocí přihlášení Azure. |
| [Windows PowerShell](https://msdn.microsoft.com/library/dn690259.aspx) |<li>Volání z příkazového řádku pomocí rutin prostředí Windows PowerShell.<br> <li>Můžou být součástí automatizované řešení s více kroků.<br> <li>Ověření žádosti o certifikát nebo OAuth uživatele hlavní / service hlavní.<br> <li>Zadejte hodnoty parametrů jednoduché a komplexní.<br> <li>Sledovat stav úlohy.<br> <li>Klient potřebné k podpoře rutiny prostředí PowerShell. |
| [Rozhraní API služby Azure Automation](https://msdn.microsoft.com/library/azure/mt662285.aspx) |<li>Nejflexibilnější, ale také většina komplexní.<br> <li>Volat z libovolný vlastní kód, který umí vytvářet požadavky HTTP.<br> <li>Žádost o ověření pomocí certifikátu nebo Oauth uživatele hlavní / service hlavní.<br> <li>Zadejte hodnoty parametrů jednoduché a komplexní. *Při volání sady runbook Python pomocí rozhraní API, musí být serializované datové části JSON.*<br> <li>Sledovat stav úlohy. |
| [Webhooky](automation-webhooks.md) |<li>Spusťte runbook z jednoho požadavku HTTP.<br> <li>K ověření pomocí tokenu zabezpečení v adrese URL.<br> <li>Hodnoty parametrů zadané při vytvoření webhooku nejde přepsat klienta. Sada Runbook může definovat jeden parametr, který je naplněn podrobnosti požadavku HTTP.<br> <li>Žádná možnost sledovat stav úlohy prostřednictvím URL webhooku se nenačetla. |
| [Reakce na výstrahy Azure](../log-analytics/log-analytics-alerts.md) |<li>Spuštění sady runbook v reakci na výstrahy Azure.<br> <li>Nakonfigurujte webhooku pro sadu runbook a odkaz na výstrahy.<br> <li>K ověření pomocí tokenu zabezpečení v adrese URL. |
| [Plán](automation-schedules.md) |<li>Runbook se automaticky spustí podle plánu hodinové, denní, týdenní nebo měsíční.<br> <li>Upravit plán prostřednictvím portálu Azure, rutiny prostředí PowerShell nebo rozhraní API služby Azure.<br> <li>Zadejte hodnoty parametrů, který se má použít s plánem. |
| [Z jiného Runbooku](automation-child-runbooks.md) |<li>Sada runbook použijte jako aktivita v jiné sady runbook.<br> <li>Tato možnost je užitečná pro funkce, které používá více sad runbook.<br> <li>Zadejte hodnoty parametrů pro podřízené sady runbook a použít výstup v nadřazené sady runbook. |

Následující obrázek ukazuje podrobné celým procesem v životním cyklu sady runbook. Obsahuje různé způsoby spuštění sady runbook ve službě Azure Automation, součásti požadované pro hybridní pracovní proces Runbooku provést sad Azure Automation runbook a interakce mezi různými součástmi. Další informace o spouštění runbooků služeb automatizace ve vašem datovém centru, najdete v tématu [procesy hybrid runbook Worker](automation-hybrid-runbook-worker.md)

![Architektura sady Runbook](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="starting-a-runbook-with-the-azure-portal"></a>Spuštění sady runbook pomocí portálu Azure
1. Na portálu Azure vyberte **automatizace** a pak klikněte na název účtu automation.
2. V nabídce centra vyberte **Runbooky**.
3. Na **sady Runbook** vyberte sadu runbook a pak klikněte na tlačítko **spustit**.
4. Pokud sada runbook obsahuje parametry, zobrazí se výzva k zadejte hodnoty s textové pole pro každý parametr. V tématu [parametry Runbooku](#Runbook-parameters) níže další podrobnosti o parametry.
5. Na **úlohy** stránky, můžete zobrazit stav úlohy sady runbook.

## <a name="starting-a-runbook-with-windows-powershell"></a>Spuštění sady runbook pomocí prostředí Windows PowerShell
Můžete použít [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) spuštění runbooku pomocí prostředí Windows PowerShell. Následující vzorový kód spustí runbook s názvem Test-Runbook.

```
Start-AzureRmAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

Spuštění AzureRmAutomationRunbook vrátí objekt úlohy, které můžete použít ke sledování jeho stavu po spuštění runbooku. Pak můžete použít tento objekt úlohy v [Get-AzureRmAutomationJob](https://msdn.microsoft.com/library/mt619440.aspx) k určení stavu úlohy a [Get-AzureRmAutomationJobOutput](https://msdn.microsoft.com/library/mt603476.aspx) k získání jejího výstupu. Následující vzorový kód spustí runbook s názvem Test-Runbook, počká byla dokončena a potom zobrazí jeho výstup.

```
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

Pokud runbook vyžaduje parametry, pak je nutné zadat jako [zatřiďovací tabulky](http://technet.microsoft.com/library/hh847780.aspx) kde klíč zatřiďovací tabulky odpovídá názvu parametru a hodnota je hodnota parametru. Následující příklad ukazuje spuštění runbooku se dvěma řetězcovými parametry s názvem FirstName a LastName, celočíselným parametrem s názvem RepeatCount a logickým parametrem s názvem Show. Další informace o parametrech najdete v tématu [parametry Runbooku](#Runbook-parameters) níže.

```
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="runbook-parameters"></a>Parametry Runbooku
Při spuštění runbooku z portálu Azure nebo prostředí Windows PowerShell, instrukce se posílá prostřednictvím webové služby Azure Automation. Tato služba nepodporuje parametry s komplexními datovými typy. Pokud je třeba zadat hodnotu komplexního parametru, pak je musíte ji volat z jiného runbooku jak je popsáno v [podřízené runbooky ve službě Azure Automation](automation-child-runbooks.md).

Webové služby Azure Automation nabízí zvláštní funkce pro parametry pomocí určitých datových typů, jak je popsáno v následujících částech:

### <a name="named-values"></a>Pojmenované hodnoty
Pokud je parametr datového typu [object], pak do něj poslat seznam pojmenovaných hodnot můžete pomocí následujícího formátu JSON: *{název1: 'Value1', NÁZEV2: 'Hodnota2', Name3: 'Hodnota3'}*. Tyto hodnoty musí být jednoduché typy. Sada runbook obdrží parametr jako [PSCustomObject](https://msdn.microsoft.com/library/system.management.automation.pscustomobject%28v=vs.85%29.aspx) s vlastnostmi, které odpovídají každé pojmenované hodnotě.

Vezměte v úvahu následující testovací runbook, který přijme parametr s názvem uživatele.

```
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

Následující text by bylo možné pro tento parametr.

```
{FirstName:'Joe',LastName:'Smith',RepeatCount:'2',Show:'True'}
```

Výsledkem je následující výstup:

```
Joe
Smith
Joe
Smith
```

### <a name="arrays"></a>Pole
Pokud je parametr pole, jako třeba [array] nebo [string []], můžete do něj poslat seznam hodnot pomocí následujícího formátu JSON: *[hodnota1, hodnota2, hodnota3]*. Tyto hodnoty musí být jednoduché typy.

Vezměte v úvahu následující testovací runbook, který přijme parametr s názvem *uživatele*.

```
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

Následující text by bylo možné pro tento parametr.

```
["Joe","Smith",2,true]
```

Výsledkem je následující výstup:

```
Joe
Smith
Joe
Smith
```

### <a name="credentials"></a>Přihlašovací údaje
Pokud je parametr datový typ **PSCredential**, můžete zadat název Azure Automation [asset přihlašovacích údajů](automation-credentials.md). Runbook načte přihlašovací údaje s názvem, který určíte.

Vezměte v úvahu následující testovací runbook, který přijme parametr s názvem přihlašovacích údajů.

```
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

Následující text by mohly být použity parametr user za předpokladu, že došlo asset přihlašovacích údajů názvem *moje pověření*.

```
My Credential
```

Za předpokladu, že uživatelské jméno v přihlašovacích údajích bylo *jsmith*, výsledkem je následující výstup:

```
jsmith
```

## <a name="next-steps"></a>Další postup
* Architektura sady runbook v aktuální článek poskytuje souhrnné informace o správě prostředků sady runbook v Azure a místně s hybridní pracovní proces Runbooku. Další informace o spouštění runbooků služeb automatizace ve vašem datovém centru, najdete v tématu [procesy Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).
* Další informace o vytváření modulární runbooky tak, aby se používat ostatní runbooky pro konkrétní nebo běžné funkce, najdete v tématu [podřízené Runbooky](automation-child-runbooks.md).

