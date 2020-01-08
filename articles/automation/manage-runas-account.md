---
title: Správa účtů Azure Automation spustit jako
description: Tento článek popisuje, jak spravovat účty Spustit jako pomocí PowerShellu nebo na portálu.
services: automation
ms.subservice: shared-capabilities
ms.date: 05/24/2019
ms.topic: conceptual
ms.openlocfilehash: 34dd15ee638335048c983fbb752bd812291456ca
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75418054"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Správa účtů Azure Automation spustit jako

Účty Spustit jako v Azure Automation slouží k zajištění ověřování pro správu prostředků v Azure pomocí rutin Azure.

Když vytvoříte účet Spustit jako, vytvoří se v Azure Active Directory nový uživatel instančního objektu, který tomuto uživateli přiřadí roli přispěvatel na úrovni předplatného. Pro Runbooky, které používají hybridní pracovní procesy Runbooku na virtuálních počítačích Azure, můžete k ověřování prostředků Azure použít [spravované identity pro prostředky Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) místo účtů spustit jako.

Existují dva typy účtů spustit jako:

* **Účet Spustit jako pro Azure** – tento účet se používá ke správě prostředků [modelu nasazení Správce prostředků](../azure-resource-manager/resource-manager-deployment-model.md) .
  * Vytvoří aplikaci Azure AD s certifikátem podepsaným svým držitelem, vytvoří účet instančního objektu pro tuto aplikaci v Azure AD a přiřadí roli přispěvatele pro tento účet v aktuálním předplatném. Toto nastavení můžete změnit na roli Vlastník nebo libovolnou jinou roli. Další informace najdete v tématu [Řízení přístupu na základě role ve službě Azure Automation](automation-role-based-access-control.md).
  * V příslušném účtu Automation vytvoří prostředek certifikátu Automation s názvem *AzureRunAsCertificate*. Prostředek certifikátu obsahuje privátní klíč certifikátu, který používá aplikace Azure AD.
  * V příslušném účtu Automation vytvoří prostředek připojení Automation s názvem *AzureRunAsConnection*. Prostředek připojení obsahuje parametry applicationId, tenantId, subscriptionId a certificate thumbprint.

