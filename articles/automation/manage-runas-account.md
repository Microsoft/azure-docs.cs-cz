---
title: Správa azure automatizace spustit jako účty
description: Tento článek popisuje, jak spravovat účty Spustit jako pomocí Prostředí PowerShell nebo z portálu.
services: automation
ms.subservice: shared-capabilities
ms.date: 05/24/2019
ms.topic: conceptual
ms.openlocfilehash: 341db4ffa5b2e0641572f2c9dc011e91fac6a1e9
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617321"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Správa azure automatizace spustit jako účty

Spouštět jako účty v Azure Automation poskytují ověřování pro správu prostředků v Azure pomocí rutin Azure. Když vytvoříte účet Spustit jako, vytvoří nového hlavního uživatele služby ve službě Azure Active Directory (AD) a přiřadí roli přispěvatele tomuto uživateli na úrovni předplatného. Pro runbooky, které používají hybridní runbook ové pracovníky na virtuálních počítačích Azure, můžete k ověření prostředků Azure použít [spravované identity pro prostředky Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) namísto účtů Run As.

Instanční objekt pro spustit jako účet nemá oprávnění ke čtení Azure AD ve výchozím nastavení. Pokud chcete přidat oprávnění ke čtení nebo správě Azure AD, budete muset udělit oprávnění na instanční objekt v rámci **oprávnění rozhraní API**. Další informace najdete v tématu [Přidání oprávnění k přístupu k webovým apim](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="types-of-run-as-accounts"></a>Typy účtů Spustit jako

Azure Automation používá dva typy účtů Run As:

* Účet Spustit jako pro Azure
* Účet Azure Classic Run As

>[!NOTE]
>Předplatná Azure Cloud Solution Provider (CSP) podporují jenom model Azure Resource Manager. Služby Azure Resource Manager nejsou v programu dostupné. Když používáte předplatné CSP, účet Azure Classic Run As se nevytvoří, ale vytvoří se účet Azure Run As. Další informace o předplatných CSP najdete v tématu [Dostupné služby v předplatných CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).

### <a name="run-as-account"></a>Účet Spustit jako

Účet Spustit jako spravuje prostředky [modelu nasazení Resource Manageru.](../azure-resource-manager/management/deployment-models.md) Provádí následující úkoly.

* Vytvoří aplikaci Azure AD s certifikátem podepsaným svým držitelem, vytvoří účet instančního objektu pro tuto aplikaci v Azure AD a přiřadí roli přispěvatele pro tento účet v aktuálním předplatném. Nastavení certifikátu můžete změnit na Vlastník nebo jinou roli. Další informace najdete v tématu [Řízení přístupu na základě role ve službě Azure Automation](automation-role-based-access-control.md).
  
* Vytvoří datový zdroj `AzureRunAsCertificate` certifikátu automatizace pojmenovaný v zadaném účtu automatizace. Datový zdroj certifikátu obsahuje soukromý klíč certifikátu, který používá aplikace Azure AD.
  
* Vytvoří prostředek připojení `AzureRunAsConnection` automatizace pojmenovaný v zadaném účtu automatizace. Prostředek připojení obsahuje ID aplikace, ID klienta, ID předplatného a kryptografický otisk certifikátu.

### <a name="azure-classic-run-as-account"></a>Azure Classic spuštěné jako účet

Účet Azure Classic Run As spravuje prostředky [modelu klasického nasazení.](../azure-resource-manager/management/deployment-models.md) Chcete-li vytvořit nebo obnovit tento typ účtu, musíte být spolusprávcem předplatného.

Účet Azure Classic Run As provádí následující úkoly.

  * Vytvoří certifikát správy v předplatném.

  * Vytvoří datový zdroj `AzureClassicRunAsCertificate` certifikátu automatizace pojmenovaný v zadaném účtu automatizace. Prostředek certifikátu obsahuje privátní klíč certifikátu, který používá certifikát pro správu.

  * Vytvoří prostředek připojení `AzureClassicRunAsConnection` automatizace pojmenovaný v zadaném účtu automatizace. Prostředek připojení obsahuje název předplatného, ID předplatného a název datového zdroje certifikátu.

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Spustit jako oprávnění účtu

Tato část definuje oprávnění pro běžné účty Spustit jako i Klasické spuštění jako.

### <a name="permissions-to-configure-run-as-accounts"></a>Oprávnění ke konfiguraci účtů Spustit jako

Chcete-li vytvořit nebo aktualizovat účet Spustit jako, musíte mít určitá oprávnění a oprávnění. Správce aplikace ve službě Azure Active Directory a vlastník v předplatném můžete dokončit všechny úkoly. V situaci, kdy máte oddělení povinností, v následující tabulce je uveden seznam úkolů, ekvivalentní rutiny a potřebných oprávnění:

|Úkol|Rutina  |Minimální oprávnění  |Kde nastavíte oprávnění|
|---|---------|---------|---|
|Vytvoření aplikace Azure AD|[Nová aplikace AzAD](https://docs.microsoft.com/powershell/module/az.resources/new-azadapplication?view=azps-3.5.0)     | Role vývojáře aplikací<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Domů > registrace aplikací > Azure |
|Přidejte pověření do aplikace.|[Nové azadapppověření](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential?view=azps-3.5.0)     | Správce aplikace nebo globální správce<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Domů > registrace aplikací > Azure|
|Vytvoření a získání instančního objektu služby Azure AD|[Nový-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-3.5.0)</br>[Get-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal?view=azps-3.5.0)     | Správce aplikace nebo globální správce<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Domů > registrace aplikací > Azure|
|Přiřazení nebo získání role RBAC pro zadaný objekt zabezpečení|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment?view=azps-3.5.0)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment?view=azps-3.5.0)      | Správce nebo vlastník přístupu uživatelů nebo máte následující oprávnění:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Předplatné](../role-based-access-control/role-assignments-portal.md)</br>Předplatná domů \<>\> > název předplatného – řízení přístupu (IAM)|
|Vytvoření nebo odebrání certifikátu automatizace|[Nový certifikát AzAutomation](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate?view=azps-3.5.0)</br>[Odebrat azautomationcertificate](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate?view=azps-3.5.0)     | Přispěvatel ve skupině prostředků         |Skupina prostředků účtu automatizace|
|Vytvoření nebo odebrání připojení automatizace|[Nové připojení AzAutomation](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.5.0)</br>[Odebrat připojení AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection?view=azps-3.5.0)|Přispěvatel ve skupině prostředků |Skupina prostředků účtu automatizace|

