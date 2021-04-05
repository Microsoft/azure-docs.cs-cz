---
title: Použití Azure AD ve službě Azure Automation pro ověření do Azure
description: V tomto článku se dozvíte, jak používat Azure AD v rámci Azure Automation jako poskytovatele pro ověřování do Azure.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 336c0387ac9febcc517c2ce358d0b04c80d10678
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99576799"
---
# <a name="use-azure-ad-to-authenticate-to-azure"></a>Použití Azure AD k ověření v Azure

Služba [Azure Active Directory (AD)](../active-directory/fundamentals/active-directory-whatis.md) umožňuje řadu úloh správy, jako je Správa uživatelů, Správa domén a konfigurace jednotného přihlašování. Tento článek popisuje, jak používat Azure AD v rámci Azure Automation jako poskytovatele pro ověřování do Azure. 

## <a name="install-azure-ad-modules"></a>Nainstalovat moduly Azure AD

Službu Azure AD můžete povolit prostřednictvím následujících modulů PowerShellu:

* Azure Active Directory PowerShell pro Graph (AzureRM and AZ moduls). Azure Automation se dodává s modulem AzureRM a jeho nedávným upgradem, a to pomocí modulu AZ Module. Funkce zahrnují neinteraktivní ověřování do Azure pomocí ověřování na základě přihlašovacích údajů uživatelů Azure AD (OrgId). Viz [Azure AD 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76).

* Microsoft Azure Active Directory prostředí Windows PowerShell (modul MSOnline). Tento modul umožňuje spolupráci s Microsoft Online, včetně Microsoft 365.

>[!NOTE]
>PowerShell Core nepodporuje modul MSOnline. Pokud chcete použít rutiny modulu, musíte je spustit z Windows PowerShellu. Doporučujeme místo modulu MSOnline používat novější Azure Active Directory PowerShellu pro moduly grafů. 

### <a name="preinstallation"></a>Preinstallation

Předtím, než na svém počítači nainstalujete moduly Azure AD:

* Odinstalujte všechny předchozí verze modulu AzureRM/AZ a modulu MSOnline. 

* Odinstalujte pomocníka Sign-In služeb Microsoft Online Services, aby bylo zajištěno správné fungování nových modulů prostředí PowerShell.  

### <a name="install-the-azurerm-and-az-modules"></a>Instalace AzureRM a AZ modules

>[!NOTE]
>Pokud chcete s těmito moduly pracovat, musíte použít PowerShell verze 5,1 nebo novější s 64 verzí Windows. 

1. Nainstalujte Windows Management Framework (WMF) 5,1. Viz [instalace a konfigurace WMF 5,1](/powershell/scripting/wmf/setup/install-configure).

2. Nainstalujte AzureRM a/nebo AZ pomocí pokynů v tématu [instalace Azure PowerShell ve Windows s PowerShellGet](/powershell/azure/azurerm/install-azurerm-ps).

### <a name="install-the-msonline-module"></a>Instalace modulu MSOnline

>[!NOTE]
>Chcete-li nainstalovat modul MSOnline, musíte být členem role správce. Další informace najdete v tématu [o rolích správce](/microsoft-365/admin/add-users/about-admin-roles).

1. Ujistěte se, že je v počítači povolená funkce Microsoft .NET Framework 3.5. x. Je možné, že je v počítači nainstalovaná novější verze, ale zpětná kompatibilita se staršími verzemi .NET Framework může být povolená nebo zakázaná. 

