---
title: Spuštění runbooku ve službě Azure Automation
description: V tomto článku se dozvíte, jak spustit Runbook v Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
ms.openlocfilehash: b5c5166785ad8c82c114fb7193cd49716536b408
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896592"
---
# <a name="start-a-runbook-in-azure-automation"></a>Spuštění runbooku ve službě Azure Automation

Následující tabulka vám pomůže určit metodu spuštění sady Runbook v Azure Automation, která je nejvhodnější pro váš konkrétní scénář. Tento článek obsahuje podrobnosti o spuštění sady Runbook pomocí Azure Portal a prostředí Windows PowerShell. Podrobnosti o dalších metodách jsou k dispozici v jiné dokumentaci, ke které máte přístup z odkazů níže.

| **Metoda** | **Vlastnosti** |
| --- | --- |
| [Azure Portal](#start-a-runbook-with-the-azure-portal) |<li>Nejjednodušší metoda s interaktivním uživatelským rozhraním.<br> <li>Formulář pro zadání jednoduchých hodnot parametrů.<br> <li>Snadno Sledujte stav úlohy.<br> <li>Přístup k ověřeným přihlášením pomocí Azure |
| [Windows PowerShell](/powershell/module/azurerm.automation/start-azurermautomationrunbook) |<li>Volání z příkazového řádku pomocí rutin prostředí Windows PowerShell.<br> <li>Lze zahrnout do automatizované funkce s více kroky.<br> <li>Požadavek se ověřuje pomocí certifikátu nebo hlavního objektu zabezpečení nebo instančního objektu OAuth.<br> <li>Zadejte jednoduché a komplexní hodnoty parametrů.<br> <li>Sledovat stav úlohy.<br> <li>Klient je nutný k podpoře rutin PowerShellu. |
| [Rozhraní API pro Azure Automation](/rest/api/automation/) |<li>Nejpružnější metoda, ale také nejvíc složitá.<br> <li>Volání z libovolného vlastního kódu, který může vytvářet požadavky HTTP.<br> <li>Žádost se ověří pomocí certifikátu nebo objektu zabezpečení nebo hlavního objektu OAuth uživatele.<br> <li>Zadejte jednoduché a komplexní hodnoty parametrů. *Pokud voláte sadu Runbook v Pythonu pomocí rozhraní API, je nutné serializovat datovou část JSON.*<br> <li>Sledovat stav úlohy. |
| [Webhooky](automation-webhooks.md) |<li>Spustit Runbook z jednoho požadavku HTTP.<br> <li>Ověřeno s tokenem zabezpečení v adrese URL.<br> <li>Klient nemůže přepsat hodnoty parametrů zadané při vytvoření Webhooku. Sada Runbook může definovat jeden parametr, který je vyplněn podrobnostmi požadavku HTTP.<br> <li>Není možné sledovat stav úlohy prostřednictvím adresy URL Webhooku. |
| [Reakce na upozornění Azure](../azure-monitor/platform/alerts-overview.md) |<li>Spuštění Runbooku v reakci na upozornění Azure<br> <li>Nakonfigurujte Webhook pro Runbook a odkaz na upozornění.<br> <li>Ověřeno s tokenem zabezpečení v adrese URL. |
| [Plán](./shared-resources/schedules.md) |<li>Automaticky spustit sadu Runbook podle hodinových, denních, týdenních nebo měsíčního plánu.<br> <li>Manipulace s plánem prostřednictvím Azure Portal, rutin prostředí PowerShell nebo rozhraní Azure API.<br> <li>Zadejte hodnoty parametrů, které se mají použít s plánem. |
| [Z jiné sady Runbook](automation-child-runbooks.md) |<li>Použití Runbooku jako aktivity v jiné sadě Runbook.<br> <li>Užitečné pro funkce používané více sadami Runbook.<br> <li>Zadejte hodnoty parametrů pro podřízený Runbook a použijte výstup v nadřazeném Runbooku. |

Následující obrázek znázorňuje podrobný proces v životním cyklu Runbooku. Obsahuje různé způsoby, kterými se sada runbook spouští v Azure Automation, které součásti potřebné pro Hybrid Runbook Worker ke spouštění Azure Automation runbooků a interakce mezi různými součástmi. Další informace o spouštění Runbooků pro automatizaci ve vašem datovém centru najdete v tématu [hybridní pracovní procesy Runbooku](automation-hybrid-runbook-worker.md) .

![Architektura Runbooku](media/automation-starting-runbook/runbooks-architecture.png)

## <a name="work-with-runbook-parameters"></a>Práce s parametry Runbooku

Při spuštění Runbooku z Azure Portal nebo Windows PowerShellu se instrukce pošle prostřednictvím webové služby Azure Automation. Tato služba nepodporuje parametry s komplexními datovými typy. Pokud potřebujete zadat hodnotu komplexního parametru, musíte ji volat z jiného Runbooku, jak je popsáno v tématu [podřízené Runbooky v Azure Automation](automation-child-runbooks.md).

Webová služba Azure Automation poskytuje speciální funkce pro parametry pomocí určitých datových typů, jak je popsáno v následujících částech:

### <a name="named-values"></a>Pojmenované hodnoty

Pokud je parametr datového typu [Object], můžete k odeslání seznamu pojmenovaných hodnot použít následující formát JSON: *{název1: ' hodnota1 ', název2: ' hodnota2 ', Název3: ' hodnota3 '}*. Tyto hodnoty musí být jednoduché typy. Sada Runbook obdrží parametr jako [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject) s vlastnostmi, které odpovídají každé pojmenované hodnotě.

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

Pokud je parametr pole, jako třeba [Array] nebo [String []], můžete k odeslání seznamu hodnot použít následující formát JSON: *[Hodnota1, hodnota2, hodnota3]*. Tyto hodnoty musí být jednoduché typy.

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

Pokud je parametrem datový typ `PSCredential` , můžete zadat název [prostředku Azure Automation přihlašovacích údajů](./shared-resources/credentials.md). Sada Runbook načte pověření s názvem, který zadáte. Následující sada testů podporuje parametr s názvem `credential` .

```powershell
Workflow Test-Parameters
{
   param (
      [Parameter(Mandatory=$true)][PSCredential]$credential
   )
   $credential.UserName
}
```

Následující text lze použít pro parametr uživatele za předpokladu, že došlo k vyvolání assetu přihlašovacích údajů `My Credential` .

```input
My Credential
```

Za předpokladu, že uživatelské jméno v přihlašovacích údajích je `jsmith` , zobrazí se následující výstup.

```output
jsmith
```

## <a name="start-a-runbook-with-the-azure-portal"></a>Spuštění Runbooku pomocí Azure Portal

1. V Azure Portal vyberte **Automation** a potom klikněte na název účtu Automation.
2. V nabídce centra vyberte **Runbooky**.
3. Na stránce sady Runbook vyberte sadu Runbook a poté klikněte na tlačítko **Spustit**.
4. Pokud má Runbook parametry, budete vyzváni k zadání hodnot s textovým polem pro každý parametr. Další informace o parametrech najdete v tématu [parametry Runbooku](#work-with-runbook-parameters).
5. V podokně úlohy můžete zobrazit stav úlohy Runbooku.

## <a name="start-a-runbook-with-powershell"></a>Spuštění Runbooku pomocí PowerShellu

K spuštění Runbooku pomocí Windows PowerShellu můžete použít rutinu [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) . Následující vzorový kód spustí Runbook s názvem **test-Runbook**.

```azurepowershell-interactive
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -ResourceGroupName "ResourceGroup01"
```

`Start-AzAutomationRunbook` Vrátí objekt úlohy, který můžete použít ke sledování stavu po spuštění Runbooku. Pak můžete použít tento objekt úlohy s příkazem [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob) k určení stavu úlohy a [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) k získání jejího výstupu. Následující příklad spustí Runbook s názvem **test-Runbook**, počká na jeho dokončení a potom zobrazí jeho výstup.

```azurepowershell-interactive
$runbookName = "Test-Runbook"
$ResourceGroup = "ResourceGroup01"
$AutomationAcct = "MyAutomationAccount"

$job = Start-AzAutomationRunbook –AutomationAccountName $AutomationAcct -Name $runbookName -ResourceGroupName $ResourceGroup

$doLoop = $true
While ($doLoop) {
   $job = Get-AzAutomationJob –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup
   $status = $job.Status
   $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput –AutomationAccountName $AutomationAcct -Id $job.JobId -ResourceGroupName $ResourceGroup –Stream Output
```

Pokud sada Runbook vyžaduje parametry, je nutné je zadat jako [zatřiďovací tabulku](/powershell/module/microsoft.powershell.core/about/about_hash_tables). Klíč zatřiďovací tabulky musí odpovídat názvu parametru a hodnota je hodnota parametru. Následující příklad ukazuje spuštění Runbooku se dvěma řetězcovými parametry s názvy FirstName a LastName, celočíselným parametrem s názvem RepeatCount a logickým parametrem s názvem Show. Další informace o parametrech najdete v tématu [parametry Runbooku](#work-with-runbook-parameters).

```azurepowershell-interactive
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -ResourceGroupName "ResourceGroup01" –Parameters $params
```

## <a name="next-steps"></a>Další kroky

* Podrobnosti o správě sad Runbook najdete [v tématu Správa runbooků v Azure Automation](manage-runbooks.md).
* Podrobnosti o PowerShellu najdete v tématu [dokumentace k PowerShellu](/powershell/scripting/overview).
* Řešení potíží se spouštěním sady Runbook najdete v tématu [řešení potíží](troubleshoot/runbooks.md)se sadou Runbook.
