---
title: Spuštění Runbooku v Azure Automation
description: Shrnuje různé metody, které lze použít ke spuštění Runbooku v Azure Automation a obsahuje podrobné informace o použití Azure Portal a prostředí Windows PowerShell.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8200052e14161d91b7daef6a0acd9c851518d9c2
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2019
ms.locfileid: "73886492"
---
# <a name="start-a-runbook-in-azure-automation"></a>Spuštění Runbooku v Azure Automation

Následující tabulka vám pomůže určit metodu spuštění sady Runbook v Azure Automation, která je nejvhodnější pro váš konkrétní scénář. Tento článek obsahuje podrobnosti o spuštění sady Runbook pomocí Azure Portal a prostředí Windows PowerShell. Podrobnosti o dalších metodách jsou k dispozici v jiné dokumentaci, ke které máte přístup z odkazů níže.

| **Metoda** | **Svých** |
| --- | --- |
| [Azure Portal](#start-a-runbook-with-the-azure-portal) |<li>Nejjednodušší metoda s interaktivním uživatelským rozhraním.<br> <li>Formulář pro zadání jednoduchých hodnot parametrů.<br> <li>Snadno Sledujte stav úlohy.<br> <li>Přístup k ověřeným přihlášením pomocí Azure |
| [Prostředí Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Volání z příkazového řádku pomocí rutin prostředí Windows PowerShell.<br> <li>Dá se zahrnout do automatizovaného řešení s několika kroky.<br> <li>Požadavek se ověřuje pomocí certifikátu nebo hlavního objektu zabezpečení nebo instančního objektu OAuth.<br> <li>Zadejte jednoduché a komplexní hodnoty parametrů.<br> <li>Sledovat stav úlohy.<br> <li>Klient je nutný k podpoře rutin PowerShellu. |
| [Rozhraní API pro Azure Automation](/rest/api/automation/) |<li>Nejpružnější metoda, ale také nejvíc složitá.<br> <li>Volání z libovolného vlastního kódu, který může vytvářet požadavky HTTP.<br> <li>Žádost se ověří pomocí certifikátu nebo objektu zabezpečení nebo hlavního objektu OAuth uživatele.<br> <li>Zadejte jednoduché a komplexní hodnoty parametrů. *Pokud voláte sadu Runbook v Pythonu pomocí rozhraní API, je nutné serializovat datovou část JSON.*<br> <li>Sledovat stav úlohy. |
| [Webhooky](automation-webhooks.md) |<li>Spustit Runbook z jednoho požadavku HTTP.<br> <li>Ověřeno s tokenem zabezpečení v adrese URL.<br> <li>Klient nemůže přepsat hodnoty parametrů zadané při vytvoření Webhooku. Sada Runbook může definovat jeden parametr, který je vyplněn podrobnostmi požadavku HTTP.<br> <li>Není možné sledovat stav úlohy prostřednictvím adresy URL Webhooku. |
| [Reakce na upozornění Azure](../log-analytics/log-analytics-alerts.md) |<li>Spuštění Runbooku v reakci na upozornění Azure<br> <li>Nakonfigurujte Webhook pro Runbook a odkaz na upozornění.<br> <li>Ověřeno s tokenem zabezpečení v adrese URL. |
| [Plán](automation-schedules.md) |<li>Automaticky spustit sadu Runbook podle hodinových, denních, týdenních nebo měsíčního plánu.<br> <li>Manipulace s plánem prostřednictvím Azure Portal, rutin prostředí PowerShell nebo rozhraní Azure API.<br> <li>Zadejte hodnoty parametrů, které se mají použít s plánem. |
| [Z jiné sady Runbook](automation-child-runbooks.md) |<li>Použití Runbooku jako aktivity v jiné sadě Runbook.<br> <li>Užitečné pro funkce používané více sadami Runbook.<br> <li>Zadejte hodnoty parametrů pro podřízený Runbook a použijte výstup v nadřazeném Runbooku. |

Následující obrázek znázorňuje podrobný proces v životním cyklu Runbooku. Obsahuje různé způsoby, kterými se sada runbook spouští v Azure Automation, které součásti potřebné pro Hybrid Runbook Worker ke spouštění Azure Automation runbooků a interakce mezi různými součástmi. Další informace o spouštění Runbooků pro automatizaci ve vašem datovém centru najdete v tématu [hybridní pracovní procesy Runbooku](automation-hybrid-runbook-worker.md) .

![Architektura Runbooku](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="start-a-runbook-with-the-azure-portal"></a>Spuštění Runbooku pomocí Azure Portal

1. V Azure Portal vyberte **Automation** a potom klikněte na název účtu Automation.
2. V nabídce centra vyberte **Runbooky**.
3. Na stránce sady **Runbook** vyberte sadu Runbook a poté klikněte na tlačítko **Spustit**.
4. Pokud má Runbook parametry, budete vyzváni k zadání hodnot s textovým polem pro každý parametr. Další informace o parametrech najdete v tématu [parametry Runbooku](#runbook-parameters).
5. Na stránce **úloha** můžete zobrazit stav úlohy Runbooku.

## <a name="start-a-runbook-with-powershell"></a>Spuštění Runbooku pomocí PowerShellu

K spuštění Runbooku pomocí Windows PowerShellu můžete použít rutinu [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) . Následující vzorový kód spustí Runbook s názvem test-Runbook.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

Start-AzureRmAutomationRunbook vrátí objekt úlohy, který můžete použít ke sledování jeho stavu po spuštění sady Runbook. Pak můžete použít tento objekt úlohy s příkazem [Get-AzureRmAutomationJob](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjob) k určení stavu úlohy a [Get-AzureRmAutomationJobOutput](https://docs.microsoft.com/powershell/module/azurerm.automation/get-azurermautomationjoboutput) k získání jejího výstupu. Následující vzorový kód spustí Runbook s názvem test-Runbook, počká na jeho dokončení a potom zobrazí jeho výstup.

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

Pokud sada Runbook vyžaduje parametry, je nutné je zadat jako [zatřiďovací tabulku](https://technet.microsoft.com/library/hh847780.aspx). Klíč zatřiďovací tabulky musí odpovídat názvu parametru a hodnota je hodnota parametru. Následující příklad ukazuje, jak spustit sadu Runbook se dvěma řetězcovými parametry s názvy FirstName a LastName, Integer s názvem RepeatCount a logickým parametrem s názvem show. Další informace o parametrech najdete v tématu [parametry Runbooku](#runbook-parameters) .

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="runbook-parameters"></a>Parametry Runbooku

Při spuštění Runbooku z Azure Portal nebo Windows PowerShellu se instrukce pošle prostřednictvím webové služby Azure Automation. Tato služba nepodporuje parametry s komplexními datovými typy. Pokud potřebujete zadat hodnotu komplexního parametru, musíte ji volat z jiného Runbooku, jak je popsáno v tématu [podřízené Runbooky v Azure Automation](automation-child-runbooks.md).

Webová služba Azure Automation poskytuje speciální funkce pro parametry pomocí určitých datových typů, jak je popsáno v následujících částech:

### <a name="named-values"></a>Pojmenované hodnoty

Pokud je parametr datového typu [Object], můžete k odeslání seznamu pojmenovaných hodnot použít následující formát JSON: *{název1: ' hodnota1 ', název2: ' hodnota2 ', Název3: ' hodnota3 '}* . Tyto hodnoty musí být jednoduché typy. Sada Runbook obdrží parametr jako [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject) s vlastnostmi, které odpovídají každé pojmenované hodnotě.

Vezměte v úvahu následující testovací Runbook, který přijme parametr s názvem User.

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

Pro parametr uživatele lze použít následující text.

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

### <a name="arrays"></a>řadách

Pokud je parametr pole, jako třeba [Array] nebo [String []], můžete k odeslání seznamu hodnot použít následující formát JSON: *[Hodnota1, hodnota2, hodnota3]* . Tyto hodnoty musí být jednoduché typy.

Vezměte v úvahu následující testovací Runbook, který přijme parametr s názvem *User*.

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

Pro parametr uživatele lze použít následující text.

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

Pokud je parametrem datový typ **PSCredential**, můžete zadat název [assetu přihlašovacích údajů](automation-credentials.md)Azure Automation. Sada Runbook načte pověření s názvem, který zadáte.

Vezměte v úvahu následující testovací Runbook, který přijme parametr s názvem Credential.

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

Pro parametr uživatele by se mohl použít následující text za předpokladu, že existovala Asset přihlašovacích údajů s názvem *Moje přihlašovací údaje*.

```input
My Credential
```

Za předpokladu, že uživatelské jméno v přihlašovacích údajích bylo *JSmith*, výsledkem je následující výstup:

```output
jsmith
```

## <a name="next-steps"></a>Další kroky

* Architektura Runbooku v aktuálním článku poskytuje podrobný přehled runbooků, které spravují prostředky v Azure a v místním prostředí pomocí Hybrid Runbook Worker. Další informace o spouštění runbooků Automation ve vašem datovém centru najdete v tématu [hybridní pracovní procesy Runbooku](automation-hybrid-runbook-worker.md).
* Další informace o vytváření modulárních runbooků, které budou používat jiné Runbooky pro konkrétní nebo běžné funkce, najdete v tématu [podřízené Runbooky](automation-child-runbooks.md).
* Další informace o PowerShellu, včetně referenčních modulů jazyka a výukových modulů, najdete v [dokumentaci k PowerShellu](https://docs.microsoft.com/powershell/scripting/overview).