2. Nainstalujte verzi 64 [Pomocníka pro přihlášení ke službám Microsoft Online Services](https://www.microsoft.com/Download/details.aspx?id=28177).

3. Spusťte prostředí Windows PowerShell jako správce a vytvořte příkazový řádek prostředí Windows PowerShell se zvýšenými oprávněními.

4. Nasaďte Azure Active Directory z [MSOnline 1,0](http://www.powershellgallery.com/packages/MSOnline/1.0).

5. Pokud budete vyzváni k instalaci poskytovatele NuGet, zadejte Y a stiskněte ENTER.

6. Pokud budete vyzváni k instalaci modulu z [PSGallery](https://www.powershellgallery.com/), zadejte Y a stiskněte ENTER.

### <a name="install-support-for-pscredential"></a>Instalace podpory pro PSCredential

Azure Automation používá třídu [PSCredential](/dotnet/api/system.management.automation.pscredential) k reprezentaci assetu přihlašovacích údajů. Vaše skripty načítají `PSCredential` objekty pomocí `Get-AutomationPSCredential` rutiny. Další informace najdete v tématu [assety přihlašovacích údajů v Azure Automation](shared-resources/credentials.md).

## <a name="assign-a-subscription-administrator"></a>Přiřazení správce předplatného

Musíte přiřadit správce k předplatnému Azure. Tato osoba má roli vlastníka oboru předplatného. Viz [řízení přístupu na základě role v Azure Automation](automation-role-based-access-control.md). 

## <a name="change-the-azure-ad-users-password"></a>Změna hesla uživatele Azure AD

Změna hesla uživatele Azure AD:

1. Odhlaste se z Azure.

2. Přihlaste se ke službě Azure jako uživatel Azure AD, který je právě vytvořený, pomocí úplného uživatelského jména (včetně domény) a dočasného hesla. 

3. Požádejte správce, aby po zobrazení výzvy změnil heslo.

## <a name="configure-azure-automation-to-manage-the-azure-subscription"></a>Konfigurace Azure Automation pro správu předplatného Azure

Aby Azure Automation komunikovala se službou Azure AD, musíte načíst přihlašovací údaje spojené s připojením Azure ke službě Azure AD. Příklady těchto přihlašovacích údajů jsou ID tenanta, ID předplatného a podobným způsobem. Další informace o připojení mezi Azure a Azure AD najdete v článku [připojení vaší organizace k Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="create-a-credential-asset"></a>Vytvoření assetu přihlašovacích údajů

Když máte k dispozici přihlašovací údaje Azure pro Azure AD, je čas vytvořit Azure Automation Asset přihlašovacích údajů pro bezpečné uložení přihlašovacích údajů Azure AD tak, aby k nim měly přístup Runbooky a konfigurační skripty pro konfiguraci stavu dostupnosti (DSC). Můžete to udělat pomocí rutin Azure Portal nebo PowerShellu.

### <a name="create-the-credential-asset-in-azure-portal"></a>Vytvoření assetu přihlašovacích údajů v Azure Portal

K vytvoření assetu přihlašovacích údajů můžete použít Azure Portal. Tuto operaci proveďte z účtu Automation pomocí **přihlašovacích údajů** v části **sdílené prostředky**. Viz [přihlašovací údaje prostředků v Azure Automation](shared-resources/credentials.md).

### <a name="create-the-credential-asset-with-windows-powershell"></a>Vytvoření assetu přihlašovacích údajů pomocí Windows PowerShellu

Při přípravě nového assetu přihlašovacích údajů v prostředí Windows PowerShell skript nejprve vytvoří `PSCredential` objekt pomocí přiřazeného uživatelského jména a hesla. Skript pak pomocí tohoto objektu vytvoří Asset prostřednictvím volání rutiny [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure.service/new-azureautomationcredential) . Případně skript může zavolat rutinu [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) , aby vyzvat uživatele k zadání jména a hesla. Viz [přihlašovací údaje prostředků v Azure Automation](shared-resources/credentials.md). 

## <a name="manage-azure-resources-from-an-azure-automation-runbook"></a>Správa prostředků Azure z Azure Automation sady Runbook

Prostředky Azure můžete spravovat z Azure Automation sad Runbook pomocí assetu přihlašovacích údajů. Níže je příklad Runbooku PowerShellu, který shromažďuje přihlašovací údaje pro použití při zastavování a spouštění virtuálních počítačů v rámci předplatného Azure. Tato sada Runbook nejdřív používá `Get-AutomationPSCredential` k načtení přihlašovacích údajů, které se mají použít k ověření v Azure. Pak zavolá rutinu [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) pro připojení k Azure pomocí přihlašovacích údajů. Skript pomocí rutiny [Select-AzureSubscription](/powershell/module/servicemanagement/azure.service/select-azuresubscription) vyberte předplatné, se kterým chcete pracovat. 

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

* Podrobnosti o použití přihlašovacích údajů najdete [v tématu Správa přihlašovacích údajů v Azure Automation](shared-resources/credentials.md).
* Informace o modulech najdete v tématu [Správa modulů v Azure Automation](shared-resources/modules.md).
* Pokud potřebujete Runbook spustit, přečtěte si téma [Spuštění Runbooku v Azure Automation](start-runbooks.md).
* Podrobnosti o PowerShellu najdete v tématu [dokumentace k PowerShellu](/powershell/scripting/overview).
