---
title: Assety přihlašovacích údajů ve službě Azure Automation
description: Assety přihlašovacích údajů ve službě Azure Automation obsahovat zabezpečovací pověření, která slouží k ověřování k prostředkům přistupuje z runbooku nebo konfigurace DSC. Tento článek popisuje, jak vytvořit assety přihlašovacích údajů a jejich použití v runbooku nebo konfigurace DSC.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e5414a7607c036f1d60f58e1eb047da1e54f4db9
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585336"
---
# <a name="credential-assets-in-azure-automation"></a>Assety přihlašovacích údajů ve službě Azure Automation

Prostředek přihlašovacích údajů Automation obsahuje objekt, který obsahuje přihlašovací údaje zabezpečení, jako je například uživatelské jméno a heslo. Runbooků a DSC konfigurace může použít rutiny přijmout objekt PSCredential pro ověřování, nebo se může extrahovat uživatelské jméno a heslo objekt PSCredential poskytovat nějaká aplikace nebo služby, které vyžadují ověřování. Vlastnosti přihlašovacích údajů jsou bezpečně uložené ve službě Azure Automation a je přístupná v runbooku nebo konfigurace DSC se [Get-AutomationPSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) aktivity.

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Zabezpečené prostředky ve službě Azure Automation zahrnovat přihlašovací údaje, certifikátů, připojení a zašifrované proměnné. Tyto prostředky jsou zašifrované a uložené ve službě Azure Automation jednotlivých účtů automation pomocí jedinečný klíč, který je generován. Tento klíč uložený ve službě Key Vault. Před uložením o zabezpečený prostředek, je klíč načíst ze služby Key Vault a použije k zašifrování assetu.

## <a name="azure-classic-powershell-cmdlets"></a>Rutiny Azure Classic PowerShell

Rutiny v následující tabulce se používají k vytváření a správě prostředků přihlašovacích údajů automation pomocí prostředí Windows PowerShell.  Se dodávají jako součást [modulu Azure PowerShell](/powershell/azure/overview) která je k dispozici pro použití v runbooků služeb automatizace a konfigurace DSC.

