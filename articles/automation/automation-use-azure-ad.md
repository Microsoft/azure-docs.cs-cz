---
title: Použití Azure AD v Azure Automation k ověření do Azure
description: Zjistěte, jak používat Azure AD v rámci Azure Automation jako zprostředkovatele pro ověřování do Azure.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 77476c67761a950430b39d5baddf2c6efd77f1a2
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80479445"
---
# <a name="use-azure-ad-in-azure-automation-to-authenticate-to-azure"></a>Použití Azure AD v Azure Automation k ověření do Azure

Služba [Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) umožňuje řadu úloh správy, jako je správa uživatelů, správa domén a konfigurace jednotného přihlašování. Tento článek popisuje, jak používat Azure AD v rámci Azure Automation jako zprostředkovatele pro ověřování do Azure. 

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="installing-azure-ad-modules"></a>Instalace modulů Azure AD

Azure AD můžete povolit prostřednictvím následujících modulů Prostředí PowerShell:

* Azure Active Directory PowerShell pro graf (moduly AzureRM a Az). Azure Automation je dodáván s modulem AzureRM a jeho nedávným upgradem, modulem Az. Funkce zahrnují neinteraktivní ověřování do Azure pomocí ověřování na základě přihlašovacích údajů uživatelů Azure AD (OrgId). Viz [Azure AD 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76).

* Microsoft Azure Active Directory pro Windows PowerShell (modul MSOnline). Tento modul umožňuje interakci se službou Microsoft Online, včetně Office 365.

>[!NOTE]
>Core prostředí PowerShell nepodporuje modul MSOnline. Chcete-li použít rutiny modulu, je nutné je spustit z prostředí Windows PowerShell. Doporučujeme použít novější moduly Azure Active Directory PowerShell pro graf y místo modulu MSOnline. 

### <a name="preinstallation"></a>Předinstalace

Před instalací modulů Azure AD do počítače:

* Odinstalujte všechny předchozí verze modulu AzureRM/Az a modulu MSOnline. 

* Odinstalujte Pomocníka při přihlašování ke službám Microsoft Online Services, abyste zajistili správnou funkci nových modulů prostředí PowerShell.  

### <a name="install-the-azurerm-and-az-modules"></a>Instalace modulů AzureRM a Az

>[!NOTE]
>Chcete-li pracovat s těmito moduly, musíte použít Prostředí PowerShell verze 5.1 nebo novější s 64bitovou verzí systému Windows. 

1. Nainstalujte rozhraní WMF (Windows Management Framework) 5.1. Viz [Instalace a konfigurace wmf 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure?view=powershell-7).

2. Nainstalujte AzureRM a/nebo Az podle pokynů v [části Instalace Azure PowerShellu v systému Windows pomocí PowerShellGet](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0).

### <a name="install-the-msonline-module"></a>Instalace modulu MSOnline

