---
title: Prostředky přihlašovacích údajů v Azure Automation
description: Prostředky přihlašovacích údajů v Azure Automation obsahují zabezpečovací pověření, která se dají použít k ověřování prostředků, ke kterým má přístup sada Runbook nebo konfigurace DSC. Tento článek popisuje, jak vytvořit assety přihlašovacích údajů a použít je v sadě Runbook nebo konfiguraci DSC.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 767c1fddbc3d1f46d4341a70c990c2b57ad40e54
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373543"
---
# <a name="credential-assets-in-azure-automation"></a>Prostředky přihlašovacích údajů v Azure Automation

Asset přihlašovacích údajů pro automatizaci obsahuje objekt, který obsahuje zabezpečovací přihlašovací údaje, jako je uživatelské jméno a heslo. Runbooky a konfigurace DSC můžou používat rutiny, které přijímají objekt PSCredential k ověřování, nebo můžou extrahovat uživatelské jméno a heslo k objektu PSCredential, aby se zajistila některá aplikace nebo služba vyžadující ověření. Vlastnosti pro přihlašovací údaje jsou bezpečně uložené v Azure Automation a jsou dostupné v sadě Runbook nebo konfiguraci DSC pomocí aktivity [Get-AutomationPSCredential](#activities) .

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Zabezpečené prostředky v Azure Automation zahrnují přihlašovací údaje, certifikáty, připojení a šifrované proměnné. Tyto prostředky jsou zašifrované a uložené v Azure Automation pomocí jedinečného klíče, který se generuje pro každý účet Automation. Tento klíč je uložený v Key Vault. Před uložením zabezpečeného prostředku se klíč načte z Key Vault a pak se použije k zašifrování assetu.

## <a name="azure-powershell-az-cmdlets"></a>Azure PowerShell AZ rutiny

Pro Azure PowerShell AZ Module se k vytváření a správě prostředků přihlašovacích údajů automatizace pomocí Windows PowerShellu použijí rutiny uvedené v následující tabulce. Dodávají se jako součást [modulu AzureAz. Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0), který je k dispozici pro použití v sadách Automation a konfiguracích DSC.

| Rutiny | Popis |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Načte informace o assetu přihlašovacích údajů. To nevrátí objekt PSCredential.  |
| [New-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Vytvoří nové přihlašovací údaje automatizace. |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Odebere přihlašovací údaje automatizace. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Nastaví vlastnosti pro existující přihlašovací údaje automatizace. |

## <a name="activities"></a>Aktivity

Aktivity v následující tabulce se používají pro přístup k přihlašovacím údajům v konfiguraci sady Runbook a DSC.

| Aktivity | Popis |
|:--- |:--- |
| Get-AutomationPSCredential |Získá pověření pro použití v sadě Runbook nebo konfiguraci DSC. Vrátí objekt [System. Management. Automation. PSCredential](/dotnet/api/system.management.automation.pscredential) . |

> [!NOTE]
> Nepoužívejte proměnné v parametru – name pro Get-AutomationPSCredential, protože to může zkomplikovat zjišťování závislostí mezi sadami Runbook nebo konfiguracemi DSC a přihlašovacími údaji v době návrhu.

## <a name="python2-functions"></a>Funkce Python2

Funkce v následující tabulce slouží k přístupu k přihlašovacím údajům v Runbooku Python2.

| Funkce | Popis |
|:---|:---|
| automationassets.get_automation_credential | Načte informace o assetu přihlašovacích údajů. |

> [!NOTE]
> Aby bylo možné získat přístup k funkcím assetu, musíte naimportovat modul "automationassets" v horní části Runbooku sady Python.

## <a name="creating-a-new-credential-asset"></a>Vytváření nového prostředku přihlašovacích údajů

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Vytvoření nového assetu přihlašovacích údajů pomocí Azure Portal

1. Z účtu Automation v části **sdílené prostředky**vyberte **přihlašovací údaje** .
1. Vyberte **Přidat pověření**.
1. Vyplňte formulář a výběrem **vytvořit** uložte nové přihlašovací údaje.

> [!NOTE]
> Uživatelské účty, které používají službu Multi-Factor Authentication, se nepodporují pro použití v Azure Automation.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Vytvoření nového assetu přihlašovacích údajů pomocí Windows PowerShellu

Následující vzorové příkazy ukazují, jak vytvořit nové přihlašovací údaje automatizace. Nejprve se vytvoří objekt PSCredential s názvem a heslem a pak se použije k vytvoření assetu přihlašovacích údajů. Alternativně můžete použít k zadání jména a hesla rutinu **Get-Credential** .

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Použití přihlašovacích údajů prostředí PowerShell

Asset přihlašovacích údajů načtete v sadě Runbook nebo v konfiguraci DSC s aktivitou **Get-AutomationPSCredential** . Vrátí [objekt PSCredential](/dotnet/api/system.management.automation.pscredential) , který můžete použít s aktivitou nebo rutinou, která vyžaduje parametr PSCredential. Můžete také načíst vlastnosti objektu přihlašovacích údajů, které se mají použít jednotlivě. Objekt má vlastnost pro uživatelské jméno a zabezpečené heslo, nebo můžete použít metodu **GetNetworkCredential** k vrácení objektu [NetworkCredential](/dotnet/api/system.net.networkcredential) , který bude poskytovat nezabezpečenou verzi hesla.

> [!NOTE]
> **Get-AzAutomationCredential** nevrátí **PSCredential** , který se dá použít k ověřování. Poskytuje jenom informace o přihlašovacích údajích. Pokud potřebujete použít přihlašovací údaje v sadě Runbook, je nutné k načtení objektu **PSCredential** použít **příkaz Get-AutomationPSCredential** .

### <a name="textual-runbook-sample"></a>Ukázka textové sady Runbook

Následující vzorové příkazy znázorňují postup použití přihlašovacích údajů prostředí PowerShell v runbooku. V tomto příkladu se načtou přihlašovací údaje a její uživatelské jméno a heslo přiřazené k proměnným.

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Přihlašovací údaje můžete použít také k ověření v Azure pomocí [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0). Ve většině případů byste měli použít [účet Spustit jako](../manage-runas-account.md) a načíst ho pomocí rutiny [Get-AzAutomationConnection](../automation-connections.md).

```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzureRmAccount -Credential $myPsCred
```

### <a name="graphical-runbook-sample"></a>Ukázka grafického Runbooku

Aktivitu **Get-AutomationPSCredential** přidáte do grafického Runbooku tak, že kliknete pravým tlačítkem na přihlašovací údaje v podokně Knihovna v grafickém editoru a vyberete **Přidat na plátno**.

![Přidat přihlašovací údaje na plátno](../media/credentials/credential-add-canvas.png)

Následující obrázek ukazuje příklad použití přihlašovacích údajů v grafickém Runbooku. V tomto případě se používá k poskytnutí ověřování pro Runbook prostředků Azure, jak je popsáno v tématu [ověřování runbooků pomocí uživatelského účtu Azure AD](../automation-create-aduser-account.md). První aktivita načte přihlašovací údaje, které mají přístup k předplatnému Azure. Aktivita **Connect-AzureRmAccount** pak pomocí těchto přihlašovacích údajů poskytuje ověřování pro všechny aktivity, které jsou po ní. [Odkaz na kanál](../automation-graphical-authoring-intro.md#links-and-workflow) je tu, protože **Get-AutomationPSCredential** očekává jeden objekt.  

![Přidat přihlašovací údaje na plátno](../media/credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Použití přihlašovacích údajů prostředí PowerShell v DSC

Konfigurace DSC v Azure Automation můžou odkazovat na prostředky přihlašovacích údajů pomocí rutiny **Get-AutomationPSCredential**, a pokud je to možné, můžou se assety přihlašovacích údajů taky předávat prostřednictvím parametrů. Další informace najdete v tématu [kompilace konfigurací v Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Použití přihlašovacích údajů v Python2

Následující příklad ukazuje příklad přístupu k přihlašovacím údajům v sadách Runbook Python2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Další kroky

* Další informace o propojeních v grafickém vytváření najdete v tématu [odkazy v grafickém vytváření](../automation-graphical-authoring-intro.md#links-and-workflow) .
* Informace o různých metodách ověřování pomocí automatizace najdete v tématu [Azure Automation Security](../automation-security-overview.md) .
* První kroky s grafickými runbooky najdete v článku [Můj první grafický runbook](../automation-first-runbook-graphical.md).
* První kroky s runbooky pracovních postupů PowerShellu najdete v článku [Můj první runbook pracovního postupu PowerShellu](../automation-first-runbook-textual.md).
* Informace o tom, jak začít s Python2 Runbooky, najdete v tématu [můj první Runbook Python2](../automation-first-runbook-textual-python2.md) . 