| Rutiny | Popis |
|:--- |:--- |
| [Get-AzureAutomationCredential](/powershell/module/servicemanagement/azure/get-azureautomationcredential?view=azuresmps-3.7.0) |Načte informace o asset přihlašovacích údajů. Přihlašovací údaje samotného dá načíst jenom z **Get-AutomationPSCredential** aktivity. |
| [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Vytvoří nový přihlašovací údaj automatizace. |
| [Remove - AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Odebere přihlašovací údaje služby Automation. |
| [Set - AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Nastaví vlastnosti pro existující pověření služby Automation. |

## <a name="azurerm-powershell-cmdlets"></a>Rutiny AzureRM Powershellu

Pro AzureRM rutiny v následující tabulce se používají k vytváření a správě prostředků přihlašovacích údajů automation pomocí prostředí Windows PowerShell.  Se dodávají jako součást [modulu Azure RM.Automation](/powershell/azure/overview) která je k dispozici pro použití v runbooků služeb automatizace a konfigurace DSC.

| Rutiny | Popis |
|:--- |:--- |
| [Get-AzureRmAutomationCredential](/powershell/module/azurerm.automation/get-azurermautomationcredential?view=azurermps-4.4.0) |Načte informace o asset přihlašovacích údajů.  |
| [New-AzureRmAutomationCredential](/powershell/module/azurerm.automation/new-azurermautomationcredential?view=azurermps-4.4.0) |Vytvoří nový přihlašovací údaj automatizace. |
| [Remove-AzureRmAutomationCredential](/powershell/module/azurerm.automation/remove-azurermautomationcredential?view=azurermps-4.4.0) |Odebere přihlašovací údaje služby Automation. |
| [Set-AzureRmAutomationCredential](/powershell/module/azurerm.automation/set-azurermautomationcredential?view=azurermps-4.4.0) |Nastaví vlastnosti pro existující pověření služby Automation. |

## <a name="activities"></a>Aktivity

Aktivity v následující tabulce se používají pro přístup k přihlašovacím údajům v runbooku a konfiguracích DSC.

| Aktivity | Popis |
|:--- |:--- |
| Get-AutomationPSCredential |Získá přihlašovací údaje pro použití v runbooku nebo konfigurace DSC. Vrátí [System.Management.Automation.PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential) objektu. |

> [!NOTE]
> Měli byste se vyhnout používání proměnných v názvu parametru – Get-AutomationPSCredential, protože to může zkomplikovat zjišťování závislostí mezi runbooky a konfigurace DSC a assety přihlašovacích údajů v době návrhu.

## <a name="python2-functions"></a>Funkce Python2

Funkce v následující tabulce se používá pro přístup k přihlašovacím údajům v sadě runbook Python2.

| Funkce | Popis |
|:---|:---|
| automationassets.get_automation_credential | Načte informace o asset přihlašovacích údajů. |

> [!NOTE]
> Je nutné naimportovat modul "automationassets" v horní části stránky sady Python runbook pro přístup k funkce asset.

## <a name="creating-a-new-credential-asset"></a>Vytvoření nových assetů přihlašovacích údajů

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>K vytvoření nových assetů přihlašovacích údajů pomocí webu Azure portal

1. Ve svém účtu automation, klikněte na tlačítko **prostředky** části otevřete **prostředky** okno.
2. Klikněte na tlačítko **pověření** části otevřete **pověření** okno.
3. Klikněte na tlačítko **přidat přihlašovací údaj** v horní části okna.
4. Vyplňte formulář a klikněte na tlačítko **vytvořit** uložit nový přihlašovací údaj.

> [!NOTE]
> Uživatelské účty, které používají ověřování službou Multi-Factor Authentication nejsou podporovány pro použití ve službě Azure Automation.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>K vytvoření nových assetů přihlašovacích údajů pomocí Windows Powershellu

Následující vzorové příkazy znázorňují postup vytvoření nových přihlašovacích údajů služby automation. Objekt PSCredential je poprvé vytvořena s uživatelské jméno a heslo a pak použít k vytvoření asset přihlašovacích údajů. Alternativně můžete použít **Get-Credential** rutiny výzva k zadání jména a hesla.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Pomocí přihlašovacích údajů prostředí PowerShell

Načíst asset přihlašovacích údajů v runbooku nebo konfigurace DSC se **Get-AutomationPSCredential** aktivity. Tím se vrátí [objekt PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) , můžete použít s aktivitu nebo rutinu, která vyžaduje parametr PSCredential. Můžete také načíst vlastnosti objekt přihlašovacích údajů použít jednotlivě. Objekt má vlastnost pro uživatelské jméno a zabezpečené heslo, nebo můžete použít **GetNetworkCredential** metodu pro návrat [NetworkCredential](http://msdn.microsoft.com/library/system.net.networkcredential.aspx) objekt, který bude poskytovat nezabezpečené verze heslo.

### <a name="textual-runbook-sample"></a>Ukázka textové sady runbook

Následující vzorové příkazy znázorňují postup použití přihlašovacích údajů prostředí PowerShell v runbooku. V tomto příkladu přihlašovací údaje, které je načten a jeho uživatelské jméno a heslo, které jsou přiřazené do proměnné.

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

### <a name="graphical-runbook-sample"></a>Ukázkový grafický runbook

Přidáte **Get-AutomationPSCredential** aktivitu grafický runbook tak, že kliknete pravým tlačítkem na přihlašovací údaje, které v podokně knihovna grafický editor a vyberete **přidat na plátno**.

![Přidat přihlašovací údaj na plátno](media/automation-credentials/credential-add-canvas.png)

Následující obrázek ukazuje příklad použití přihlašovacích údajů v grafický runbook.  V takovém případě se používá k ověřování pro sady runbook a prostředky Azure, jak je popsáno v [ověření Runbooků pomocí uživatelského účtu Azure AD](automation-create-aduser-account.md).  První aktivita načte přihlašovací údaj, který má přístup k předplatnému Azure.  **Add-AzureAccount** aktivita pak používá tento přihlašovací údaj k ověřování pro všechny aktivity, které následují po.  A [propojení kanálu](automation-graphical-authoring-intro.md#links-and-workflow) je tady od **Get-AutomationPSCredential** je očekáván jeden objekt.  

![Přidat přihlašovací údaj na plátno](media/automation-credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Pomocí přihlašovacích údajů prostředí PowerShell DSC

Během konfigurace DSC ve službě Azure Automation může odkazovat na používání prostředků přihlašovacích údajů **Get-AutomationPSCredential**, assety přihlašovacích údajů můžete také předat ve prostřednictvím parametrů, v případě potřeby. Další informace najdete v tématu [kompilace konfigurací v Azure Automation DSC](automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Pomocí přihlašovacích údajů v Python2

Následující příklad ukazuje příklad přístup k přihlašovacím údajům runbooky Python2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Další postup

* Další informace o propojeních ve vytváření grafického obsahu najdete v tématu [odkazy v vytváření grafického obsahu](automation-graphical-authoring-intro.md#links-and-workflow)
* Různé metody ověřování díky službě Automation najdete v tématu [zabezpečení Azure Automation](automation-security-overview.md)
* První kroky s grafickými runbooky najdete v článku [Můj první grafický runbook](automation-first-runbook-graphical.md).
* První kroky s runbooky pracovních postupů PowerShellu najdete v článku [Můj první runbook pracovního postupu PowerShellu](automation-first-runbook-textual.md). 
* Začínáme s runbooky Python2 najdete v článku [Můj první runbook Python2](automation-first-runbook-textual-python2.md) 

