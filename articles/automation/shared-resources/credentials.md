---
title: Spravovat přihlašovací údaje v Azure Automation
description: Prostředky přihlašovacích údajů v Azure Automation obsahují zabezpečovací pověření, která se dají použít k ověřování prostředků, ke kterým má přístup sada Runbook nebo konfigurace DSC. Tento článek popisuje, jak vytvořit assety přihlašovacích údajů a použít je v sadě Runbook nebo konfiguraci DSC.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4226a625918be378b14e14c55fe4dd4ca5c398d5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82136681"
---
# <a name="manage-credentials-in-azure-automation"></a>Spravovat přihlašovací údaje v Azure Automation

Asset přihlašovacích údajů pro automatizaci obsahuje objekt, který obsahuje zabezpečovací pověření, například uživatelské jméno a heslo. Runbooky a konfigurace DSC používají rutiny, které přijímají pro ověřování objekt [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) . Případně mohou extrahovat uživatelské jméno a heslo `PSCredential` objektu, které mají být k dispozici pro některé aplikace nebo služby vyžadující ověřování. 

> [!NOTE]
> Zabezpečené prostředky v Azure Automation zahrnují přihlašovací údaje, certifikáty, připojení a šifrované proměnné. Tyto prostředky jsou zašifrované a uložené v Azure Automation pomocí jedinečného klíče, který se generuje pro každý účet Automation. Tento klíč je uložený v Key Vault. Před uložením zabezpečeného prostředku se klíč načte z Key Vault a pak se použije k zašifrování assetu.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci nástroje AZ Module Hybrid Runbook Worker najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pro váš účet Automation můžete aktualizovat moduly na nejnovější verzi pomocí [postupu aktualizace modulů Azure PowerShell v Azure Automation](../automation-update-azure-modules.md).

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="azure-powershell-az-cmdlets-used-for-credential-assets"></a>Azure PowerShell AZ rutiny používané pro assety přihlašovacích údajů