>[!NOTE]
>Pokud chcete nainstalovat modul MSOnline, musíte být členem role správce Office 365. Viz [Informace o rolích správců](https://docs.microsoft.com/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide).

1. Zkontrolujte, zda je v počítači povolena funkce rozhraní Microsoft .NET Framework 3.5.x. Je pravděpodobné, že váš počítač má nainstalovanou novější verzi, ale zpětná kompatibilita se staršími verzemi rozhraní .NET Framework může být povolena nebo zakázána. 

2. Nainstalujte 64bitovou verzi [Pomocníka přihlašování ke službám Microsoft Online Services](https://www.microsoft.com/download/details.aspx?id=41950).

3. Spusťte prostředí Windows PowerShell jako správce a vytvořte příkazový řádek prostředí Windows PowerShell se zvýšenými oprávněními.

4. Nasazení služby Azure Active Directory z [msonline 1.0](http://www.powershellgallery.com/packages/MSOnline/1.0).

5. Pokud se zobrazí výzva k instalaci zprostředkovatele NuGet, zadejte Y a stiskněte klávesu ENTER.

6. Pokud se zobrazí výzva k instalaci modulu z [PSGallery](https://www.powershellgallery.com/), zadejte Y a stiskněte klávesu ENTER.

### <a name="install-support-for-pscredential"></a>Instalace podpory pro pscredential

Azure Automation používá [pscredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) třídy představují prostředek pověření. Skripty načítají `PSCredential` objekty pomocí rutiny. `Get-AutomationPSCredential` Další informace najdete [v tématu Prostředky pověření v Azure Automation](shared-resources/credentials.md).

## <a name="assigning-a-subscription-administrator"></a>Přiřazení správce předplatného

Pro předplatné Azure musíte přiřadit správce. Tato osoba má roli vlastníka pro obor předplatného. Viz [Řízení přístupu na základě rolí v Azure Automation](automation-role-based-access-control.md). 

## <a name="changing-the-azure-ad-users-password"></a>Změna hesla uživatele služby Azure AD

Změna hesla uživatele služby Azure AD:

1. Odhlaste se z Azure.

2. Nechte správce přihlásit se k Azure jako uživatel Azure AD právě vytvořil, pomocí úplné uživatelské jméno (včetně domény) a dočasné heslo. 

3. Požádejte správce, aby po zobrazení výzvy změnil heslo.

## <a name="configuring-azure-automation-to-use-the-azure-ad-user-to-manage-the-azure-subscription"></a>Konfigurace Azure Automation tak, aby uživatel Azure AD používal ke správě předplatného Azure

Pro Azure Automation ke komunikaci s Azure AD, musíte načíst přihlašovací údaje spojené s připojením Azure k Azure AD. Příklady těchto přihlašovacích údajů jsou ID klienta, ID předplatného a podobně. Další informace o připojení mezi Azure a Azure AD najdete v [tématu Připojení vaší organizace k Azure Active Directory](https://docs.microsoft.com/azure/devops/organizations/accounts/connect-organization-to-azure-ad?view=azure-devops).

## <a name="creating-a-credential-asset"></a>Vytvoření datového zdroje pověření

Když jsou k dispozici přihlašovací údaje Azure pro Azure AD, je čas vytvořit datový zdroj přihlašovacích údajů Azure Automation pro bezpečné ukládání přihlašovacích údajů Azure AD, aby k nim měly přístup runbooky a skripty DSC (Desire State Configuration). Můžete to provést pomocí portálu Azure nebo rutin prostředí PowerShell.

### <a name="create-the-credential-asset-in-azure-portal"></a>Vytvoření datového zdroje přihlašovacích údajů na webu Azure Portal

K vytvoření datového zdroje přihlašovacích údajů můžete použít portál Azure. Proveďte tuto operaci z účtu Automatizace pomocí **přihlašovacích údajů** v části **Sdílené prostředky**. Viz [Prostředky pověření v Azure Automation](shared-resources/credentials.md).

### <a name="create-the-credential-asset-with-windows-powershell"></a>Vytvoření datového zdroje přihlašovacích údajů pomocí prostředí Windows PowerShell

Chcete-li připravit nový datový zdroj pověření v `PSCredential` prostředí Windows PowerShell, skript nejprve vytvoří objekt pomocí přiřazeného uživatelského jména a hesla. Skript pak používá tento objekt k vytvoření datového zdroje prostřednictvím volání rutiny [New-AzureAutomationCredential.](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) Případně skript může volat [rutinu Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) a vyzvat uživatele k zadání jména a hesla. Viz [Prostředky pověření v Azure Automation](shared-resources/credentials.md). 

## <a name="managing-azure-resources-from-an-azure-automation-runbook"></a>Správa prostředků Azure z runbooku Azure Automation

Prostředky Azure můžete spravovat z runbooků Azure Automation pomocí datového zdroje přihlašovacích údajů. Níže je uveden příklad runbooku Prostředí PowerShell, který shromažďuje datový zdroj přihlašovacích údajů, který se má použít pro zastavení a spuštění virtuálních počítačů v předplatném Azure. Tato runbook `Get-AutomationPSCredential` nejprve používá k načtení přihlašovací údaje pro ověření do Azure. Potom volá [rutinu Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.6.1) pro připojení k Azure pomocí pověření. Skript používá [rutinu Select-AzureSubscription](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0) k výběru předplatného, se kterým chcete pracovat. 

```azurepowershell
Workflow Stop-Start-AzureVM 
{ 
    Param 
    (    
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureSubscriptionId, 
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureVMList="All", 
        [Parameter(Mandatory=$true)][ValidateSet("Start","Stop")] 
        [String] 
        $Action 
    ) 
     
    $credential = Get-AutomationPSCredential -Name 'AzureCredential' 
    Connect-AzAccount -Credential $credential 
    Select-AzureSubscription -SubscriptionId $AzureSubscriptionId 
 
    if($AzureVMList -ne "All") 
    { 
        $AzureVMs = $AzureVMList.Split(",") 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
    } 
    else 
    { 
        $AzureVMs = (Get-AzVM).Name 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
 
    } 
 
    foreach($AzureVM in $AzureVMsToHandle) 
    { 
        if(!(Get-AzVM | ? {$_.Name -eq $AzureVM})) 
        { 
            throw " AzureVM : [$AzureVM] - Does not exist! - Check your inputs " 
        } 
    } 
 
    if($Action -eq "Stop") 
    { 
        Write-Output "Stopping VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Stop-AzVM -Force 
        } 
    } 
    else 
    { 
        Write-Output "Starting VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Start-AzVM 
        } 
    } 
}
```  

## <a name="next-steps"></a>Další kroky

* Informace o datových prostředkůch pověření automatizace najdete v [datových prostředkůch pověření v Azure Automation](shared-resources/credentials.md).
* Informace o tom, jak pracovat s moduly automatizace, najdete [v tématu Správa modulů v Azure Automation.](shared-resources/modules.md)
* Další informace o metodách, které se dá použít ke spuštění sady Runbook v Azure Automation, najdete v [tématu Spuštění runbooku v Azure Automation](automation-starting-a-runbook.md).
* Další informace o Prostředí PowerShell, včetně jazykových referenčních a výukových modulů, najdete v [tématu Dokumenty prostředí PowerShell](https://docs.microsoft.com/powershell/scripting/overview).