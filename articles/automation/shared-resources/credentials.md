---
title: Správa přihlašovacích údajů ve službě Automation
description: V tomto článku se dozvíte, jak vytvořit assety přihlašovacích údajů a použít je v sadě Runbook nebo konfiguraci DSC.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: caaeb0e40d277ef5e356c0f385a818b831326d6e
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734823"
---
# <a name="manage-credentials-in-azure-automation"></a>Správa přihlašovacích údajů ve službě Automation

Asset přihlašovacích údajů pro automatizaci obsahuje objekt, který obsahuje zabezpečovací pověření, například uživatelské jméno a heslo. Runbooky a konfigurace DSC používají rutiny, které přijímají pro ověřování objekt [PSCredential](/dotnet/api/system.management.automation.pscredential) . Případně mohou extrahovat uživatelské jméno a heslo `PSCredential` objektu, které mají být k dispozici pro některé aplikace nebo služby vyžadující ověřování.

>[!NOTE]
>Zabezpečené prostředky v Azure Automation zahrnují přihlašovací údaje, certifikáty, připojení a šifrované proměnné. Tyto prostředky jsou zašifrované a uložené v Azure Automation pomocí jedinečného klíče, který se generuje pro každý účet Automation. Azure Automation ukládá klíč do Key Vault spravovaném systémem. Před uložením zabezpečeného assetu Automation načte klíč z Key Vault a pak ho použije k zašifrování prostředku. 

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="powershell-cmdlets-used-to-access-credentials"></a>Rutiny PowerShellu používané pro přístup k přihlašovacím údajům

Rutiny v následující tabulce vytvářejí a spravují přihlašovací údaje automatizace pomocí PowerShellu. Dodávají se jako součást [AZ moduls](modules.md#az-modules).

| Rutina | Popis |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential) |Načte objekt [CredentialInfo](/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo) obsahující metadata o přihlašovacích údajích. Rutina nenačte `PSCredential` samotný objekt.  |
| [New-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential) |Vytvoří nové přihlašovací údaje automatizace. |
| [Remove-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential) |Odebere přihlašovací údaje automatizace. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential) |Nastaví vlastnosti pro existující přihlašovací údaje automatizace. |

## <a name="other-cmdlets-used-to-access-credentials"></a>Další rutiny používané pro přístup k přihlašovacím údajům

Rutiny v následující tabulce se používají pro přístup k přihlašovacím údajům v sadách Runbook a konfiguracích DSC. 

