---
title: Prostředky přihlašovacích údajů ve službě Azure Automation
description: Prostředky přihlašovacích údajů v Azure Automation obsahují pověření zabezpečení, které lze použít k ověření prostředků přístupných pomocí konfigurace sady Runbook nebo DSC. Tento článek popisuje, jak vytvořit datové zdroje přihlašovacích údajů a použít je v konfiguraci runbooku nebo DSC.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c8b63a2676690004d23094b490fea0ef150ab9cb
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546415"
---
# <a name="credential-assets-in-azure-automation"></a>Prostředky přihlašovacích údajů ve službě Azure Automation

Prostředek pověření automatizace obsahuje objekt, který obsahuje pověření zabezpečení, jako je například uživatelské jméno a heslo. Sady Runbook a DSC konfigurace používají rutiny, které přijímají objekt [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) pro ověřování. Alternativně mohou extrahovat uživatelské jméno `PSCredential` a heslo objektu poskytnout některé aplikace nebo služby, které vyžadují ověření. 

Azure Automation bezpečně ukládá vlastnosti pověření. Přístup k vlastnostem prostřednictvím runbooku nebo konfigurace DSC používá [get-AutomationPScredential](#activities-used-to-access-credentials) aktivity.

> [!NOTE]
> Zabezpečené datové zdroje v Azure Automation zahrnují přihlašovací údaje, certifikáty, připojení a šifrované proměnné. Tyto prostředky jsou šifrované a uložené v Azure Automation pomocí jedinečného klíče, který se generuje pro každý účet automatizace. Tento klíč je uložen v trezoru klíčů. Před uložením zabezpečeného datového zdroje je klíč načten z trezoru klíčů a poté použit k šifrování datového zdroje.

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="azure-powershell-az-cmdlets-used-for-credential-assets"></a>Rutiny Azure PowerShell Az používané pro datové zdroje přihlašovacích údajů

Jako součást modulu Azure PowerShell Az se rutiny v následující tabulce používají k vytváření a správě prostředků pověření automatizace pomocí prostředí Windows PowerShell. Jsou dodávány v [modulu Az.Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0), který je k dispozici pro použití v runbookech automatizace a konfiguracích DSC. Viz [Podpora modulů Az v Azure Automation](https://docs.microsoft.com/azure/automation/az-modules).

| Rutina | Popis |
|:--- |:--- |
| [Pověření Get-AzAutomation](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Načte informace o datovém zdroji pověření. Tato rutina nevrátí `PSCredential` objekt.  |
| [Nové pověření AzAutomation](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Vytvoří nové pověření automatizace. |
| [Odebrat pověření AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Odebere pověření automatizace. |
| [Nastavení azautomationpověření](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Nastaví vlastnosti existujícího pověření automatizace. |

## <a name="activities-used-to-access-credentials"></a>Aktivity používané k přístupu k přihlašovacím údajům

Aktivity v následující tabulce se používají pro přístup k pověření mj.

| Aktivita | Popis |
|:--- |:--- |
| `Get-AutomationPSCredential` |Získá pověření pro použití v konfiguraci runbook nebo DSC. Pověření je ve formě `PSCredential` objektu. |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |Získá pověření s výzvou pro uživatelské jméno a heslo. |
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | Vytvoří datový zdroj pověření. |

Pro místní vývoj pomocí Azure Automation Authoring `Get-AutomationPSCredential` Toolkit je rutina součástí sestavení [AzureAutomationAuthoringToolkit](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9). Pro Azure práci s kontextem automatizace `Orchestrator.AssetManagement.Cmdlets`rutina je v . Viz [Správa modulů v Azure Automation](modules.md).

Chcete-li načíst `PSCredential` objekty v kódu, můžete nainstalovat [doplněk Microsoft Azure Automation ISE pro prostředí PowerShell ISE](https://github.com/azureautomation/azure-automation-ise-addon).

```azurepowershell
Install-Module AzureAutomationAuthoringToolkit -Scope CurrentUser -Force
```

Skript můžete také importovat požadovaný modul v případě potřeby, jako v následujícím příkladu: 

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> Měli byste se vyhnout `Name` použití `Get-AutomationPSCredential`proměnných v parametru . Jejich použití může komplikovat zjišťování závislostí mezi runbooky nebo konfiguracemi DSC a prostředky pověření v době návrhu.

## <a name="python2-functions-that-access-credentials"></a>Funkce Pythonu2, které přistupují k přihlašovacím údajům

Funkce v následující tabulce se používá pro přístup k přihlašovacím údajům v runbooku Python2.

| Funkce | Popis |
|:---|:---|
| `automationassets.get_automation_credential` | Načte informace o datovém zdroji pověření. |

> [!NOTE]
> Importujte `automationassets` modul v horní části sady runbook pythonu pro přístup k funkcím datových zdrojů.

## <a name="creating-a-new-credential-asset"></a>Vytvoření nového datového zdroje přihlašovacích údajů

Nový datový zdroj přihlašovacích údajů můžete vytvořit pomocí portálu Azure nebo pomocí prostředí Windows PowerShell.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Vytvoření nového datového zdroje přihlašovacích údajů pomocí portálu Azure

1. V části Sdílené prostředky vyberte v části **Sdílené prostředky**možnost **Pověření** .
1. Vyberte **Přidat pověření**.
2. V podokně Nové pověření zadejte příslušný název pověření podle standardů pro pojmenování. 
3. Do pole **Uživatelské jméno** zadejte ID přístupu. 
4. Pro obě pole hesla zadejte tajný přístupový klíč.

    ![Vytvořit nové pověření](../media/credentials/credential-create.png)

5. Pokud je políčko vícefaktorového ověřování zaškrtnuté, odškrtnete ho. 
6. Kliknutím na **Vytvořit** uložte nový datový zdroj přihlašovacích údajů.

> [!NOTE]
> Azure Automation nepodporuje uživatelské účty, které používají vícefaktorové ověřování.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Vytvoření nového datového zdroje přihlašovacích údajů pomocí prostředí Windows PowerShell

Následující příklad ukazuje, jak vytvořit nový prostředek pověření automatizace. Objekt `PSCredential` je nejprve vytvořen s názvem a heslem a poté použit k vytvoření datového zdroje pověření. Místo toho můžete `Get-Credential` pomocí rutiny vyzvat uživatele k zadání jména a hesla.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Použití pověření Prostředí PowerShell

Konfigurace runbooku nebo DSC načte `Get-AutomationPSCredential` datový zdroj pověření s aktivitou. Tato aktivita `PSCredential` načte objekt, který můžete použít s aktivitou nebo rutinou, která vyžaduje pověření. Můžete také načíst vlastnosti objektu pověření pro použití jednotlivě. Objekt má vlastnosti pro uživatelské jméno a zabezpečené heslo. Alternativně můžete použít [GetNetworkCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0) metoda načíst [NetworkCredential](/dotnet/api/system.net.networkcredential) objekt, který představuje nezabezpečenou verzi hesla.

> [!NOTE]
> `Get-AzAutomationCredential`nenačte `PSCredential` objekt, který lze použít k ověřování. Poskytuje pouze informace o pověření. Pokud potřebujete použít pověření v runbooku, musíte je `PSCredential` načíst jako objekt pomocí `Get-AutomationPSCredential`.

### <a name="textual-runbook-example"></a>Textový příklad runbooku

Následující příklad ukazuje, jak používat pověření prostředí PowerShell v runbooku. Načte pověření a přiřadí své uživatelské jméno a heslo proměnným.


```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Přihlašovací údaje můžete také použít k ověření v Azure pomocí [connect-azaccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0). Ve většině případů byste měli použít [účet Spustit jako](../manage-runas-account.md) a načíst připojení s [Get-AzAutomationConnection](../automation-connections.md).


```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzAccount -Credential $myPsCred
```

### <a name="graphical-runbook-example"></a>Příklad grafického seznamu runbook

Aktivitu `Get-AutomationPSCredential` můžete přidat do grafického runbooku kliknutím pravým tlačítkem myši na přihlašovací údaje v podokně Knihovna grafického editoru a výběrem **možnosti Přidat na plátno**.

![Přidání přihlašovacích údajů k plátnu](../media/credentials/credential-add-canvas.png)

Následující obrázek ukazuje příklad použití pověření v grafickém runbooku. V takovém případě pověření poskytuje ověřování pro runbook na prostředky Azure, jak je popsáno v [použití Azure AD v Azure Automation k ověření azure](../automation-use-azure-ad.md). První aktivita načte přihlašovací údaje, které má přístup k předplatnému Azure. Aktivita připojení účtu pak používá toto pověření k zajištění ověřování pro všechny aktivity, které přijdou po něm. Odkaz [kanálu](../automation-graphical-authoring-intro.md#links-and-workflow) se zde `Get-AutomationPSCredential` používá, protože očekává jeden objekt.  

![Přidání přihlašovacích údajů k plátnu](../media/credentials/get-credential.png)

## <a name="using-credentials-in-a-dsc-configuration"></a>Použití pověření v konfiguraci DSC

Zatímco konfigurace DSC v Azure Automation můžou pracovat s prostředky pověření pomocí `Get-AutomationPSCredential`, můžou taky předávat prostředky pověření prostřednictvím parametrů. Další informace naleznete [v tématu Kompilace konfigurací v Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Používání přihlašovacích údajů v Pythonu2

Následující příklad ukazuje příklad přístupu k přihlašovacím údajům v runbookech Pythonu2.


```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Další kroky

* Další informace o odkazech v grafickém vytváření najdete v [tématu Odkazy v grafickém vytváření](../automation-graphical-authoring-intro.md#links-and-workflow).
* Informace o různých metodách ověřování pro automatizaci najdete v [tématu Azure Automation Security](../automation-security-overview.md).
* Informace o tom, jak začít s grafickými runbooky, najdete [v tématu Moje první grafická kniha runbook](../automation-first-runbook-graphical.md).
* Pokud chcete začít s runbooky pracovních postupů Prostředí PowerShell, [přečtěte si první runbook pracovního postupu PowerShellu](../automation-first-runbook-textual.md).
* První kroky s runbooky Python2 najdete v článku [Můj první runbook Python2](../automation-first-runbook-textual-python2.md). 