<sup>1</sup> Uživatelé, kteří nejsou správci ve vašem tenantovi Azure AD, [můžou zaregistrovat aplikace AD,](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) pokud uživatelé klienta Azure AD **můžou zaregistrovat aplikace** na stránce Nastavení uživatele, je nastavená na **Ano**. Pokud je nastavení registrace aplikace **Ne**, musí být uživatel provádějící tuto akci definován v této tabulce.

Pokud nejste členem instance služby Active Directory předplatného před přidáním do role globálního správce předplatného, budete přidáni jako host. V takovém případě se `You do not have permissions to create…` zobrazí upozornění na stránce Přidat účet automatizace. 

Pokud jste členem instance služby Active Directory předplatného při přiřazení role globálního správce, můžete také obdržet `You do not have permissions to create…` upozornění na stránce Přidat účet automatizace. V takovém případě můžete požádat o odebrání instance služby Active Directory a potom požádat o opětovné přidání, abyste se stali úplným uživatelem ve službě Active Directory.

Chcete-li ověřit, zda byla napravena situace vytvářející chybovou zprávu:

1. V podokně Azure Active Directory na webu Azure Portal vyberte **Uživatelé a skupiny**. 
2. Vyberte **možnost Všichni uživatelé**.
3. Vyberte své jméno a pak vyberte **Profil**. 
4. Ujistěte se, že hodnota atributu **User type** pod profilem uživatele není nastavena na **hodnotu Host**.

### <a name="permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>Oprávnění ke konfiguraci účtů Classic Run As