| Rutina | Popis |
|:--- |:--- |
| `Get-AutomationPSCredential` |Získá `PSCredential` objekt, který se má použít v sadě Runbook nebo konfiguraci DSC. Nejčastěji byste měli místo rutiny použít tuto [interní rutinu](modules.md#internal-cmdlets) `Get-AzAutomationCredential` , protože ta načte jenom informace o přihlašovacích údajích. Tyto informace nejsou obvykle užitečné k předání jiné rutině. |
| [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) |Získá přihlašovací údaje s výzvou k zadání uživatelského jména a hesla. Tato rutina je součástí výchozího modulu Microsoft. PowerShell. Security. Viz [výchozí moduly](modules.md#default-modules).|
| [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential) | Vytvoří Asset přihlašovacích údajů. Tato rutina je součástí výchozího modulu Azure. Viz [výchozí moduly](modules.md#default-modules).|

Chcete-li načíst `PSCredential` objekty v kódu, musíte importovat `Orchestrator.AssetManagement.Cmdlets` modul. Další informace najdete v tématu [Správa modulů v Azure Automation](modules.md).

```powershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> Nepoužívejte proměnné v `Name` parametru `Get-AutomationPSCredential` . Jejich použití může zkomplikovat zjišťování závislostí mezi sadami Runbook a konfigurací DSC a assety přihlašovacích údajů v době návrhu.

## <a name="python-functions-that-access-credentials"></a>Python – funkce pro přístup k přihlašovacím údajům

Funkce v následující tabulce slouží k přístupu k přihlašovacím údajům v sadě Runbook Python 2 a 3. Sady Python 3 Runbooky jsou momentálně ve verzi Preview.

| Funkce | Popis |
|:---|:---|
| `automationassets.get_automation_credential` | Načte informace o assetu přihlašovacích údajů. |

> [!NOTE]
> Importujte `automationassets` modul v horní části Runbooku Pythonu, abyste měli přístup k funkcím assetu.

## <a name="create-a-new-credential-asset"></a>Vytvořit nový prostředek přihlašovacích údajů

Pomocí Azure Portal nebo Windows PowerShellu můžete vytvořit nový prostředek přihlašovacích údajů.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Vytvoření nového assetu přihlašovacích údajů pomocí Azure Portal

1. Z účtu Automation v levém podokně vyberte **přihlašovací údaje** v části **sdílené prostředky**.
2. Na stránce **pověření** vyberte **Přidat pověření**.
3. V podokně nové přihlašovací údaje zadejte odpovídající název přihlašovacích údajů podle standardů pojmenování.
4. Do pole **uživatelské jméno** zadejte své přístupové ID.
5. Pro obě pole hesla zadejte svůj tajný přístupový klíč.

    ![Vytvořit nové přihlašovací údaje](../media/credentials/credential-create.png)

6. Pokud je zaškrtnuto políčko Multi-Factor Authentication, zrušte jeho zaškrtnutí.
7. Kliknutím na **vytvořit** uložte nový prostředek přihlašovacích údajů.

> [!NOTE]
> Azure Automation nepodporuje uživatelské účty, které používají službu Multi-Factor Authentication.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Vytvoření nového assetu přihlašovacích údajů pomocí Windows PowerShellu

Následující příklad ukazuje, jak vytvořit nový prostředek přihlašovacích údajů Automation. `PSCredential`Nejprve se vytvoří objekt s názvem a heslem a pak se použije k vytvoření assetu přihlašovacích údajů. Místo toho můžete pomocí `Get-Credential` rutiny vyzvat uživatele k zadání jména a hesla.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="get-a-credential-asset"></a>Získání prostředku přihlašovacích údajů

Sada Runbook nebo konfigurace DSC načte Asset přihlašovacích údajů pomocí interní `Get-AutomationPSCredential` rutiny. Tato rutina načte `PSCredential` objekt, který můžete použít s rutinou, která vyžaduje přihlašovací údaje. Můžete také načíst vlastnosti objektu přihlašovacích údajů, které se mají použít jednotlivě. Objekt má vlastnosti pro uživatelské jméno a zabezpečené heslo. 

> [!NOTE]
> `Get-AzAutomationCredential`Rutina nenačte `PSCredential` objekt, který lze použít k ověřování. Poskytuje jenom informace o přihlašovacích údajích. Pokud potřebujete použít přihlašovací údaje v sadě Runbook, je nutné ji načíst jako `PSCredential` objekt pomocí `Get-AutomationPSCredential` .

Alternativně můžete použít metodu [GetNetworkCredential](/dotnet/api/system.management.automation.pscredential.getnetworkcredential) k načtení objektu [NetworkCredential](/dotnet/api/system.net.networkcredential) , který představuje nezabezpečenou verzi hesla.

### <a name="textual-runbook-example"></a>Příklad textového Runbooku

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Následující příklad ukazuje způsob použití přihlašovacích údajů prostředí PowerShell v Runbooku. Načte přihlašovací údaje a přiřadí jí uživatelské jméno a heslo k proměnným.

```powershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Přihlašovací údaje můžete použít také k ověření v Azure pomocí [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Ve většině případů byste měli použít [účet Spustit jako](../manage-runas-account.md) a načíst připojení pomocí rutiny [Get-AzAutomationConnection](../automation-connections.md).

```powershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$securePassword)

Connect-AzAccount -Credential $myPsCred
```

# <a name="python-2"></a>[Python 2](#tab/python2)

Následující příklad ukazuje příklad přístupu k přihlašovacím údajům v sadách Python 2 Runbooky.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

# <a name="python-3"></a>[Python 3](#tab/python3)

Následující příklad ukazuje příklad přístupu k přihlašovacím údajům v Python 3 Runbooky (Preview).

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print (cred["username"])
print (cred["password"])
```

---

### <a name="graphical-runbook-example"></a>Příklad grafického Runbooku

Aktivitu pro interní `Get-AutomationPSCredential` rutinu můžete do grafického Runbooku přidat tak, že kliknete pravým tlačítkem na přihlašovací údaje v podokně Knihovna v grafickém editoru a vyberete **Přidat na plátno**.

![Přidat rutinu přihlašovacích údajů na plátno](../media/credentials/credential-add-canvas.png)

Následující obrázek ukazuje příklad použití přihlašovacích údajů v grafickém Runbooku. V takovém případě přihlašovací údaje poskytují ověřování pro sadu Runbook prostředky Azure, jak je popsáno v tématu [použití Azure AD v Azure Automation k ověření v Azure](../automation-use-azure-ad.md). První aktivita načte přihlašovací údaje, které mají přístup k předplatnému Azure. Aktivita připojení k účtu pak pomocí těchto přihlašovacích údajů poskytuje ověřování pro všechny aktivity, které jsou po ní. [Odkaz na kanál](../automation-graphical-authoring-intro.md#use-links-for-workflow) se tady používá, protože `Get-AutomationPSCredential` očekává jeden objekt.  

![Příklad pracovního postupu pověření s odkazem na kanál](../media/credentials/get-credential.png)

## <a name="use-credentials-in-a-dsc-configuration"></a>Použití přihlašovacích údajů v konfiguraci DSC

I když konfigurace DSC v Azure Automation můžou pracovat s assety přihlašovacích údajů pomocí `Get-AutomationPSCredential` , můžou taky předávat assety přihlašovacích údajů prostřednictvím parametrů. Další informace najdete v tématu [kompilace konfigurací v Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="next-steps"></a>Další kroky

* Další informace o rutinách používaných pro přístup k certifikátům najdete v tématu [Správa modulů v Azure Automation](modules.md).
* Obecné informace o sadách Runbook naleznete [v tématu Spuštění Runbooku v Azure Automation](../automation-runbook-execution.md).
* Podrobnosti o konfiguracích DSC najdete v tématu [Přehled konfigurace stavu Azure Automation](../automation-dsc-overview.md).