---
title: Předání objektu JSON do runbooku Azure Automation
description: Jak předávat parametry v sadě runbook jako objekt JSON
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
keywords: prostředí PowerShell, sady runbook, json, služby azure automation
ms.openlocfilehash: 1bdeef02621924bbb7af1e676d2b275229761081
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/11/2018
ms.locfileid: "42056589"
---
# <a name="pass-a-json-object-to-an-azure-automation-runbook"></a>Předání objektu JSON do runbooku Azure Automation

Může být užitečné k ukládání dat, které chcete předat do sady runbook v souboru JSON.
Například může vytvořit soubor JSON, který obsahuje všechny parametry, které chcete předat do sady runbook.
K tomuto účelu, budete muset převést na řetězec ve formátu JSON a poté převést řetězec na objekt prostředí PowerShell před předáním jeho obsah do runbooku.

V tomto příkladu vytvoříme Powershellový skript, který volá [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) spuštění Powershellového runbooku, předá obsah ve formátu JSON do runbooku.
Powershellový runbook spustí virtuální počítač Azure, získávání parametrů pro virtuální počítač z formátu JSON, který byl předán.

## <a name="prerequisites"></a>Požadavky
Pro absolvování tohoto kurzu potřebujete:

* Předplatné Azure. Pokud nemáte účet, můžete si [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo <a href="/pricing/free-account/" target="_blank">[si zaregistrovat bezplatný účet](https://azure.microsoft.com/free/).
* [Účet Automation](automation-sec-configure-azure-runas-account.md), abyste si mohli runbook podržet a mohli ověřovat prostředky Azure.  Tento účet musí mít oprávnění ke spuštění a zastavení virtuálního počítače.
* Virtuální počítač Azure. Počítač zastavíme a spustíme, proto to nesmí být produkční virtuální počítač.
* Na místním počítači nainstalovaný Azure Powershell. Zobrazit [nainstalovat a nakonfigurovat Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.1.0) informace o tom, jak získat Azure Powershellu.

## <a name="create-the-json-file"></a>Vytvořte soubor JSON

Zadejte následující test do textového souboru a uložte ho jako `test.json` někde v místním počítači.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

## <a name="create-the-runbook"></a>Vytvoření sady runbook

Vytvořte nový runbook Powershellu s názvem "Test-Json" ve službě Azure Automation.
Zjistěte, jak vytvořit nový runbook Powershellu, najdete v článku [Můj první Powershellový runbook](automation-first-runbook-textual-powershell.md).

Tak, aby přijímal JSON data, runbook přijme jako vstupní parametr objekt.

Sada runbook pak můžete použít vlastnosti definované ve formátu JSON.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account   
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzureRmVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
 ```

 Uložení a publikování této sady runbook ve vašem účtu Automation.

## <a name="call-the-runbook-from-powershell"></a>Volání runbooku z prostředí PowerShell

Nyní můžete volat sadu runbook z místního počítače pomocí Azure Powershellu.
Spusťte následující příkazy Powershellu:

1. Přihlaste se k Azure:
   ```powershell
   Connect-AzureRmAccount
   ```
    Zobrazí se výzva k zadání přihlašovacích údajů Azure.

   > [!IMPORTANT]
   > **Add-AzureRmAccount** je nyní alias pro **Connect-AzureRMAccount**. Při vyhledávání knihovny položky, pokud se nezobrazí **Connect-AzureRMAccount**, můžete použít **Add-AzureRmAccount**, nebo ve vašem účtu Automation můžete aktualizovat moduly.

1. Získat obsah souboru JSON a převést na řetězec:
    ```powershell
    $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
    ```
    `JsonPath` představuje cestu, kam jste uložili soubor JSON.
1. Převést řetězec obsah `$json` na objekt prostředí PowerShell:
   ```powershell
   $JsonParams = @{"json"=$json}
   ```
1. Vytvořit tabulku hash pro parametry `Start-AzureRmAutomationRunbook`:
   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```
   Všimněte si, že se nastaví hodnota `Parameters` na objekt prostředí PowerShell, který obsahuje hodnoty ze souboru JSON. 
1. Spuštění runbooku
   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

Sada runbook používá hodnoty ze souboru JSON pro spuštění virtuálního počítače.

## <a name="next-steps"></a>Další postup

* Další informace o editaci v sadě textový editor sady runbook Powershellu a pracovních postupů Powershellu najdete v tématu [úpravy textových runbooků ve službě Azure Automation](automation-edit-textual-runbook.md) 
* Další informace o vytváření a import sad runbook najdete v tématu [vytvoření nebo import runbooku ve službě Azure Automation](automation-creating-importing-runbook.md)


