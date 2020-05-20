---
title: Správa účtů Azure Automation spustit jako
description: Tento článek popisuje, jak spravovat účty Spustit jako pomocí PowerShellu nebo na portálu.
services: automation
ms.subservice: shared-capabilities
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 68d04603ba9f0633bfa55598790b790055384fdb
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648158"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Správa účtů Azure Automation spustit jako

Účty Spustit jako v Azure Automation poskytují ověřování pro správu prostředků v Azure pomocí rutin Azure. Když vytvoříte účet Spustit jako, vytvoří se nový uživatel instančního objektu ve službě Azure Active Directory (AD) a přiřadí roli přispěvatele tomuto uživateli na úrovni předplatného.

## <a name="types-of-run-as-accounts"></a>Typy účtů spustit jako

Azure Automation používá dva typy účtů spustit jako:

* Účet Spustit jako pro Azure
* Účet Spustit jako pro Azure Classic

>[!NOTE]
>Předplatná Azure Cloud Solution Provider (CSP) podporují pouze model Azure Resource Manager. V programu nejsou k dispozici služby, které nejsou Azure Resource Manager. Pokud používáte předplatné CSP, účet Spustit jako pro Azure Classic se nevytvoří, ale vytvoří se účet Spustit jako pro Azure. Další informace o předplatných CSP najdete [v tématu dostupné služby v předplatných CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).