V rámci Azure PowerShell AZ Module se k vytváření a správě prostředků přihlašovacích údajů automatizace pomocí Windows PowerShellu použijí rutiny uvedené v následující tabulce. Dodávají se v [modulu AZ. Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0), který je k dispozici pro použití v sadách Automation a konfiguracích DSC. Viz [AZ Module Support in Azure Automation](https://docs.microsoft.com/azure/automation/az-modules).

| Rutina | Popis |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Načte objekt [CredentialInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo?view=azurerm-ps) obsahující metadata o přihlašovacích údajích. Rutina nenačte samotný `PSCredential` objekt.  |
| [New-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Vytvoří nové přihlašovací údaje automatizace. |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Odebere přihlašovací údaje automatizace. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Nastaví vlastnosti pro existující přihlašovací údaje automatizace. |

## <a name="activities-used-to-access-credentials"></a>Aktivity používané pro přístup k přihlašovacím údajům

Aktivity v následující tabulce se používají pro přístup k přihlašovacím údajům v grafických sadách Runbook a konfiguracích DSC. Příklady použití aktivit najdete v tématu věnovaném [vytváření grafik v Azure Automation](../automation-graphical-authoring-intro.md#activities).

| Aktivita | Popis |
|:--- |:--- |
| `Get-AutomationPSCredential` |Získá `PSCredential` objekt, který se má použít v sadě Runbook nebo konfiguraci DSC. Nejčastěji byste tuto aktivitu měli použít místo `Get-AzAutomationCredential` rutiny, protože ta načte jenom informace o přihlašovacích údajích. Tyto informace nejsou obvykle užitečné k předání jiné rutině. |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |Získá přihlašovací údaje s výzvou k zadání uživatelského jména a hesla. |
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | Vytvoří Asset přihlašovacích údajů. |

Chcete- `PSCredential` li načíst objekty v kódu, musíte importovat `Orchestrator.AssetManagement.Cmdlets` modul. Další informace najdete v tématu [Správa modulů v Azure Automation](modules.md).

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> Nepoužívejte proměnné v `Name` parametru `Get-AutomationPSCredential`. Jejich použití může zkomplikovat zjišťování závislostí mezi sadami Runbook a konfigurací DSC a assety přihlašovacích údajů v době návrhu.

## <a name="python-2-functions-that-access-credentials"></a>Funkce Python 2, které přistupují k přihlašovacím údajům

Funkce v následující tabulce slouží k přístupu k přihlašovacím údajům v sadě Runbook Python 2.

| Funkce | Popis |
|:---|:---|
| `automationassets.get_automation_credential` | Načte informace o assetu přihlašovacích údajů. |

> [!NOTE]
> Importujte `automationassets` modul v horní části Runbooku Pythonu, abyste měli přístup k funkcím assetu.

## <a name="creating-a-new-credential-asset"></a>Vytváření nového prostředku přihlašovacích údajů

Pomocí Azure Portal nebo Windows PowerShellu můžete vytvořit nový prostředek přihlašovacích údajů.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Vytvoření nového assetu přihlašovacích údajů pomocí Azure Portal

1. Z účtu Automation v části **sdílené prostředky**vyberte **přihlašovací údaje** .
1. Vyberte **Přidat pověření**.
2. V podokně nové přihlašovací údaje zadejte odpovídající název přihlašovacích údajů podle standardů pojmenování. 
3. Do pole **uživatelské jméno** zadejte své přístupové ID. 
4. Pro obě pole hesla zadejte svůj tajný přístupový klíč.

    ![Vytvořit nové přihlašovací údaje](../media/credentials/credential-create.png)

5. Pokud je zaškrtnuto políčko Multi-Factor Authentication, zrušte jeho zaškrtnutí. 
6. Kliknutím na **vytvořit** uložte nový prostředek přihlašovacích údajů.

> [!NOTE]
> Azure Automation nepodporuje uživatelské účty, které používají službu Multi-Factor Authentication.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Vytvoření nového assetu přihlašovacích údajů pomocí Windows PowerShellu

Následující příklad ukazuje, jak vytvořit nový prostředek přihlašovacích údajů Automation. Nejprve `PSCredential` se vytvoří objekt s názvem a heslem a pak se použije k vytvoření assetu přihlašovacích údajů. Místo toho můžete pomocí `Get-Credential` rutiny vyzvat uživatele k zadání jména a hesla.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Použití přihlašovacích údajů prostředí PowerShell

Sada Runbook nebo konfigurace DSC načte Asset přihlašovacích údajů s `Get-AutomationPSCredential` aktivitou. Tato aktivita načte `PSCredential` objekt, který můžete použít s aktivitou nebo rutinou, která vyžaduje přihlašovací údaje. Můžete také načíst vlastnosti objektu přihlašovacích údajů, které se mají použít jednotlivě. Objekt má vlastnosti pro uživatelské jméno a zabezpečené heslo. Alternativně můžete použít metodu [GetNetworkCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0) k načtení objektu [NetworkCredential](/dotnet/api/system.net.networkcredential) , který představuje nezabezpečenou verzi hesla.

> [!NOTE]
> `Get-AzAutomationCredential`nenačte `PSCredential` objekt, který lze použít k ověřování. Poskytuje jenom informace o přihlašovacích údajích. Pokud potřebujete použít přihlašovací údaje v sadě Runbook, je nutné ji načíst jako `PSCredential` objekt pomocí. `Get-AutomationPSCredential`

### <a name="textual-runbook-example"></a>Příklad textového Runbooku

Následující příklad ukazuje způsob použití přihlašovacích údajů prostředí PowerShell v Runbooku. Načte přihlašovací údaje a přiřadí jí uživatelské jméno a heslo k proměnným.


```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Přihlašovací údaje můžete použít také k ověření v Azure pomocí [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0). Ve většině případů byste měli použít [účet Spustit jako](../manage-runas-account.md) a načíst připojení pomocí rutiny [Get-AzAutomationConnection](../automation-connections.md).


```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzAccount -Credential $myPsCred
```

### <a name="graphical-runbook-example"></a>Příklad grafického Runbooku

`Get-AutomationPSCredential` Aktivitu můžete přidat do grafického Runbooku kliknutím pravým tlačítkem na přihlašovací údaje v podokně Knihovna v grafickém editoru a výběrem možnosti **Přidat na plátno**.

![Přidat přihlašovací údaje na plátno](../media/credentials/credential-add-canvas.png)

Následující obrázek ukazuje příklad použití přihlašovacích údajů v grafickém Runbooku. V takovém případě přihlašovací údaje poskytují ověřování pro sadu Runbook prostředky Azure, jak je popsáno v tématu [použití Azure AD v Azure Automation k ověření v Azure](../automation-use-azure-ad.md). První aktivita načte přihlašovací údaje, které mají přístup k předplatnému Azure. Aktivita připojení k účtu pak pomocí těchto přihlašovacích údajů poskytuje ověřování pro všechny aktivity, které jsou po ní. [Odkaz na kanál](../automation-graphical-authoring-intro.md#links-and-workflow) se tady používá, `Get-AutomationPSCredential` protože očekává jeden objekt.  

![Přidat přihlašovací údaje na plátno](../media/credentials/get-credential.png)

## <a name="using-credentials-in-a-dsc-configuration"></a>Použití přihlašovacích údajů v konfiguraci DSC

I když konfigurace DSC v Azure Automation můžou pracovat s assety `Get-AutomationPSCredential`přihlašovacích údajů pomocí, můžou taky předávat assety přihlašovacích údajů prostřednictvím parametrů. Další informace najdete v tématu [kompilace konfigurací v Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python-2"></a>Použití přihlašovacích údajů v Pythonu 2

Následující příklad ukazuje příklad přístupu k přihlašovacím údajům v sadách Python 2 Runbooky.


```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Další kroky

* Další informace o propojeních v grafickém vytváření najdete v tématu [odkazy v grafickém vytváření](../automation-graphical-authoring-intro.md#links-and-workflow).
* Pro pochopení různých metod ověřování pro automatizaci si přečtěte téma [Azure Automation Security](../automation-security-overview.md).
* Informace o tom, jak začít s grafickými Runbooky, najdete v tématu [můj první grafický Runbook](../automation-first-runbook-graphical.md).
* Informace o tom, jak začít s Runbooky pracovních postupů PowerShellu najdete v tématu [můj první Runbook pracovního postupu PowerShellu](../automation-first-runbook-textual.md).
* Chcete-li začít s Runbooky Python 2, přečtěte si [můj první Runbook sady Python 2](../automation-first-runbook-textual-python2.md). 
