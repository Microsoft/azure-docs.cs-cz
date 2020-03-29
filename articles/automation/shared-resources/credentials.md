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
ms.openlocfilehash: 767c1fddbc3d1f46d4341a70c990c2b57ad40e54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252698"
---
# <a name="credential-assets-in-azure-automation"></a>Prostředky přihlašovacích údajů ve službě Azure Automation

Prostředek pověření automatizace obsahuje objekt, který obsahuje pověření zabezpečení, jako je uživatelské jméno a heslo. Sady Runbook a DSC konfigurace mohou používat rutiny, které přijímají objekt PSCredential pro ověřování, nebo mohou extrahovat uživatelské jméno a heslo objektu PSCredential, aby poskytly některé aplikace nebo služby vyžadující ověření. Vlastnosti pověření jsou bezpečně uloženy v Azure Automation a lze k nim přistupovat v konfiguraci runbook nebo DSC s [aktivitou Get-AutomationPSCredential.](#activities)

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Zabezpečené datové zdroje v Azure Automation zahrnují přihlašovací údaje, certifikáty, připojení a šifrované proměnné. Tyto prostředky jsou šifrované a uložené v Azure Automation pomocí jedinečného klíče, který se generuje pro každý účet automatizace. Tento klíč je uložen v trezoru klíčů. Před uložením zabezpečeného datového zdroje je klíč načten z trezoru klíčů a poté použit k šifrování datového zdroje.

## <a name="azure-powershell-az-cmdlets"></a>Rutiny Azure PowerShell Az

Pro modul Azure PowerShell Az se rutiny v následující tabulce používají k vytváření a správě prostředků pověření automatizace pomocí prostředí Windows PowerShell. Dodávají se jako součást [modulu AzureAz.Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0), který je k dispozici pro použití v runbookech automatizace a konfiguracích DSC.

| Rutiny | Popis |
|:--- |:--- |
| [Pověření Get-AzAutomation](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Načte informace o datovém zdroji pověření. To nevrátí objekt PSCredential.  |
| [Nové pověření AzAutomation](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Vytvoří nové pověření automatizace. |
| [Odebrat pověření AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Odebere pověření automatizace. |
| [Nastavení azautomationpověření](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Nastaví vlastnosti existujícího pověření automatizace. |

## <a name="activities"></a>Aktivity

Aktivity v následující tabulce se používají pro přístup k pověřením v konfiguracích runbooku a DSC.

| Aktivity | Popis |
|:--- |:--- |
| Get-AutomationPSCredential |Získá pověření pro použití v konfiguraci runbook nebo DSC. Vrátí objekt [System.Management.Automation.PSCredential.](/dotnet/api/system.management.automation.pscredential) |

> [!NOTE]
> Měli byste se vyhnout použití proměnných v parametru –Name get-AutomationPSCredential, protože to může zkomplikovat zjišťování závislostí mezi runbooky nebo konfiguracemi DSC a prostředky pověření v době návrhu.

## <a name="python2-functions"></a>Funkce Pythonu2

Funkce v následující tabulce se používá pro přístup k přihlašovacím údajům v runbooku Python2.

| Funkce | Popis |
|:---|:---|
| automationassets.get_automation_credential | Načte informace o datovém zdroji pověření. |

> [!NOTE]
> Chcete-li získat přístup k funkcím datového zdroje, musíte importovat modul "automationassets" v horní části sady Runbook pythonu.

## <a name="creating-a-new-credential-asset"></a>Vytvoření nového datového zdroje přihlašovacích údajů

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Vytvoření nového datového zdroje přihlašovacích údajů pomocí portálu Azure

1. V účtu automatizace vyberte v části **Sdílené prostředky** **položku Přihlašovací údaje** .
1. Vyberte **Přidat pověření**.
1. Vyplňte formulář a výběrem **možnosti Vytvořit** uložte nové přihlašovací údaje.

> [!NOTE]
> Uživatelské účty, které používají vícefaktorové ověřování, nejsou podporovány pro použití v Azure Automation.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Vytvoření nového datového zdroje přihlašovacích údajů pomocí prostředí Windows PowerShell

Následující ukázkové příkazy ukazují, jak vytvořit nové pověření automatizace. Objekt PSCredential je nejprve vytvořen s názvem a heslem a poté použit k vytvoření datového zdroje pověření. Případně můžete použít rutinu **Get-Credential,** abyste byli vyzváni k zadání jména a hesla.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Použití pověření Prostředí PowerShell

Načtete datový zdroj pověření v konfiguraci runbook nebo DSC s **aktivitou Get-AutomationPSCredential.** Vrátí to [objekt PSCredential,](/dotnet/api/system.management.automation.pscredential) který můžete použít s aktivitou nebo rutinou, která vyžaduje parametr PSCredential. Můžete také načíst vlastnosti objektu pověření pro použití jednotlivě. Objekt má vlastnost pro uživatelské jméno a zabezpečené heslo, nebo můžete použít **GetNetworkCredential** metoda vrátit [NetworkCredential](/dotnet/api/system.net.networkcredential) objekt, který bude poskytovat nezabezpečenou verzi hesla.

> [!NOTE]
> **Get-AzAutomationCredential** nevrátí **pscredential,** které lze použít pro ověřování. Poskytuje pouze informace o pověření. Pokud potřebujete použít pověření v runbooku, musíte použít **Get-AutomationPSCredential** k načtení objektu **PSCredential.**

### <a name="textual-runbook-sample"></a>Ukázka textového seznamu runbooků

Následující vzorové příkazy ukazují, jak použít přihlašovací údaje prostředí PowerShell v Runbooku. V tomto příkladu je načtenpověření a jeho uživatelské jméno a heslo přiřazené proměnným.

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Přihlašovací údaje můžete také použít k ověření v Azure pomocí [connect-azaccount](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0). Ve většině případů byste měli použít [účet Spustit jako](../manage-runas-account.md) a načíst jej pomocí [get-AzAutomationConnection](../automation-connections.md).

```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzureRmAccount -Credential $myPsCred
```

### <a name="graphical-runbook-sample"></a>Ukázka grafického seznamu runbooků

Aktivitu **Get-AutomationPSCredential** přidáte do grafického seznamu runbook kliknutím pravým tlačítkem myši na přihlašovací údaje v podokně Knihovna grafického editoru a výběrem **možnosti Přidat na plátno**.

![Přidání přihlašovacích údajů k plátnu](../media/credentials/credential-add-canvas.png)

Následující obrázek ukazuje příklad použití pověření v grafickém runbooku. V tomto případě se používá k poskytování ověřování pro runbook prostředkům Azure, jak je popsáno v [ověřování Runbooků s uživatelským účtem Azure AD](../automation-create-aduser-account.md). První aktivita načte přihlašovací údaje, které má přístup k předplatnému Azure. Aktivita **Connect-AzureRmAccount** pak používá toto pověření k zajištění ověřování pro všechny aktivity, které po ní přijdou. [Propojení kanálu](../automation-graphical-authoring-intro.md#links-and-workflow) je zde, protože **Get-AutomationPSCredential** očekává jeden objekt.  

![Přidání přihlašovacích údajů k plátnu](../media/credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Použití pověření prostředí PowerShell v DSC

Zatímco konfigurace DSC v Azure Automation můžete odkazovat na prostředky pověření pomocí **Get-AutomationPSCredential**, prostředky pověření lze také předat prostřednictvím parametrů, pokud chcete. Další informace naleznete [v tématu Kompilace konfigurací v Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Používání přihlašovacích údajů v Pythonu2

Následující ukázka ukazuje příklad přístupu k přihlašovacím údajům v runbookech Pythonu2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Další kroky

* Další informace o odkazech v grafickém vytváření najdete v [tématu Odkazy v grafickém vytváření](../automation-graphical-authoring-intro.md#links-and-workflow)
* Informace o různých metodách ověřování pomocí automatizace najdete v [tématu Azure Automation Security](../automation-security-overview.md)
* První kroky s grafickými runbooky najdete v článku [Můj první grafický runbook](../automation-first-runbook-graphical.md).
* Pokud chcete začít s runbooky pracovních postupů Prostředí PowerShell, [přečtěte si první runbook pracovního postupu PowerShellu.](../automation-first-runbook-textual.md)
* Informace o tom, jak začít s runbooky Pythonu2, najdete [v tématu Moje první runbook pythonu2](../automation-first-runbook-textual-python2.md) 