Instanční objekt pro účet Spustit jako nemá oprávnění ke čtení Azure AD ve výchozím nastavení. Pokud chcete přidat oprávnění ke čtení nebo správě služby Azure AD, musíte oprávnění k instančnímu objektu udělit v části **oprávnění rozhraní API**. Další informace najdete v tématu [Přidání oprávnění pro přístup k webovým rozhraním API](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

### <a name="run-as-account"></a>Účet Spustit jako

Účet Spustit jako spravuje prostředky [modelu nasazení Správce prostředků](../azure-resource-manager/management/deployment-models.md) . Provádí následující úlohy.

* Vytvoří aplikaci Azure AD s certifikátem podepsaným svým držitelem, vytvoří účet instančního objektu pro tuto aplikaci v Azure AD a přiřadí roli přispěvatele pro tento účet v aktuálním předplatném. Nastavení certifikátu můžete změnit na vlastník nebo na jinou roli. Další informace najdete v tématu [Řízení přístupu na základě role ve službě Azure Automation](automation-role-based-access-control.md).
  
* Vytvoří prostředek certifikátu Automation s názvem `AzureRunAsCertificate` v zadaném účtu Automation. Asset certifikátu obsahuje privátní klíč certifikátu, který používá aplikace Azure AD.
  
* Vytvoří prostředek připojení Automation s názvem `AzureRunAsConnection` v zadaném účtu Automation. Asset připojení obsahuje ID aplikace, ID tenanta, ID předplatného a kryptografický otisk certifikátu.

### <a name="azure-classic-run-as-account"></a>Azure Classic spuštěné jako účet

Účet Spustit jako pro Azure Classic spravuje prostředky [modelu nasazení Classic](../azure-resource-manager/management/deployment-models.md) . Aby bylo možné vytvořit nebo obnovit tento typ účtu, musíte být spolusprávcem předplatného.

Účet Spustit jako pro Azure Classic provádí následující úlohy.

  * Vytvoří v předplatném certifikát pro správu.

  * Vytvoří prostředek certifikátu Automation s názvem `AzureClassicRunAsCertificate` v zadaném účtu Automation. Prostředek certifikátu obsahuje privátní klíč certifikátu, který používá certifikát pro správu.

  * Vytvoří prostředek připojení Automation s názvem `AzureClassicRunAsConnection` v zadaném účtu Automation. Prostředek připojení obsahuje název předplatného, ID předplatného a název assetu certifikátu.

>[!NOTE]
>Účet Spustit jako pro Azure Classic se ve výchozím nastavení ve stejnou chvíli při vytváření účtu Automation nevytvoří. Tento účet se vytváří jednotlivě podle kroků popsaných dále v tomto článku.

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Oprávnění účtu Spustit jako

Tato část definuje oprávnění pro účty běžných účtů spustit jako a účty Spustit jako pro Azure Classic.

### <a name="permissions-to-configure-run-as-accounts"></a>Oprávnění ke konfiguraci účtů spustit jako

Chcete-li vytvořit nebo aktualizovat účet Spustit jako, je nutné mít specifická oprávnění a oprávnění. Správce aplikace v Azure Active Directory a vlastník v rámci předplatného může dokončit všechny úlohy. V situaci, kdy máte oddělení povinností, je v následující tabulce uveden seznam úkolů, ekvivalentní rutina a potřebná oprávnění:

|Úkol|Rutina  |Minimální oprávnění  |Místo nastavení oprávnění|
|---|---------|---------|---|
|Vytvoření aplikace Azure AD|[New-AzADApplication](https://docs.microsoft.com/powershell/module/az.resources/new-azadapplication)     | Role vývojáře aplikace<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Registrace aplikací > domovského > služby Azure AD |
|Přidejte do aplikace přihlašovací údaje.|[New-AzADAppCredential](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential)     | Správce aplikace nebo globální správce<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Registrace aplikací > domovského > služby Azure AD|
|Vytvoření a získání instančního objektu služby Azure AD|[New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal)     | Správce aplikace nebo globální správce<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Registrace aplikací > domovského > služby Azure AD|
|Přiřazení nebo získání role RBAC pro zadaný objekt zabezpečení|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Správce nebo vlastník přístupu uživatele nebo musí mít následující oprávnění:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Předplatné](../role-based-access-control/role-assignments-portal.md)</br>Předplatné Home > > \< název předplatného \> Access Control (IAM)|
|Vytvoření nebo odebrání certifikátu Automation|[New-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate)     | Přispěvatel ve skupině prostředků         |Skupina prostředků účtu služby Automation|
|Vytvoření nebo odebrání připojení služby Automation|[New-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection)|Přispěvatel ve skupině prostředků |Skupina prostředků účtu služby Automation|

<sup>1</sup> uživatelé bez oprávnění správce v TENANTOVI Azure AD můžou [Registrovat aplikace služby AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) , pokud je možnost Uživatelé klienta Azure AD **můžou registrovat aplikace** na stránce nastavení uživatele je nastavená na **Ano**. Pokud je nastavení registrace aplikace **ne**, uživatel provádějící tuto akci musí být definovaný v této tabulce.

Pokud nejste členem instance Active Directory předplatného, než se přiřadíte do role globálního správce předplatného, jste přidaní jako host. V takovém případě se `You do not have permissions to create…` na stránce Přidat účet Automation zobrazí upozornění. 

Pokud jste členem instance Active Directory předplatného, když je přiřazená role globálního správce, můžete také zobrazit `You do not have permissions to create…` Upozornění na stránce Přidat účet Automation. V takovém případě si můžete vyžádat odebrání z instance Active Directory předplatného a potom požádat o opětovné přidání, takže se ve službě Active Directory stanete úplným uživatelem.

Chcete-li ověřit, zda byla odstraněna situace, kdy došlo k chybě této chybové zprávy:

1. V podokně Azure Active Directory v Azure Portal vyberte **Uživatelé a skupiny**. 
2. Vyberte **Všichni uživatelé**.
3. Zvolte své jméno a pak vyberte **profil**. 
4. Ujistěte se, že hodnota atributu **Type uživatele** v profilu uživatele není nastavená na **Host**.

### <a name="permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>Oprávnění ke konfiguraci účtů spustit jako pro Classic

Pokud chcete nakonfigurovat nebo prodloužit účty Spustit jako pro Azure Classic, musíte mít roli spolusprávce na úrovni předplatného. Další informace o oprávněních klasického předplatného najdete v tématu [Správci předplatného Azure Classic](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="creating-a-run-as-account-in-azure-portal"></a>Vytvoření účtu Spustit jako v Azure Portal

Chcete-li aktualizovat účet Azure Automation v Azure Portal, proveďte následující kroky. Vytvořte účty Spustit jako a účet Spustit jako pro Azure Classic samostatně. Pokud nepotřebujete spravovat klasické prostředky, můžete vytvořit jenom účet Spustit jako pro Azure.

1. Přihlaste se k portálu Azure pomocí účtu, který je členem role správců předplatného a spolusprávcem předplatného.
2. Vyhledejte a vyberte **účty Automation**.
3. Na stránce účty Automation v seznamu vyberte svůj účet Automation.
4. V levém podokně vyberte **účty Spustit jako** v části nastavení účtu.
5. Podle toho, který účet požadujete, vyberte buď **Účet Spustit jako pro Azure**, nebo **Účet Spustit jako pro Azure Classic**. 
6. V závislosti na účtu zájmu použijte podokno přidat účet Spustit v **Azure jako** nebo **Přidat účet Spustit jako** pro Azure Classic. Po kontrole informací v přehledu klikněte na **vytvořit**.
6. Zatímco Azure vytváří účet Spustit jako, můžete průběh sledovat v nabídce v části **Oznámení**. Zobrazí se také zpráva s informacemi o tom, že účet se vytváří. Dokončení procesu může trvat několik minut.

## <a name="creating-a-run-as-account-using-powershell"></a>Vytvoření účtu Spustit jako pomocí PowerShellu

Následující seznam uvádí požadavky na vytvoření účtu Spustit jako v prostředí PowerShell. Tyto požadavky platí pro oba typy účtů spustit jako.

* Windows 10 nebo Windows Server 2016 s Azure Resource Manager moduly 3.4.1 a novější. PowerShellový skript nepodporuje starší verze Windows.
* Azure PowerShell 1.0 nebo novější. Informace o vydání PowerShellu 1.0 najdete v článku [Postup instalace a konfigurace Azure PowerShellu](/powershell/azureps-cmdlets-docs).
* Účet Automation, na který se odkazuje jako na hodnotu pro `AutomationAccountName` parametry a `ApplicationDisplayName` .
* Oprávnění, která jsou shodná s těmi, která jsou uvedena v [požadovaných oprávněních ke konfiguraci účtů spustit jako](#permissions).

Chcete-li získat hodnoty pro `SubscriptionId` , `ResourceGroupName` a `AutomationAccountName` , které jsou vyžadovány parametry pro skript prostředí PowerShell, proveďte následující kroky.

1. V Azure Portal vyberte **účty Automation**.
1. Na stránce účty Automation vyberte svůj účet Automation.
1. V části nastavení účtu vyberte **vlastnosti**.
1. Poznamenejte si hodnoty pro **název**, **ID předplatného**a **skupinu prostředků** na stránce Vlastnosti. Tyto hodnoty odpovídají hodnotám `AutomationAccountName` `SubscriptionId` `ResourceGroupName` parametrů skriptu PowerShellu, a v uvedeném pořadí.

   ![Stránka vlastností účtu služby Automation](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>Skript PowerShellu pro vytvoření účtu Spustit jako

V této části najdete skript prostředí PowerShell pro vytvoření účtu Spustit jako. Tento skript zahrnuje podporu pro několik konfigurací.

* Vytvoření účtu Spustit v Azure jako pomocí certifikátu podepsaného svým držitelem
* Vytvoření účtu Spustit jako a účtu Spustit jako pro Classic pomocí certifikátu podepsaného svým držitelem
* Vytvoření účtu Spustit jako a účtu Spustit jako pro Classic pomocí certifikátu, který vydala vaše podniková certifikační autorita
* Vytvoření účtu Spustit jako a účtu Spustit jako pro Classic pomocí certifikátu podepsaného svým držitelem v cloudu Azure Government

Skript k vytváření prostředků používá několik rutin Azure Resource Manager. Informace o rutinách a oprávněních, která vyžadují, najdete v tématu [oprávnění ke konfiguraci účtů spustit jako](#permissions-to-configure-run-as-accounts).

Uložte skript na svém počítači pomocí názvu souboru **New-RunAsAccount. ps1**.

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
>`Add-AzAccount`a `Add-AzureRMAccount` jsou aliasy pro [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Tyto rutiny můžete použít nebo můžete [své moduly](automation-update-azure-modules.md) v účtu Automation aktualizovat na nejnovější verze. Vaše moduly možná budete muset aktualizovat i v případě, že jste právě vytvořili nový účet Automation.

### <a name="execute-the-powershell-script"></a>Spuštění skriptu PowerShellu

1. Na svém počítači na obrazovce **Start** spusťte **Windows PowerShell** se zvýšenými uživatelskými právy.
1. V prostředí příkazového řádku se zvýšenými oprávněními přejdete do složky, která obsahuje váš skript.
1. Spusťte skript s použitím hodnot parametrů pro konfiguraci, kterou požadujete.
1. Pokud po spuštění skriptu vytvoříte účet Spustit jako pro Classic, nahrajte do úložiště správy pro předplatné, ve kterém se vytvořil účet služby Automation, certifikát veřejného certifikátu (přípona názvu souboru **. cer** ).

Po spuštění skriptu budete vyzváni k ověření pomocí Azure. Přihlaste se pomocí účtu, který je členem role správců předplatného a spolusprávcem předplatného.

#### <a name="create-a-run-as-account-by-using-a-self-signed-certificate"></a>Vytvoření účtu Spustit jako pomocí certifikátu podepsaného svým držitelem

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate"></a>Vytvoření účtu Spustit jako a účtu Spustit jako pro Azure Classic pomocí certifikátu podepsaného svým držitelem

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-an-enterprise-certificate"></a>Vytvoření účtu Spustit jako a účtu Spustit jako pro Azure Classic pomocí podnikového certifikátu

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
```

Pokud jste vytvořili účet Spustit jako pro Classic s podnikovým veřejným certifikátem (soubor **. cer** ), použijte tento certifikát. Viz [nahrání certifikátu rozhraní API pro správu do Azure Portal](../azure-api-management-certs.md).

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate-in-the-azure-government-cloud"></a>Vytvoření účtu Spustit jako a účtu Spustit jako pro Classic pomocí certifikátu podepsaného svým držitelem v cloudu Azure Government

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
```

Pokud jste vytvořili účet Spustit jako pro Classic s veřejným certifikátem podepsaným svým držitelem (soubor **. cer** ), skript ho vytvoří a uloží do složky dočasných souborů ve vašem počítači. Najdete ho v profilu uživatele `%USERPROFILE%\AppData\Local\Temp` , který jste použili ke spuštění relace PowerShellu.

## <a name="deleting-a-run-as-or-classic-run-as-account"></a>Odstranění účtu Spustit jako nebo účtu Spustit jako pro Classic

Tato část popisuje, jak odstranit účet Spustit jako nebo účet Spustit jako pro Azure Classic. Při provedení této akce se účet Automation uchová. Po odstranění účtu ho můžete znovu vytvořit v Azure Portal.

1. Na webu Azure Portal otevřete účet Automation.

2. V levém podokně vyberte **účty Spustit jako** v části nastavení účtu.

3. Na stránce vlastností Účty Spustit jako vyberte účet Spustit jako nebo účet Spustit jako pro Classic, který chcete odstranit. 

4. V podokně vlastnosti vybraného účtu klikněte na **Odstranit**.

   ![Odstranění účtu Spustit jako](media/manage-runas-account/automation-account-delete-runas.png)

5. Zatímco se účet odstraňuje, můžete průběh sledovat v nabídce v části **Oznámení**.

6. Účet po odstranění můžete znovu vytvořit na stránce vlastností Účty Spustit jako výběrem možnosti Vytvořit v části **Účet Spustit jako pro Azure**.

   ![Znovuvytvoření účtu Automation Spustit jako](media/manage-runas-account/automation-account-create-runas.png)

## <a name="renewing-a-self-signed-certificate"></a><a name="cert-renewal"></a>Obnovování certifikátu podepsaného svým držitelem

Certifikát podepsaný svým držitelem, který jste vytvořili pro účet Spustit jako, vyprší jeden rok od data vytvoření. V určitém okamžiku před vypršením platnosti účtu Spustit jako je nutné certifikát obnovit. Můžete ho prodloužit kdykoli předtím, než vyprší jeho platnost. 

Při obnovení certifikátu podepsaného svým držitelem se zachová aktuální platný certifikát, aby se zajistilo, že všechny Runbooky, které jsou ve frontě nebo aktivně spuštěné a které se ověřují pomocí účtu Spustit jako, nejsou negativně ovlivněny. Certifikát zůstane platný až do data vypršení jeho platnosti.

>[!NOTE]
>Pokud si myslíte, že došlo k ohrožení zabezpečení účtu Spustit jako, můžete certifikát podepsaný svým držitelem odstranit a znovu vytvořit.

>[!NOTE]
>Pokud jste nakonfigurovali účet Spustit jako, aby používal certifikát vydaný certifikační autoritou rozlehlé sítě, a použijete možnost k obnovení certifikátu podepsaného svým držitelem, bude podnikový certifikát nahrazen certifikátem podepsaným svým držitelem.

K obnovení certifikátu podepsaného svým držitelem použijte následující postup.

1. Na webu Azure Portal otevřete účet Automation.

1. V části nastavení účtu vyberte **účty Spustit jako** .

    ![Podokno vlastností účtu Automation](media/manage-runas-account/automation-account-properties-pane.png)

1. Na stránce vlastností účty Spustit jako vyberte účet Spustit jako nebo účet Spustit jako pro Classic, pro který chcete obnovit certifikát.

1. V podokně vlastnosti vybraného účtu klikněte na **obnovit certifikát**.

    ![Obnovení certifikátu pro účet Spustit jako](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Zatímco se certifikát obnovuje, můžete průběh sledovat v nabídce v části **Oznámení**.

## <a name="setting-up-automatic-certificate-renewal-with-an-automation-runbook"></a><a name="auto-cert-renewal"></a>Nastavení automatického obnovení certifikátu pomocí Runbooku služby Automation

K automatickému obnovení certifikátů můžete použít Runbook služby Automation. Tento skript na [GitHubu](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) tuto funkci umožňuje ve vašem účtu Automation.

>[!NOTE]
>Abyste mohli skript spustit, musíte být globálním správcem nebo správcem společnosti ve službě Azure AD.

Tento skript vytvoří týdenní plán pro obnovení certifikátů účtu Spustit jako. Přidá do svého účtu Automation Runbook **Update-AutomationRunAsCredential** . Kód Runbooku můžete zobrazit na GitHubu ve skriptu [Update-AutomationRunAsCredential. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1). V případě potřeby můžete v souboru použít kód PowerShellu k ručnímu obnovení certifikátů.

K okamžitému otestování procesu obnovení použijte následující postup.

1. Upravte sadu Runbook **Update-AutomationRunAsCredential** a umístěte znak komentáře (#) na řádku 122 před příkaz **Exit (1)** .

   ```powershell
   #Exit(1)
   ```

2. Publikujte Runbook.
3. Spusťte Runbook.
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

5. Po dokončení testu upravte Runbook a odeberte znak komentáře, který jste přidali v kroku 1.
6. Publikujte Runbook.

## <a name="limiting-run-as-account-permissions"></a><a name="limiting-run-as-account-permissions"></a>Omezení oprávnění účtu Spustit jako

Pokud chcete řídit cílení na automatizaci u prostředků v Azure, můžete spustit skript [Update-AutomationRunAsAccountRoleAssignments. ps1](https://aka.ms/AA5hug8) . Tento skript změní existující instanční objekt účtu Spustit jako tak, aby vytvořil a používal vlastní definici role. Role má oprávnění pro všechny prostředky kromě [Key Vault](https://docs.microsoft.com/azure/key-vault/).

>[!IMPORTANT]
>Po spuštění skriptu **Update-AutomationRunAsAccountRoleAssignments. ps1** nebudou Runbooky, které přistupují k Key Vault pomocí účtů spustit jako, nadále fungovat. Před spuštěním skriptu byste měli zkontrolovat Runbooky ve vašem účtu pro volání Azure Key Vault. Pokud chcete povolit přístup k Key Vault z Azure Automation sad Runbook, musíte [Přidat účet Spustit jako pro Key Vault oprávnění](#add-permissions-to-key-vault).

Pokud potřebujete omezit, co může instanční objekt spustit jako, můžete přidat další typy prostředků do `NotActions` prvku definice vlastní role. Následující příklad omezuje přístup na `Microsoft.Compute/*` . Pokud přidáte tento typ prostředku do `NotActions` definice role, role nebude mít přístup k žádnému výpočetnímu prostředku. Další informace o definicích rolí najdete v tématu [vysvětlení definic rolí pro prostředky Azure](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Můžete určit, jestli instanční objekt používaný účtem spustit jako je v definici role přispěvatele, nebo na vlastní. 

1. V části nastavení účtu vyberte účet Automation a vyberte **účty Spustit jako** .
2. Vyberte **účet Spustit jako pro Azure**. 
3. Vyberte **role** pro vyhledání používané definice role.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

Můžete také určit definici role používané účty Spustit jako pro více předplatných nebo účtů Automation. Použijte k tomu skript [check-AutomationRunAsAccountRoleAssignments. ps1](https://aka.ms/AA5hug5) v Galerie prostředí PowerShell.

### <a name="add-permissions-to-key-vault"></a>Přidat oprávnění pro Key Vault

Můžete Azure Automation, abyste ověřili, jestli Key Vault a objekt služby účet Spustit jako používá vlastní definici role. Musíte:

* Udělte oprávnění Key Vault.
* Nastavte zásady přístupu.

Pomocí skriptu [extend-AutomationRunAsAccountRoleAssignmentToKeyVault. ps1](https://aka.ms/AA5hugb) v Galerie prostředí PowerShell můžete poskytnout účtu Spustit jako oprávnění k Key Vault. Další podrobnosti o nastavení oprávnění pro Key Vault najdete v tématu [udělení přístupu k trezoru klíčů aplikacím](../key-vault/general/group-permissions-for-apps.md) .

## <a name="resolving-misconfiguration-issues-for-run-as-accounts"></a>Řešení potíží s chybou konfigurace pro účty Spustit jako

Některé položky konfigurace potřebné pro účet Spustit jako nebo pro klasický účet Spustit jako byly pravděpodobně při počátečním nastavení odstraněny nebo vytvořeny nesprávně. Mezi možné instance nestejné konfigurace patří:

* Asset certifikátu
* Asset připojení
* Účet Spustit jako odebraný z role Přispěvatel
* Instanční objekt nebo aplikace v Azure AD

U takových instancí s *neúplnými* konfigurace účet Automation zjistí změny a v podokně vlastností účty Spustit jako pro tento účet zobrazí stav nedokončeno.

![Nedokončená konfigurace účtu Spustit jako](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Když vyberete účet Spustit jako, v podokně vlastností účtu se zobrazí následující chybová zpráva:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Tyto potíže s účtem Spustit jako můžete rychle vyřešit jeho odstraněním a znovuvytvořením.

## <a name="next-steps"></a>Další kroky

* Další informace o instančních objektech naleznete v tématu [Application Objects and instanční objekty](../active-directory/develop/app-objects-and-service-principals.md).
* Další informace o certifikátech a službách Azure najdete v tématu [Přehled certifikátů pro azure Cloud Services](../cloud-services/cloud-services-certs-create.md).
