---
title: Prostředků přihlašovacích údajů ve službě Azure Automation
description: Prostředků přihlašovacích údajů ve službě Azure Automation obsahovat zabezpečovací pověření, která můžete použít k ověřování k prostředkům přístup sada runbook nebo konfigurace DSC. Tento článek popisuje, jak vytvořit prostředků přihlašovacích údajů a použít je v sady runbook nebo konfigurace DSC.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: c56758012fc09287d0abb887db9148bd093a678a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="credential-assets-in-azure-automation"></a>Prostředků přihlašovacích údajů ve službě Azure Automation
Obsahuje prostředek přihlašovacích údajů automatizace [PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential) objekt, který obsahuje zabezpečovací přihlašovací údaje, jako je například uživatelské jméno a heslo. Konfigurace Runbooků a DSC může použít rutiny přijmout objekt PSCredential pro ověřování, nebo se může extrahuje uživatelské jméno a heslo objektu PSCredential zajistit některé aplikace nebo služby, které vyžadují ověřování. Vlastnosti přihlašovacích údajů jsou bezpečně uloženy ve službě Azure Automation a je přístupný v sada runbook nebo konfigurace DSC s [Get-AutomationPSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) aktivity.

>[!NOTE]
>Zabezpečené prostředky ve službě Azure Automation zahrnovat přihlašovací údaje, připojení, certifikátů a zašifrované proměnné. Tyto prostředky jsou zašifrovány a uložené ve službě Azure Automation pomocí jedinečný klíč, který se vygeneruje pro každý účet automation. Tento klíč je uložený v Key Vault. Před uložením o zabezpečený prostředek, je klíč načtený ze Key Vault a pak použije k zašifrování asset.

## <a name="azure-classic-powershell-cmdlets"></a>Rutiny Azure Classic PowerShell
Rutiny v následující tabulce se používají k vytváření a správě prostředků přihlašovacích údajů automatizace v prostředí Windows PowerShell.  Se dodávají jako součást [modul Azure PowerShell](/powershell/azure/overview) která je k dispozici pro použití v runbooků služeb automatizace a konfigurace DSC.