* **Účet Spustit jako pro Azure Classic** – tento účet se používá ke správě prostředků [modelu nasazení Classic](../azure-resource-manager/resource-manager-deployment-model.md) .
  * Vytvoří certifikát pro správu v předplatném.
  * V příslušném účtu Automation vytvoří prostředek certifikátu Automation s názvem *AzureClassicRunAsCertificate*. Prostředek certifikátu obsahuje privátní klíč certifikátu, který používá certifikát pro správu.
  * V příslušném účtu Automation vytvoří prostředek připojení Automation s názvem *AzureClassicRunAsConnection*. Prostředek propojení obsahuje název a ID předplatného a název prostředku certifikátu.
  * Musí být spolusprávcem předplatného, aby bylo možné vytvořit nebo prodloužit platnost.

  > [!NOTE]
  > Předplatná Azure Cloud Solution Provider (CSP) podporují jenom model Azure Resource Manager, ale služby, které nejsou Azure Resource Manager, nejsou v programu k dispozici. Pokud používáte předplatné CSP, účet Spustit jako pro Azure Classic se nevytvoří. Účet Spustit jako pro Azure se pořád vytvoří. Další informace o předplatných CSP najdete [v tématu dostupné služby v předplatných CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services#comments).

  > [!NOTE]
  > Instanční objekt pro účet Spustit jako nemá oprávnění ke čtení Azure Active Directory ve výchozím nastavení. Pokud chcete přidat oprávnění ke čtení nebo správě služby Azure Active Directory, musíte toto oprávnění použít u instančního objektu v části **oprávnění rozhraní API**. Další informace najdete v tématu [Přidání oprávnění pro přístup k webovým rozhraním API](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="permissions"></a>Oprávnění ke konfiguraci účtů spustit jako

Chcete-li vytvořit nebo aktualizovat účet Spustit jako, je nutné mít specifická oprávnění a oprávnění. Správce aplikace v Azure Active Directory a vlastník v rámci předplatného může dokončit všechny úlohy. V případě, kdy máte oddělení povinností, v následující tabulce je uveden seznam úkolů, ekvivalentní rutina a oprávnění:

|Úkol|Rutina  |Minimální oprávnění  |Místo nastavení oprávnění|
|---|---------|---------|---|
|Vytvoření aplikace Azure AD|[New-AzureRmADApplication](/powershell/module/azurerm.resources/new-azurermadapplication)     | Role vývojáře aplikace<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Domovské > Azure Active Directory > Registrace aplikací |
|Přidejte do aplikace přihlašovací údaje.|[New-AzureRmADAppCredential](/powershell/module/AzureRM.Resources/New-AzureRmADAppCredential)     | Správce aplikace nebo globální správce<sup>1</sup>         |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Domovské > Azure Active Directory > Registrace aplikací|
|Vytvoření a získání instančního objektu služby Azure AD|[New-AzureRMADServicePrincipal](/powershell/module/AzureRM.Resources/New-AzureRmADServicePrincipal)</br>[Get-AzureRmADServicePrincipal](/powershell/module/AzureRM.Resources/Get-AzureRmADServicePrincipal)     | Správce aplikace nebo globální správce<sup>1</sup>        |[Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Domovské > Azure Active Directory > Registrace aplikací|
|Přiřazení nebo získání role RBAC pro zadaný objekt zabezpečení|[New-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment)</br>[Get-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/Get-AzureRmRoleAssignment)      | Musíte mít následující oprávnění:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br>Nebo se musí jednat o:</br></br>Správce nebo vlastník přístupu uživatele        | [Předplatné](../role-based-access-control/role-assignments-portal.md)</br>Předplatné Home > > \<název předplatného\>-Access Control (IAM)|
|Vytvoření nebo odebrání certifikátu Automation|[New-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/New-AzureRmAutomationCertificate)</br>[Remove-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationCertificate)     | Přispěvatel ve skupině prostředků         |Skupina prostředků účtu služby Automation|
|Vytvoření nebo odebrání připojení služby Automation|[New-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/New-AzureRmAutomationConnection)</br>[Remove-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationConnection)|Přispěvatel ve skupině prostředků |Skupina prostředků účtu služby Automation|

<sup>1</sup> uživatelé, kteří nejsou správci v TENANTOVI Azure AD, můžou [Registrovat aplikace AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) , pokud je možnost Uživatelé klienta Azure AD **můžou registrovat aplikace** na stránce **nastavení uživatele** je nastavená na **Ano**. Pokud je nastavení registrace aplikací nastaveno na **ne**, uživatel provádějící tuto akci musí být ten, který je definovaný v předchozí tabulce.

Pokud nejste členem instance Active Directory předplatného, než se přiřadíte do role **globálního správce** předplatného, jste přidaní jako host. V takovém případě se na stránce **Přidat účet Automation** zobrazí upozornění `You do not have permissions to create…`. Uživatele, kteří byli nejdřív přidaní do role **globálního správce** , je možné odebrat z instance Active Directory předplatného a znovu je přidat, aby je uživatelé ve službě Active Directory mohli vytvořit jako úplný. Takovou situaci můžete ověřit v podokně **Azure Active Directory** na webu Azure Portal. Vyberte **Uživatelé a skupiny**, potom **Všichni uživatelé** a po výběru konkrétního uživatele vyberte **Profil**. Hodnota atributu **Typ uživatele** v profilu uživatele by neměla být **Host**.

## <a name="permissions-classic"></a>Oprávnění ke konfiguraci účtů spustit jako pro Classic

Pokud chcete nakonfigurovat nebo prodloužit účty Spustit jako pro Azure Classic, musíte mít roli **spolusprávce** na úrovni předplatného. Další informace o klasických oprávněních najdete v tématu [Správci předplatného Azure Classic](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="create-a-run-as-account-in-the-portal"></a>Vytvoření účtu Spustit jako na portálu

V této části provedete následující kroky a aktualizujete svůj účet Azure Automation na webu Azure Portal. Vytvoříte samostatně účet Spustit jako a účet Spustit jako pro Azure Classic. Pokud nepotřebujete spravovat klasické prostředky, můžete vytvořit jenom účet Spustit jako pro Azure.

1. Přihlaste se k webu Azure Portal pomocí účtu, který je členem role správců předplatného a spolusprávcem předplatného.
2. V Azure Portal vyhledejte a vyberte **účty Automation**.
3. Na stránce **Účty Automation** vyberte ze seznamu svůj účet Automation.
4. V levém podokně vyberte **Účty Spustit jako** v části **Nastavení účtu**.
5. Podle toho, který účet požadujete, vyberte buď **Účet Spustit jako pro Azure**, nebo **Účet Spustit jako pro Azure Classic**. Po výběru se zobrazí podokno **Přidat účet Spustit jako pro Azure** nebo **Přidat účet Spustit jako pro Azure Classic**. Jakmile zkontrolujete souhrnné informace, klikněte na **Vytvořit** a pokračujte ve vytváření účtu Spustit jako.
6. Zatímco Azure vytváří účet Spustit jako, můžete průběh sledovat v nabídce v části **Oznámení**. Zobrazí se také nápis informující, že je účet vytvořený. Dokončení tohoto procesu může trvat několik minut.

## <a name="create-run-as-account-using-powershell"></a>Vytvoření účtu Spustit jako pomocí PowerShellu

## <a name="prerequisites"></a>Požadavky

Následující seznam uvádí požadavky na vytvoření účtu Spustit jako v prostředí PowerShell:

* Windows 10 nebo Windows Server 2016 s Azure Resource Manager moduly 3.4.1 a novější. Skript PowerShellu nepodporuje dřívější verze Windows.
* Azure PowerShell 1.0 nebo novější. Informace o vydání PowerShellu 1.0 najdete v článku [Postup instalace a konfigurace Azure PowerShellu](/powershell/azureps-cmdlets-docs).
* Účet Automation, na který se odkazuje jako na hodnotu parametrů *-AutomationAccountName* a *-ApplicationDisplayName*.
* Oprávnění, která jsou shodná s tím, co je uvedeno v [požadovaných oprávněních ke konfiguraci účtů spustit jako](#permissions)

Pokud chcete získat hodnoty pro *SubscriptionId*, *Resource*a *AutomationAccountName*, které jsou pro tento skript povinné, proveďte následující kroky:

1. V Azure Portal vyhledejte a vyberte **účty Automation**.
1. Na stránce účtu Automation vyberte váš účet Automation a potom v části **Nastavení účtu** vyberte **Vlastnosti**.
1. Poznamenejte si hodnoty **ID předplatného**, **název**a **skupiny prostředků** na stránce **vlastnosti** .

   ![Stránka vlastnosti účtu Automation](media/manage-runas-account/automation-account-properties.png)

Tento skript PowerShellu zahrnuje podporu následujících konfigurací:

* Vytvoření účtu Spustit v Azure jako pomocí certifikátu podepsaného svým držitelem
* Vytvoření účtu Spustit jako a účtu Spustit jako pro Classic pomocí certifikátu podepsaného svým držitelem
* Vytvoření účtu Spustit jako a účtu Spustit jako pro Classic pomocí certifikátu, který vydala vaše podniková certifikační autorita
* Vytvoření účtu Spustit jako a účtu Spustit jako pro Classic pomocí certifikátu podepsaného svým držitelem v cloudu Azure Government

>[!NOTE]
> Pokud při vytváření účtu Spustit jako pro Classic vyberete libovolnou z těchto možností, po spuštění skriptu nahrajte veřejný certifikát (soubor s příponou .cer) do úložiště správy předplatného, ve kterém byl účet Automation vytvořený.

1. Uložte následující skript do počítače. V tomto příkladu ho uložte s názvem *New-RunAsAccount.ps1*.

   Skript k vytváření prostředků používá několik rutin Azure Resource Manager. Předchozí tabulka [oprávnění](#permissions) zobrazuje rutiny a jejich potřebná oprávnění.

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
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId)
        # Requires Application administrator or GLOBAL ADMIN
        $ApplicationCredential = New-AzureRmADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
        # Requires Application administrator or GLOBAL ADMIN
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
        # Requires User Access Administrator or Owner.
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6) {
            Sleep -s 10
            New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
            $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
            $Retries++;
        }
        return $Application.ApplicationId.ToString();
    }

    function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
    }

    function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
    }

    Import-Module AzureRM.Profile
    Import-Module AzureRM.Resources

    $AzureRMProfileVersion = (Get-Module AzureRM.Profile).Version
    if (!(($AzureRMProfileVersion.Major -ge 3 -and $AzureRMProfileVersion.Minor -ge 4) -or ($AzureRMProfileVersion.Major -gt 3))) {
        Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
        return
    }

    # To use the new Az modules to create your Run As accounts please uncomment the following lines and ensure you comment out the previous 8 lines that import the AzureRM modules to avoid any issues. To learn about about using Az modules in your Automation Account see https://docs.microsoft.com/azure/automation/az-modules

    # Import-Module Az.Automation
    # Enable-AzureRmAlias


    Connect-AzureRmAccount -Environment $EnvironmentName
    $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

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
    $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
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

    > [!IMPORTANT]
    > **Add-AzureRmAccount** je teď alias pro **Connect-AzureRmAccount**. Pokud se při hledání položek knihovny nezobrazí **příkaz Connect-AzureRMAccount**, můžete použít příkaz **Add-AzureRMAccount**nebo můžete [aktualizovat moduly](automation-update-azure-modules.md) v účtu Automation.

1. Na svém počítači na obrazovce **Start** spusťte **Windows PowerShell** se zvýšenými uživatelskými právy.
1. V prostředí příkazového řádku se zvýšenými oprávněními přejděte do složky, která obsahuje skript vytvořený v kroku 1.
1. Spusťte tento skript s využitím hodnot parametrů pro požadovanou konfiguraci.

    **Vytvoření účtu Spustit v Azure jako pomocí certifikátu podepsaného svým držitelem**

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
    ```

    **Vytvoření účtu Spustit jako a účtu Spustit jako pro Classic pomocí certifikátu podepsaného svým držitelem**

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
    ```

    **Vytvoření účtu Spustit jako a účtu Spustit jako pro Classic pomocí podnikového certifikátu**

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
    ```

    **Vytvoření účtu Spustit jako a účtu Spustit jako pro Classic pomocí certifikátu podepsaného svým držitelem v cloudu Azure Government**

    ```powershell
    .\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
    ```

    > [!NOTE]
    > Po spuštění skriptu se zobrazí výzva k ověření pomocí Azure. Přihlaste se pomocí účtu, který je členem role správců předplatného a spolusprávcem předplatného.

Po úspěšném spuštění skriptu je třeba počítat s následujícím:

* Pokud jste vytvořili účet Spustit jako pro Classic s využitím veřejného certifikátu podepsaného svým držitelem (soubor .cer), skript ho vytvoří a uloží ve složce dočasných souborů ve vašem počítači pod profilem uživatele *%USERPROFILE%\AppData\Local\Temp*, který používáte ke spuštění relace PowerShellu.

* Pokud jste vytvořili účet Spustit jako pro Classic s využitím podnikového veřejného certifikátu (soubor .cer), použijte tento certifikát. Postupujte podle pokynů pro [nahrání certifikátu rozhraní API pro správu do Azure Portal](../azure-api-management-certs.md).

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Odstranění účet Spustit jako nebo Spustit jako pro Classic

Tato část popisuje, jak odstranit a znovu vytvořit účet Spustit jako nebo účet Spustit jako pro Classic. Při provedení této akce se účet Automation uchová. Odstraněný účet Spustit jako nebo účet Spustit jako pro Classic můžete znovu vytvořit na webu Azure Portal.

1. Na webu Azure Portal otevřete účet Automation.

2. Na stránce **Účet Automation** vyberte **Účty Spustit jako**.

3. Na stránce vlastností **Účty Spustit jako** vyberte účet Spustit jako nebo účet Spustit jako pro Classic, který chcete odstranit. Potom v podokně **Vlastnosti** vybraného účtu klikněte na **Odstranit**.

   ![Odstranění účtu Spustit jako](media/manage-runas-account/automation-account-delete-runas.png)

1. Zatímco se účet odstraňuje, můžete průběh sledovat v nabídce v části **Oznámení**.

1. Účet po odstranění můžete znovu vytvořit na stránce vlastností **Účty Spustit jako** výběrem možnosti Vytvořit v části **Účet Spustit jako pro Azure**.

   ![Znovuvytvoření účtu Automation Spustit jako](media/manage-runas-account/automation-account-create-runas.png)

## <a name="cert-renewal"></a>Obnovení certifikátu podepsaného svým držitelem

V určitém okamžiku před vypršením platnosti účtu Spustit jako je nutné certifikát obnovit. Pokud se domníváte, že zabezpečení účtu Spustit jako je ohrožené, můžete ho odstranit a vytvořit znovu. Tato část popisuje, jak tyto operace provést.

Platnost certifikátu podepsaného svým držitelem, který jste vytvořili pro účet Spustit jako, vyprší jeden rok od data jeho vytvoření. Před vypršením platnosti ho můžete kdykoli obnovit. Při obnovení se zachová aktuální platný certifikát, aby se zajistilo, že všechny Runbooky, které jsou ve frontě nebo aktivně spuštěné a které se ověřují pomocí účtu Spustit jako, nejsou negativně ovlivněny. Certifikát zůstane platný až do data vypršení jeho platnosti.

> [!NOTE]
> Pokud jste svůj účet Automation Spustit jako nakonfigurovali k používání certifikátu vydaného podnikovou certifikační autoritou a použijete tuto možnost, tento podnikový certifikát se nahradí certifikátem podepsaným svým držitelem.

Pokud chcete certifikát obnovit, postupujte takto:

1. Na webu Azure Portal otevřete účet Automation.

1. V části **Nastavení účtu**vyberte **účty Spustit jako** .

    ![Podokno vlastností účtu Automation](media/manage-runas-account/automation-account-properties-pane.png)

1. Na stránce vlastností **Účty Spustit jako** vyberte účet Spustit jako nebo účet Spustit jako pro Classic, pro který chcete obnovit certifikát.

1. V podokně **Vlastnosti** vybraného účtu klikněte na **Obnovit certifikát**.

    ![Obnovení certifikátu pro účet Spustit jako](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Zatímco se certifikát obnovuje, můžete průběh sledovat v nabídce v části **Oznámení**.

## <a name="auto-cert-renewal"></a>Nastavení automatického obnovení certifikátu pomocí Runbooku služby Automation

K automatickému obnovení certifikátů můžete použít Runbook služby Automation. Následující skript na [GitHubu](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) tuto funkci umožňuje ve vašem účtu Automation.

- Skript `GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1` vytvoří týdenní plán pro obnovení certifikátů účtu Spustit jako.
- Skript přidá do svého účtu Automation Runbook **Update-AutomationRunAsCredential** .
  - Můžete také zobrazit kód Runbooku na GitHubu ve skriptu: [Update-AutomationRunAsCredential. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1).
  - V souboru můžete také použít kód PowerShellu k ručnímu obnovení certifikátů podle potřeby.

K okamžitému otestování procesu obnovení použijte následující postup:

1. Upravte sadu Runbook **Update-AutomationRunAsCredential** a na řádku 122 vložte znak komentáře (`#`), jak `Exit(1)` je znázorněno níže.

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

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. Po dokončení testu upravte Runbook a odeberte znak komentáře, který jste přidali v **kroku 1**.
6. **Publikujte** Runbook.

> [!NOTE]
> Abyste mohli skript spustit, musíte být **globálním správcem** nebo **správcem společnosti** v Azure Active Directory.

## <a name="limiting-run-as-account-permissions"></a>Omezení oprávnění účtu Spustit jako

Chcete-li řídit cílení automatizace proti prostředkům v Azure, můžete spustit skript [Update-AutomationRunAsAccountRoleAssignments. ps1](https://aka.ms/AA5hug8) v galerii prostředí PowerShell a změnit stávající objekt služby účtu Spustit jako pro vytvoření a použití vlastní definice role. Tato role bude mít oprávnění pro všechny prostředky kromě [Key Vault](https://docs.microsoft.com/azure/key-vault/).

> [!IMPORTANT]
> Po spuštění skriptu `Update-AutomationRunAsAccountRoleAssignments.ps1` nebudou sady Runbook, které přistupují k trezoru klíčů prostřednictvím použití účtů RunAs, nadále fungovat. V rámci svého účtu byste měli zkontrolovat Runbooky pro volání do Azure webrecovery.
>
> Pokud chcete povolit přístup k trezoru klíčů z Azure Automation Runbooky, budete muset [Přidat účet RunAs do oprávnění trezoru](#add-permissions-to-key-vault)klíčů.

Pokud potřebujete omezit, co může instanční objekt RunAs dále dělat, můžete přidat další typy prostředků do `NotActions` definice vlastní role. Následující příklad omezuje přístup k `Microsoft.Compute`. Pokud toto přidáte do **NotActions** definice role, tato role nebude mít přístup k žádnému výpočetnímu prostředku. Další informace o definicích rolí najdete v tématu [vysvětlení definic rolí pro prostředky Azure](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzureRmRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzureRMRoleDefinition
```

Pokud chcete zjistit, jestli se instanční objekt používaný účtem spustit jako nachází v rámci definice **přispěvatele** nebo vlastní role, klikněte na účet Automation a v části **Nastavení účtu**vyberte **účty Spustit jako** > **účet Spustit**jako pro Azure. V části **role** najdete definici role, která se používá.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

Chcete-li zjistit definici role používané účty Spustit jako služby Automation pro více předplatných nebo účtů služby Automation, můžete použít skript [check-AutomationRunAsAccountRoleAssignments. ps1](https://aka.ms/AA5hug5) v Galerie prostředí PowerShell.

### <a name="add-permissions-to-key-vault"></a>Přidat oprávnění pro Key Vault

Pokud chcete Azure Automation spravovat Key Vault a váš instanční objekt účtu Spustit jako používá vlastní definici role, budete muset provést další kroky, abyste toto chování povolili:

* Udělit oprávnění k Key Vault
* Nastavení zásad přístupu

Skript [extend-AutomationRunAsAccountRoleAssignmentToKeyVault. ps1](https://aka.ms/AA5hugb) můžete v Galerie prostředí PowerShell použít k udělení oprávnění účtu Spustit jako k trezoru klíčů. Další informace o nastavení oprávnění k trezoru klíčů najdete v [udělení přístupu aplikací k trezoru klíčů](../key-vault/key-vault-group-permissions-for-apps.md) .

## <a name="misconfiguration"></a>Chybná konfigurace

Může se stát, že se během prvotního nastavení nesprávně vytvoří nebo později odstraní některá z položek konfigurace nezbytných pro správné fungování účtu Spustit jako nebo Spustit jako pro Classic. Mezi tyto položky patří:

* Asset certifikátu
* Asset připojení
* Účet Spustit jako byl odebrán z role přispěvatele
* Instanční objekt nebo aplikace v Azure AD

V předchozí a dalších instancích chybné konfigurace účet Automation zjistí změny a v podokně vlastností **Účty Spustit jako** příslušného účtu zobrazí stav *Nedokončeno*.

![Nedokončená konfigurace účtu Spustit jako](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Pokud vyberete tento účet Spustit jako, v podokně **Vlastnosti** účtu se zobrazí následující chybová zpráva:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Tyto potíže s účtem Spustit jako můžete rychle vyřešit jeho odstraněním a znovuvytvořením.

## <a name="next-steps"></a>Další kroky

* Další informace o instančních objektech naleznete v tématu [Application Objects and instanční objekty](../active-directory/develop/app-objects-and-service-principals.md).
* Další informace o certifikátech a službách Azure najdete v tématu [Přehled certifikátů pro azure Cloud Services](../cloud-services/cloud-services-certs-create.md).