Chcete-li konfigurovat nebo obnovovat klasické účty Spustit jako, musíte mít roli spolusprávce na úrovni předplatného. Další informace o klasických oprávněních k předplatném najdete v [tématu Správci klasického předplatného Azure](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="creating-a-run-as-account-in-azure-portal"></a>Vytvoření účtu Spustit jako na webu Azure Portal

Proveďte následující kroky k aktualizaci účtu Azure Automation na webu Azure Portal. Vytvořte účty Spustit jako a Klasický běh jako jednotlivě. Pokud nepotřebujete spravovat klasické prostředky, můžete vytvořit jenom účet Spustit jako pro Azure.

1. Přihlaste se k portálu Azure pomocí účtu, který je členem role správců předplatného a spolusprávcem předplatného.
2. Vyhledejte a vyberte **automatizační účty**.
3. Na stránce Účty automatizace vyberte účet automatizace ze seznamu.
4. V levém podokně vyberte v části Nastavení účtu možnost **Spustit jako účty.**
5. Podle toho, který účet požadujete, vyberte buď **Účet Spustit jako pro Azure**, nebo **Účet Spustit jako pro Azure Classic**. 
6. V závislosti na účtu zájmu použijte podokno **Přidat Azure Run As** nebo Přidat azure klasický **spouštění jako účet.** Po kontrole informací o přehledu klepněte na **tlačítko Vytvořit**.
6. Zatímco Azure vytváří účet Spustit jako, můžete průběh sledovat v nabídce v části **Oznámení**. Zobrazí se také nápis oznamující, že účet je vytvářen. Proces může trvat několik minut.

## <a name="creating-a-run-as-account-using-powershell"></a>Vytvoření účtu Spustit jako pomocí PowerShellu

Následující seznam obsahuje požadavky na vytvoření účtu Spustit jako v prostředí PowerShell. Tyto požadavky platí pro oba typy účtů Spustit jako.

* Windows 10 nebo Windows Server 2016 s moduly Azure Resource Manager u verze 3.4.1 a novější. Skript Prostředí PowerShell nepodporuje starší verze systému Windows.
* Azure PowerShell 1.0 nebo novější. Informace o vydání PowerShellu 1.0 najdete v článku [Postup instalace a konfigurace Azure PowerShellu](/powershell/azureps-cmdlets-docs).
* Účet automatizace, který je odkazován `AutomationAccountName` `ApplicationDisplayName` jako hodnota pro parametry a.
* Oprávnění ekvivalentní oprávněním uvedeným v [části Požadovaná oprávnění ke konfiguraci účtů Spustit jako](#permissions).

Chcete-li získat `SubscriptionId` `ResourceGroupName`hodnoty pro , a , což jsou požadované parametry pro skript prostředí PowerShell, proveďte další kroky.

1. Na portálu Azure vyberte **Účty automatizace**.
1. Na stránce Účty automatizace vyberte účet automatizace.
1. V části Nastavení účtu vyberte **Vlastnosti**.
1. Poznamenejte si hodnoty **pro NAME**, **ID předplatného**a **SKUPINU PROSTŘEDKŮ** na stránce Vlastnosti. Tyto hodnoty odpovídají hodnotám `AutomationAccountName` `SubscriptionId`parametrů `ResourceGroupName` skriptu , a PowerShell.

   ![Stránka vlastností účtu automatizace](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>Skript Prostředí PowerShell pro vytvoření účtu Spustit jako

Tato část obsahuje skript prostředí PowerShell pro vytvoření účtu Spustit jako. Skript obsahuje podporu pro několik konfigurací.

* Vytvoření účtu Spustit v Azure jako pomocí certifikátu podepsaného svým držitelem
* Vytvoření účtu Spustit jako a účtu Spustit jako pro Classic pomocí certifikátu podepsaného svým držitelem
* Vytvoření účtu Spustit jako a účtu Spustit jako pro Classic pomocí certifikátu, který vydala vaše podniková certifikační autorita
* Vytvoření účtu Spustit jako a účtu Spustit jako pro Classic pomocí certifikátu podepsaného svým držitelem v cloudu Azure Government

Skript používá k vytváření prostředků více rutin Azure Resource Manager. Rutiny a oprávnění, která vyžadují, naleznete v [tématu Oprávnění ke konfiguraci účtů Spustit jako](#permissions-to-configure-run-as-accounts).

Uložte skript do počítače pomocí názvu souboru **New-RunAsAccount.ps1**.

```powershell
#Requires -RunAsAdministrator
Param (
    [Parameter(Mandatory = $true)]
    [String] $ResourceGroup,

    [Parameter(Mandatory = $true)]
    [String] $AutomationAccountName,

    [Parameter(Mandatory = $true)]
    [String] $ApplicationDisplayName,

    [Parameter(Mandatory = $true)]
    [String] $SubscriptionId,

    [Parameter(Mandatory = $true)]
    [Boolean] $CreateClassicRunAsAccount,

    [Parameter(Mandatory = $true)]
    [String] $SelfSignedCertPlainPassword,

    [Parameter(Mandatory = $false)]
    [string] $EnterpriseCertPathForRunAsAccount,

    [Parameter(Mandatory = $false)]
    [String] $EnterpriseCertPlainPasswordForRunAsAccount,

    [Parameter(Mandatory = $false)]
    [String] $EnterpriseCertPathForClassicRunAsAccount,

    [Parameter(Mandatory = $false)]
    [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

    [Parameter(Mandatory = $false)]
    [ValidateSet("AzureCloud", "AzureUSGovernment")]
    [string]$EnvironmentName = "AzureCloud",

    [Parameter(Mandatory = $false)]
    [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
)

function CreateSelfSignedCertificate([string] $certificateName, [string] $selfSignedCertPlainPassword,
    [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
    $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
        -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
        -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired) -HashAlgorithm SHA256

    $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
    Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
    Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
}

function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {
    $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
    $keyId = (New-Guid).Guid

    # Create an Azure AD application, AD App Credential, AD ServicePrincipal

    # Requires Application Developer Role, but works with Application administrator or GLOBAL ADMIN
    $Application = New-AzADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId)
    # Requires Application administrator or GLOBAL ADMIN
    $ApplicationCredential = New-AzADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
    # Requires Application administrator or GLOBAL ADMIN
    $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $Application.ApplicationId
    $GetServicePrincipal = Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id

    # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
    Sleep -s 15
    # Requires User Access Administrator or Owner.
    $NewRole = New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
    $Retries = 0;
    While ($NewRole -eq $null -and $Retries -le 6) {
        Sleep -s 10
        New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
        $NewRole = Get-AzRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries++;
    }
    return $Application.ApplicationId.ToString();
}

function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
    $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force
    Remove-AzAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
    New-AzAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
}

function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
    Remove-AzAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
    New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
}

Import-Module AzureRm.Profile
Import-Module AzureRm.Resources

$AureRmProfileVersion = (Get-Module AzureRm.Profile).Version
if (!(($AzureRmProfileVersion.Major -ge 3 -and $AzureRmProfileVersion.Minor -ge 4) -or ($AzureRmProfileVersion.Major -gt 3))) {
    Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
    return
}

# To use the new Az modules to create your Run As accounts, please uncomment the following lines and ensure you comment out the previous 8 lines that import the AzureRM modules to avoid any issues. To learn about about using Az modules in your Automation account see https://docs.microsoft.com/azure/automation/az-modules.

# Import-Module Az.Automation
# Enable-AzureRmAlias


Connect-AzAccount -Environment $EnvironmentName
$Subscription = Get-AzSubscription -SubscriptionId $SubscriptionId

# Create a Run As account by using a service principal
$CertifcateAssetName = "AzureRunAsCertificate"
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionTypeName = "AzureServicePrincipal"

if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
    $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
    $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
}
else {
    $CertificateName = $AutomationAccountName + $CertifcateAssetName
    $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
    $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
    $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
    CreateSelfSignedCertificate $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
}

# Create a service principal
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
$ApplicationId = CreateServicePrincipal $PfxCert $ApplicationDisplayName

# Create the Automation certificate asset
CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

# Populate the ConnectionFieldValues
$SubscriptionInfo = Get-AzSubscription -SubscriptionId $SubscriptionId
$TenantID = $SubscriptionInfo | Select TenantId -First 1
$Thumbprint = $PfxCert.Thumbprint
$ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}

# Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

if ($CreateClassicRunAsAccount) {
    # Create a Run As account by using a service principal
    $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
    $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
    $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
    $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
    "Log in to the Microsoft Azure portal (https://portal.azure.com) and select Subscriptions -> Management Certificates." + [Environment]::NewLine +
    "Then click Upload and upload the .cer format of #CERT#"

    if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
        $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
        $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
        $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
    }
    else {
        $ClassicRunAsAccountCertificateName = $AutomationAccountName + $ClassicRunAsAccountCertifcateAssetName
        $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
        $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
        $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
        $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
        CreateSelfSignedCertificate $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
    }
    
    # Create the Automation certificate asset
    CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

    # Populate the ConnectionFieldValues
    $SubscriptionName = $subscription.Subscription.Name
    $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

    # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName   $ClassicRunAsAccountConnectionFieldValues

    Write-Host -ForegroundColor red       $UploadMessage
}
```

>[!NOTE]
>`Add-AzAccount`a `Add-AzureRMAccount` jsou aliasy pro [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Můžete použít tyto rutiny nebo můžete [aktualizovat moduly](automation-update-azure-modules.md) v účtu Automation na nejnovější verze. Možná budete muset aktualizovat moduly, i když jste právě vytvořili nový účet Automatizace.

### <a name="execute-the-powershell-script"></a>Spuštění skriptu Prostředí PowerShell

1. Na svém počítači na obrazovce **Start** spusťte **Windows PowerShell** se zvýšenými uživatelskými právy.
1. Z prostředí příkazového řádku se zvýšenými oprávněními přejděte do složky, která obsahuje skript.
1. Spusťte skript pomocí hodnot parametrů pro konfiguraci, kterou požadujete.
1. Pokud vytváříte účet Classic Run As, po spuštění skriptu nahrajte veřejný certifikát ( příponu název souboru**CER)** do úložiště pro správu pro předplatné, ve kterém byl vytvořen účet Automation.

Po spuštění skriptu se zobrazí výzva k ověření pomocí Azure. Přihlaste se pomocí účtu, který je členem role správci předplatného a spolusprávcem předplatného.

#### <a name="create-a-run-as-account-by-using-a-self-signed-certificate"></a>Vytvoření účtu Spustit jako pomocí certifikátu podepsaného svým držitelem

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate"></a>Vytvoření účtu Spustit jako a účtu Classic Run As pomocí certifikátu podepsaného svým držitelem

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-an-enterprise-certificate"></a>Vytvoření účtu Spustit jako a účtu Classic Run As pomocí podnikového certifikátu

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
```

Pokud jste vytvořili účet Classic Run As s veřejným certifikátem rozlehlé sítě ( soubor**CER),** použijte tento certifikát. Viz [Nahrání certifikátu rozhraní API pro správu na portál Azure](../azure-api-management-certs.md).

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate-in-the-azure-government-cloud"></a>Vytvoření účtu Spustit jako a účtu Spustit jako pro Classic pomocí certifikátu podepsaného svým držitelem v cloudu Azure Government

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
```

Pokud jste vytvořili účet Classic Run As s veřejným certifikátem podepsaným svým držitelem ( soubor**CER),** skript jej vytvoří a uloží do složky dočasných souborů v počítači. Najdete jej v profilu `%USERPROFILE%\AppData\Local\Temp`uživatele , který jste použili ke spuštění relace prostředí PowerShell.

## <a name="deleting-a-run-as-or-classic-run-as-account"></a>Odstranění účtu Spustit jako nebo Klasický běh jako

Tato část popisuje, jak odstranit účet Spustit jako nebo Klasický spustit jako. Při provedení této akce se účet Automation uchová. Po odstranění účtu ho můžete znovu vytvořit na webu Azure Portal.

1. Na webu Azure Portal otevřete účet Automation.

2. V levém podokně vyberte v části Nastavení účtu možnost **Spustit jako účty.**

3. Na stránce vlastností Účty Spustit jako vyberte účet Spustit jako nebo účet Spustit jako pro Classic, který chcete odstranit. 

4. V podokně Vlastnosti vybraného účtu klepněte na tlačítko **Odstranit**.

   ![Odstranění účtu Spustit jako](media/manage-runas-account/automation-account-delete-runas.png)

5. Zatímco se účet odstraňuje, můžete průběh sledovat v nabídce v části **Oznámení**.

6. Účet po odstranění můžete znovu vytvořit na stránce vlastností Účty Spustit jako výběrem možnosti Vytvořit v části **Účet Spustit jako pro Azure**.

   ![Znovuvytvoření účtu Automation Spustit jako](media/manage-runas-account/automation-account-create-runas.png)

## <a name="renewing-a-self-signed-certificate"></a><a name="cert-renewal"></a>Obnovení certifikátu podepsaného svým držitelem

Platnost certifikátu podepsaného svým držitelem, který jste vytvořili pro účet Spustit jako, vyprší jeden rok od data vytvoření. V určitém okamžiku před vypršením platnosti účtu Spustit jako je nutné certifikát obnovit. Můžete jej obnovit kdykoli před vypršením jeho platnosti. 

Při obnovení certifikátu podepsaného svým držitelem je aktuální platný certifikát zachován, aby bylo zajištěno, že všechny sady Runbook, které jsou zařazeny do fronty nebo aktivně spuštěny, a že se ověřují pomocí účtu Spustit jako, nebudou negativně ovlivněny. Certifikát zůstane platný až do data vypršení jeho platnosti.

>[!NOTE]
>Pokud se domníváte, že byl napaden účet Spustit jako, můžete odstranit a znovu vytvořit certifikát podepsaný svým držitelem.

>[!NOTE]
>Pokud jste nakonfigurovali účet Spustit jako tak, aby používal certifikát vydaný certifikační autoritou rozlehlé sítě, a tuto možnost používáte k obnovení možnosti certifikátu podepsaného svým držitelem, je certifikát organizace nahrazen certifikátem podepsaným svým držitelem.

Pomocí následujících kroků obnovte certifikát podepsaný svým držitelem.

1. Na webu Azure Portal otevřete účet Automation.

1. V části Nastavení účtu vyberte **Spustit jako účty.**

    ![Podokno vlastností účtu Automation](media/manage-runas-account/automation-account-properties-pane.png)

1. Na stránce Vlastnosti Spustit jako účty vyberte buď účet Spustit jako, nebo účet Classic Run As, pro který chcete certifikát obnovit.

1. V podokně vlastností vybraného účtu klepněte na tlačítko **Obnovit certifikát**.

    ![Obnovení certifikátu pro účet Spustit jako](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Zatímco se certifikát obnovuje, můžete průběh sledovat v nabídce v části **Oznámení**.

## <a name="setting-up-automatic-certificate-renewal-with-an-automation-runbook"></a><a name="auto-cert-renewal"></a>Nastavení automatického obnovení certifikátu pomocí runbooku automatizace

Chcete-li certifikáty obnovit automaticky, můžete použít runbook automatizace. Tento skript na [GitHubu](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) umožňuje tuto funkci ve vašem účtu Automation.

>[!NOTE]
>Chcete-li skript spustit, musíte být globálním správcem nebo správcem společnosti ve službě Azure AD.

Tento skript vytvoří týdenní plán pro obnovení certifikátů účtů Spustit jako. Přidá **runbook Update-AutomationRunAsCredential** do vašeho účtu Automation. Kód sady Runbook můžete zobrazit na GitHubu ve skriptu [Update-AutomationRunAsCredential.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1). Kód prostředí PowerShell v souboru můžete podle potřeby ručně obnovit certifikáty.

Pomocí následujících kroků otestujte proces obnovení okamžitě.

1. Upravte runbook **Update-AutomationRunAsCredential** a umístěte znak komentáře (#) na řádek 122 před příkaz **Exit(1).**

   ```powershell
   #Exit(1)
   ```

2. Publikujte runbook.
3. Spusťte runbook.
4. Ověřte úspěšné obnovení pomocí následujícího kódu:

   ```powershell
   (Get-AzAutomationCertificate -AutomationAccountName TestAA
                                -Name AzureRunAsCertificate
                                -ResourceGroupName TestAutomation).ExpiryTime.DateTime
   ```
    Výstup:

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. Po testu upravte runbook a odeberte znak komentáře, který jste přidali v kroku 1.
6. Publikujte runbook.

## <a name="limiting-run-as-account-permissions"></a><a name="limiting-run-as-account-permissions"></a>Omezení oprávnění účtu Spustit jako

Chcete-li řídit cílení automatizace proti prostředkům v Azure, můžete spustit [skript Update-AutomationAccountAccountRoleAssignments.ps1.](https://aka.ms/AA5hug8) Tento skript změní stávající spuštění jako instanční objekt účtu vytvořit a použít vlastní definici role. Role má oprávnění pro všechny prostředky kromě [trezoru klíčů](https://docs.microsoft.com/azure/key-vault/).

>[!IMPORTANT]
>Po spuštění skriptu **Update-AutomationRunAsAccountRoleAssignments.ps1** již nefungují sady Runbook, které přistupují k trezoru klíčů pomocí účtů Run-Automation. Před spuštěním skriptu byste měli zkontrolovat runbooky ve vašem účtu pro volání do Azure Key Vault. Chcete-li povolit přístup k trezoru klíčů z runbooků Azure Automation, musíte [přidat účet Spustit jako do oprávnění trezoru klíčů](#add-permissions-to-key-vault).

Pokud potřebujete omezit, dále co spustit jako instanční objekt může udělat, můžete přidat další typy prostředků do `NotActions` prvku definice vlastní role. Následující příklad omezuje přístup `Microsoft.Compute/*`k aplikaci . Pokud přidáte tento typ `NotActions` prostředku pro definici role, role nebude mít přístup k žádnému výpočetnímu prostředku. Další informace o definicích rolí najdete [v tématu Principy definic rolí pro prostředky Azure](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Můžete určit, zda je instanční objekt používaný vaším účtem Spustit jako v definici role přispěvatele nebo v vlastní. 

1. V části Nastavení účtu přejděte na účet Automation a vyberte **Spustit jako účty.**
2. Vyberte **Azure Spustit jako účet**. 
3. Vyberte **Role** vyhledejte definici role, která se používá.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

Můžete také určit definici role používané účty Spustit jako pro více předplatných nebo účty automatizace. To provést pomocí [check-automationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) skript v Galerii prostředí PowerShell.

### <a name="add-permissions-to-key-vault"></a>Přidání oprávnění do trezoru klíčů

Azure Automation můžete povolit ověření, jestli trezor klíčů a váš hlavní uživatel služby Spustit jako účet používají vlastní definici role. Musíte:

* Udělte oprávnění trezoru klíčů.
* Nastavte zásady přístupu.

Pomocí skriptu [Extend-AutomationRunAsAccountAccountAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) v Galerii prostředí PowerShell můžete udělit trezoru klíčů oprávnění ke spuštění jako účtu. Další informace o nastavení oprávnění v trezoru klíčů najdete v [tématu Udělení přístupu aplikacím do trezoru klíčů.](../key-vault/general/group-permissions-for-apps.md)

## <a name="resolving-misconfiguration-issues-for-run-as-accounts"></a>Řešení problémů s chybnou konfigurací pro účty Spustit jako

Některé položky konfigurace nezbytné pro účet Spustit jako nebo Klasický běh jako mohly být odstraněny nebo vytvořeny nesprávně během počátečníinstalace. Možné instance chybné konfigurace zahrnují:

* Asset certifikátu
* Asset připojení
* Spustit jako účet odebrán z role přispěvatele
* Instanční objekt nebo aplikace v Azure AD

Pro takové instance chybné konfigurace účet automatizace zjistí změny a `Incomplete` zobrazí stav v podokně vlastností Spustit jako účty pro účet.

![Nedokončená konfigurace účtu Spustit jako](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Když vyberete účet Spustit jako, v podokně vlastností účtu se zobrazí následující chybová zpráva:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Tyto potíže s účtem Spustit jako můžete rychle vyřešit jeho odstraněním a znovuvytvořením.

## <a name="next-steps"></a>Další kroky

* Další informace o instančních objektech naleznete v [tématu Application Objects and Service Principal Objects](../active-directory/develop/app-objects-and-service-principals.md).
* Další informace o certifikátech a službách Azure najdete [v tématu Přehled certifikátů pro Cloudové služby Azure](../cloud-services/cloud-services-certs-create.md).