| Rutiny | Popis |
|:--- |:--- |
| [Get-AzureAutomationCredential](/powershell/module/azure/get-azureautomationcredential?view=azuresmps-3.7.0) |Načte informace o asset přihlašovacích údajů. Pouze můžete načíst přihlašovací údaje sám sebe z **Get-AutomationPSCredential** aktivity. |
| [New-AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Vytvoří nových přihlašovacích údajů automatizace. |
| [Remove - AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Odebere pověření automatizace. |
| [Set - AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Nastaví vlastnosti pro existující automatizace pověření. |

## <a name="azurerm-powershell-cmdlets"></a>Rutiny prostředí AzureRM PowerShell
Pro AzureRM rutiny v následující tabulce se používají k vytváření a správě prostředků přihlašovacích údajů automatizace v prostředí Windows PowerShell.  Se dodávají jako součást [AzureRM.Automation modulu](/powershell/azure/overview) která je k dispozici pro použití v runbooků služeb automatizace a konfigurace DSC.

| Rutiny | Popis |
|:--- |:--- |
| [Get-AzureRmAutomationCredential](/powershell/module/azurerm.automation/get-azurermautomationcredential?view=azurermps-4.4.0) |Načte informace o asset přihlašovacích údajů.  |
| [New-AzureRmAutomationCredential](/powershell/module/azurerm.automation/new-azurermautomationcredential?view=azurermps-4.4.0) |Vytvoří nových přihlašovacích údajů automatizace. |
| [Remove-AzureRmAutomationCredential](/powershell/module/azurerm.automation/remove-azurermautomationcredential?view=azurermps-4.4.0) |Odebere pověření automatizace. |
| [Set-AzureRmAutomationCredential](/powershell/module/azurerm.automation/set-azurermautomationcredential?view=azurermps-4.4.0) |Nastaví vlastnosti pro existující automatizace pověření. |

## <a name="activities"></a>Aktivity
Aktivity v následující tabulce se používají pro přístup k přihlašovací údaje v runbooku a konfigurace DSC.

| Aktivity | Popis |
|:--- |:--- |
| Get-AutomationPSCredential |Získá pověření pro použití v runbooku nebo konfigurace DSC. Vrátí [System.Management.Automation.PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential) objektu. |

> [!NOTE]
> Byste neměli používat proměnné v parametru – Name Get-AutomationPSCredential, protože to může zkomplikovat zjišťování závislostí mezi runbooky a konfigurace DSC a assety přihlašovacích údajů v době návrhu.

## <a name="python2-functions"></a>Funkce Python2
Funkce v následující tabulce slouží k přístupu k pověřením v sadě runbook Python2.

| Funkce | Popis |
|:---|:---|
| automationassets.get_automation_credential | Načte informace o asset přihlašovacích údajů. |

> [!NOTE]
> Chcete-li přístup funkce asset musí naimportovat modul "automationassets" v horní části runbookem Python.

## <a name="creating-a-new-credential-asset"></a>Vytvoření nového prostředku přihlašovacích údajů

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Chcete-li vytvořit nový prostředek přihlašovacích údajů pomocí portálu Azure
1. Z vašeho účtu automation, klikněte na tlačítko **prostředky** část otevřete **prostředky** okno.
2. Klikněte na tlačítko **pověření** část otevřete **pověření** okno.
3. Klikněte na tlačítko **přidat pověření** v horní části okna.
4. Vyplňte formulář a klikněte na tlačítko **vytvořit** pro uložení nových přihlašovacích údajů.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Chcete-li vytvořit nový prostředek přihlašovacích údajů v prostředí Windows PowerShell
Následující vzorové příkazy znázorňují postup vytvoření nových přihlašovacích údajů automatizace. Objekt PSCredential, je prvním vytvoření jméno a heslo a pak použít k vytvoření asset přihlašovacích údajů. Alternativně můžete použít **Get-Credential** rutiny se výzva k zadání jména a hesla.

    $user = "MyDomain\MyUser"
    $pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
    $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
    New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred

## <a name="using-a-powershell-credential"></a>Pomocí přihlašovacích údajů prostředí PowerShell
Získat prostředek přihlašovacích údajů v runbooku nebo konfigurace DSC s **Get-AutomationPSCredential** aktivity. Tento příkaz vrátí [objektu PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) , můžete použít s aktivitu nebo rutinu, která vyžaduje parametr PSCredential. Můžete také načíst vlastnosti objekt přihlašovacích údajů, který chcete použít jednotlivě. Objekt nemá vlastnost pro uživatelské jméno a heslo zabezpečené, nebo můžete použít **GetNetworkCredential** metoda vrátí [NetworkCredential](http://msdn.microsoft.com/library/system.net.networkcredential.aspx) objekt, který bude poskytovat zabezpečená verzi heslo.

### <a name="textual-runbook-sample"></a>Ukázkový textový
Následující vzorové příkazy znázorňují postup použití přihlašovacích údajů prostředí PowerShell v runbooku. V tomto příkladu se načte přihlašovací údaje a jeho uživatelské jméno a heslo přiřazené k proměnné.

    $myCredential = Get-AutomationPSCredential -Name 'MyCredential'
    $userName = $myCredential.UserName
    $securePassword = $myCredential.Password
    $password = $myCredential.GetNetworkCredential().Password


### <a name="graphical-runbook-sample"></a>Ukázka grafický runbook
Přidání **Get-AutomationPSCredential** aktivitu grafický runbook pravým tlačítkem myši na přihlašovací údaje v podokně knihovna grafického editoru a výběrem **přidat na plátno**.

![Přidat přihlašovací údaje na plátno](media/automation-credentials/credential-add-canvas.png)

Následující obrázek ukazuje příklad použití pověření v grafický runbook.  V takovém případě se používá k ověření pro sady runbook a prostředky Azure, jak je popsáno v [ověření Runbooků pomocí účtu uživatele Azure AD](automation-create-aduser-account.md).  První aktivitu načte přihlašovacích údajů, který má přístup k předplatnému Azure.  **Add-AzureAccount** aktivita pak používá toto pověření k ověřování pro všechny aktivity, které následují po.  A [propojení kanálu](automation-graphical-authoring-intro.md#links-and-workflow) se zde od **Get-AutomationPSCredential** je očekáván jeden objekt.  

![Přidat přihlašovací údaje na plátno](media/automation-credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Pomocí přihlašovacích údajů prostředí PowerShell v DSC
Během konfigurace DSC ve službě Azure Automation, můžete odkazovat pomocí prostředků přihlašovacích údajů **Get-AutomationPSCredential**, prostředků přihlašovacích údajů můžete také být předán prostřednictvím parametrů, v případě potřeby. Další informace najdete v tématu [kompilování konfigurace v Azure Automation DSC](automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Pomocí přihlašovacích údajů v Python2
Následující příklad ukazuje příklad přihlašovací údaje v sadách runbook Python2 přístup.

    import automationassets
    from automationassets import AutomationAssetNotFound

    # get a credential
    cred = automationassets.get_automation_credential("credtest")
    print cred["username"]
    print cred["password"]

## <a name="next-steps"></a>Další kroky
* Další informace o propojení v vytváření grafického obsahu najdete v tématu [odkazy v vytváření grafického obsahu](automation-graphical-authoring-intro.md#links-and-workflow)
* Chcete-li pochopit různé metody ověřování pomocí automatizace, přečtěte si téma [zabezpečení automatizace Azure](automation-security-overview.md)
* První kroky s grafickými runbooky najdete v článku [Můj první grafický runbook](automation-first-runbook-graphical.md).
* První kroky s runbooky pracovních postupů PowerShellu najdete v článku [Můj první runbook pracovního postupu PowerShellu](automation-first-runbook-textual.md). 
* Kroky s runbooky Python2, najdete v tématu [Můj první runbook Python2](automation-first-runbook-textual-python2.md) 

