---
title: Správa účtů služby Automation spustit jako pro Azure
description: Tento článek popisuje, jak spravovat účty spustit jako pomocí Powershellu nebo z portálu.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 09/12/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a8821b2e1be10cddafba04109041e76ef65f6a6a
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47433697"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Správa účtů služby Automation spustit jako pro Azure

Účty spustit jako ve službě Azure Automation se používají k zajištění ověřování pro správu prostředků v Azure pomocí rutin Azure.

Když vytvoříte účet Spustit jako, vytvoří nový uživatel instančního objektu služby v Azure Active Directory a přiřadí roli přispěvatele k tomuto uživateli na úrovni předplatného. Pro sady runbook, který používat Hybrid Runbook Worker na virtuálních počítačích Azure, můžete použít [spravovaných identit pro prostředky Azure](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) místo účtů spustit jako k ověření vašich prostředků Azure.

Existují dva typy účtů spustit jako:

* **Azure účet Spustit jako** – tento účet se používá ke správě prostředků modelu nasazení Resource Manageru.
  * Vytvoří aplikaci Azure AD s certifikátem podepsaným svým držitelem, vytvoří účet instančního objektu pro tuto aplikaci v Azure AD a přiřadí roli přispěvatele pro tento účet v aktuálním předplatném. Toto nastavení můžete změnit na roli Vlastník nebo libovolnou jinou roli. Další informace najdete v tématu [Řízení přístupu na základě role ve službě Azure Automation](automation-role-based-access-control.md).
  * V příslušném účtu Automation vytvoří prostředek certifikátu Automation s názvem *AzureRunAsCertificate*. Prostředek certifikátu obsahuje privátní klíč certifikátu, který používá aplikace Azure AD.
  * V příslušném účtu Automation vytvoří prostředek připojení Automation s názvem *AzureRunAsConnection*. Prostředek připojení obsahuje parametry applicationId, tenantId, subscriptionId a certificate thumbprint.

* **Azure Classic účet Spustit jako** – tento účet slouží ke správě prostředků modelu nasazení Classic.
  * V příslušném účtu Automation vytvoří prostředek certifikátu Automation s názvem *AzureClassicRunAsCertificate*. Prostředek certifikátu obsahuje privátní klíč certifikátu, který používá certifikát pro správu.
  * V příslušném účtu Automation vytvoří prostředek připojení Automation s názvem *AzureClassicRunAsConnection*. Prostředek propojení obsahuje název a ID předplatného a název prostředku certifikátu.

## <a name="permissions"></a>Oprávnění ke konfiguraci účtů spustit jako

Vytvořit nebo aktualizovat účet Spustit jako, musí mít specifické oprávnění a oprávnění. Globální správce nebo Spolusprávce může dokončení všech úloh. V situaci, kdy máte seperation povinností následující tabulka uvádí seznam úkolů, ekvivalentní rutinu a potřebná oprávnění:

|Úkol|Rutina  |Minimální oprávnění  |
|---|---------|---------|
|Vytvoření aplikace Azure AD|[New-AzureRmADApplication](/powershell/module/azurerm.resources/new-azurermadapplication)     | Role pro vývojáře aplikací        |
|Přidání přihlašovacích údajů k aplikaci.|[New-AzureRmADAppCredential](/powershell/module/AzureRM.Resources/New-AzureRmADAppCredential)     | Správce aplikace nebo globální správce         |
|Vytvořte a získejte instanční objekt Azure AD|[Nové AzureRMADServicePrincipal](/powershell/module/AzureRM.Resources/New-AzureRmADServicePrincipal)</br>[Get-AzureRmADServicePrincipal](/powershell/module/AzureRM.Resources/Get-AzureRmADServicePrincipal)     | Správce aplikace nebo globální správce        |
|Přiřazení nebo získat roli RBAC pro zadaný objekt zabezpečení|[Nový AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment)</br>[Get-AzureRMRoleAssignment](/powershell/module/AzureRM.Resources/Get-AzureRmRoleAssignment)      | Správce uživatelských přístupů nebo vlastníka        |
|Vytvořit nebo odebrat na server certifikát služby Automation|[New-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/New-AzureRmAutomationCertificate)</br>[Remove-AzureRmAutomationCertificate](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationCertificate)     | Přispěvatelem skupiny prostředků         |
|Vytvoření nebo odebrání připojení služby Automation|[New-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/New-AzureRmAutomationConnection)</br>[Remove-AzureRmAutomationConnection](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationConnection)|Přispěvatelem skupiny prostředků |

* Uživatelský účet AD s oprávněními role přispěvatele pro prostředky Microsoft.Automation, jak je uvedeno v článku [řízení přístupu na základě rolí ve službě Azure Automation](automation-role-based-access-control.md#contributor).  
* Uživatelé ve vašem tenantovi Azure AD, kteří nejsou správci, můžou [registrovat aplikace AD](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions), pokud je možnost **Uživatelé můžou registrovat aplikace** na stránce **Uživatelská nastavení** pro vašeho tenanta Azure AD nastavená na **Ano**. Pokud je nastavení Registrace aplikací nastaveno na **Ne**, uživatel provádějící tuto akci musí být globálním správcem služby Azure AD.

Pokud před přidáním do role globálního správce nebo spolusprávce nejste členem instance Active Directory příslušného předplatného, budete do služby Active Directory přidaní jako host. V takovém případě se zobrazí `You do not have permissions to create…` upozornění na **přidat účet Automation** stránky. Uživatele, kteří byli nejdřív přidaní do role globálního správce nebo spolusprávce, je možné z instance Active Directory předplatného odebrat a potom je znovu přidat – tak se z nich ve službě Active Directory stanou úplní uživatelé. Takovou situaci můžete ověřit v podokně **Azure Active Directory** na webu Azure Portal. Vyberte **Uživatelé a skupiny**, potom **Všichni uživatelé** a po výběru konkrétního uživatele vyberte **Profil**. Hodnota atributu **Typ uživatele** v profilu uživatele by neměla být **Host**.

## <a name="create-a-run-as-account-in-the-portal"></a>Vytvoření účtu spustit jako na portálu

V této části provedete následující kroky a aktualizujete svůj účet Azure Automation na webu Azure Portal. Vytvoříte samostatně účet Spustit jako a účet Spustit jako pro Azure Classic. Pokud nepotřebujete spravovat klasické prostředky, můžete vytvořit jenom účet Spustit jako pro Azure.  

1. Přihlaste se k webu Azure Portal pomocí účtu, který je členem role správců předplatného a spolusprávcem předplatného.
1. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Automation**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Účty Automation**.
1. Na stránce **Účty Automation** vyberte ze seznamu svůj účet Automation.
1. V levém podokně vyberte **Účty Spustit jako** v části **Nastavení účtu**.  
1. Podle toho, který účet požadujete, vyberte buď **Účet Spustit jako pro Azure**, nebo **Účet Spustit jako pro Azure Classic**. Po výběru se zobrazí podokno **Přidat účet Spustit jako pro Azure** nebo **Přidat účet Spustit jako pro Azure Classic**. Jakmile zkontrolujete souhrnné informace, klikněte na **Vytvořit** a pokračujte ve vytváření účtu Spustit jako.  
1. Zatímco Azure vytváří účet Spustit jako, můžete průběh sledovat v nabídce v části **Oznámení**. Zobrazí se také nápis informující, že je účet vytvořený. Dokončení tohoto procesu může trvat několik minut.  

## <a name="create-run-as-account-using-powershell"></a>Vytvoření účtu spustit jako pomocí Powershellu

## <a name="prerequisites"></a>Požadavky

Následující seznam uvádí požadavky pro vytvoření účtu spustit jako v prostředí PowerShell:

* Windows 10 nebo Windows Server 2016 s nainstalovanými moduly Azure Resource Manageru verze 3.4.1 nebo novější. Skript PowerShellu nepodporuje dřívější verze Windows.
* Azure PowerShell 1.0 nebo novější. Informace o vydání PowerShellu 1.0 najdete v článku [Postup instalace a konfigurace Azure PowerShellu](/powershell/azureps-cmdlets-docs).
* Účet Automation, na který se odkazuje jako na hodnotu parametrů *-AutomationAccountName* a *-ApplicationDisplayName*.
* Oprávnění na jaký je uveden v [požadovaná oprávnění ke konfiguraci účtů spustit jako](#permissions)

Abyste získali hodnoty pro parametry *SubscriptionID*, *ResourceGroup* a *AutomationAccountName*, které jsou pro skript povinné, postupujte takto:

1. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Automation**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Účty Automation**.
1. Na stránce účtu Automation vyberte váš účet Automation a potom v části **Nastavení účtu** vyberte **Vlastnosti**.  
1. Poznámka: **ID předplatného**, **název**, a **skupiny prostředků** hodnoty na **vlastnosti** stránky.

   ![Stránka "Properties" účtu Automation](media/manage-runas-account/automation-account-properties.png)

Tento skript PowerShellu zahrnuje podporu následujících konfigurací:

* Vytvoření účtu Spustit v Azure jako pomocí certifikátu podepsaného svým držitelem
* Vytvoření účtu Spustit jako a účtu Spustit jako pro Classic pomocí certifikátu podepsaného svým držitelem
* Vytvoření účtu Spustit jako a účtu Spustit jako pro Classic pomocí certifikátu, který vydala vaše podniková certifikační autorita
* Vytvoření účtu Spustit jako a účtu Spustit jako pro Classic pomocí certifikátu podepsaného svým držitelem v cloudu Azure Government

>[!NOTE]
> Pokud při vytváření účtu Spustit jako pro Classic vyberete libovolnou z těchto možností, po spuštění skriptu nahrajte veřejný certifikát (soubor s příponou .cer) do úložiště správy předplatného, ve kterém byl účet Automation vytvořený.

1. Uložte následující skript do počítače. V tomto příkladu ho uložte s názvem *New-RunAsAccount.ps1*.

   Tento skript využívá více rutiny Azure Resource Manageru k vytváření prostředků. Následující tabulka uvádí rutiny a jejich potřebná oprávnění.

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
    > **Add-AzureRmAccount** je nyní alias pro **Connect-AzureRMAccount**. Při vyhledávání knihovny položky, pokud se nezobrazí **Connect-AzureRMAccount**, můžete použít **Add-AzureRmAccount**, nebo můžete [aktualizovat moduly](automation-update-azure-modules.md) ve vaší službě Automation Účet.

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

* Pokud jste vytvořili účet Spustit jako pro Classic s využitím podnikového veřejného certifikátu (soubor .cer), použijte tento certifikát. Postupujte podle pokynů pro [odeslání certifikátu rozhraní API pro správu na webu Azure portal](../azure-api-management-certs.md). () Automation-Verify-runas-Authentication.MD#Classic-Run-as-Authentication).

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Odstranění účet Spustit jako nebo Spustit jako pro Classic

Tato část popisuje, jak odstranit a znovu vytvořit účet Spustit jako nebo účet Spustit jako pro Classic. Při provedení této akce se účet Automation uchová. Odstraněný účet Spustit jako nebo účet Spustit jako pro Classic můžete znovu vytvořit na webu Azure Portal.

1. Na webu Azure Portal otevřete účet Automation.

1. Na stránce **Účet Automation** vyberte **Účty Spustit jako**.

1. Na stránce vlastností **Účty Spustit jako** vyberte účet Spustit jako nebo účet Spustit jako pro Classic, který chcete odstranit. Potom v podokně **Vlastnosti** vybraného účtu klikněte na **Odstranit**.

 ![Odstranění účtu Spustit jako](media/manage-runas-account/automation-account-delete-runas.png)

1. Zatímco se účet odstraňuje, můžete průběh sledovat v nabídce v části **Oznámení**.

1. Účet po odstranění můžete znovu vytvořit na stránce vlastností **Účty Spustit jako** výběrem možnosti Vytvořit v části **Účet Spustit jako pro Azure**.

 ![Znovuvytvoření účtu Automation Spustit jako](media/manage-runas-account/automation-account-create-runas.png)

## <a name="cert-renewal"></a>Obnovení certifikátu podepsaného svým držitelem

V určitém okamžiku před vypršením platnosti účtu spustit jako, musíte obnovit certifikát. Pokud se domníváte, že zabezpečení účtu Spustit jako je ohrožené, můžete ho odstranit a vytvořit znovu. Tato část popisuje, jak tyto operace provést.

Platnost certifikátu podepsaného svým držitelem, který jste vytvořili pro účet Spustit jako, vyprší jeden rok od data jeho vytvoření. Před vypršením platnosti ho můžete kdykoli obnovit. Když ho obnovíte, aktuální platný certifikát se uchová, aby se zajistilo, že to negativně neovlivní runbooky ověřované účtem Spustit jako, které jsou právě ve frontě nebo aktivně spuštěné. Certifikát zůstane platný až do data vypršení jeho platnosti.

> [!NOTE]
> Pokud jste svůj účet Automation Spustit jako nakonfigurovali k používání certifikátu vydaného podnikovou certifikační autoritou a použijete tuto možnost, tento podnikový certifikát se nahradí certifikátem podepsaným svým držitelem.

Pokud chcete certifikát obnovit, postupujte takto:

1. Na webu Azure Portal otevřete účet Automation.

1. Vyberte **účty spustit jako** pod **nastavení účtu**.

    ![Podokno vlastností účtu Automation](media/manage-runas-account/automation-account-properties-pane.png)

1. Na stránce vlastností **Účty Spustit jako** vyberte účet Spustit jako nebo účet Spustit jako pro Classic, pro který chcete obnovit certifikát.

1. V podokně **Vlastnosti** vybraného účtu klikněte na **Obnovit certifikát**.

    ![Obnovení certifikátu pro účet Spustit jako](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. Zatímco se certifikát obnovuje, můžete průběh sledovat v nabídce v části **Oznámení**. 

## <a name="limiting-run-as-account-permissions"></a>Omezení oprávnění účtu spustit jako

K řízení cílení služby automation s prostředky ve službě Azure Automation, účet Spustit jako ve výchozím nastavení je povoleno oprávněním přispěvatele v předplatném. Pokud potřebujete omezit, co můžete dělat instančního objektu RunAs, můžete odebrat účet z role přispěvatele k předplatnému a přidat jako přispěvatelé do skupiny prostředků, kterou chcete zadat.

Na webu Azure Portal, vyberte **předplatná** a zvolte předplatné účtu Automation. Vyberte **řízení přístupu (IAM)** a vyhledejte instančním objektu pro svůj účet Automation (vypadá jako \<AutomationAccountName\>_unique identifikátor). Vyberte účet a klikněte na tlačítko **odebrat** odebrat z předplatného.

![Přispěvatelé předplatného](media/manage-runas-account/automation-account-remove-subscription.png)

Přidání instančního objektu do skupiny prostředků, vyberte skupinu prostředků v Azure portal a vyberte **řízení přístupu (IAM)**. Vyberte **přidat**, tím se otevře **přidat oprávnění** stránky. Pro **Role**vyberte **Přispěvatel**. V **vyberte** textového pole zadejte název objektu služby pro účet Spustit jako a vyberte ho ze seznamu. Kliknutím na **Uložit** uložte změny. To lze proveďte pro skupiny prostředků, kterou chcete přidělit Azure Automation spustit jako službu objektu zabezpečení přístup k.

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

## <a name="next-steps"></a>Další postup

* Další informace o instančních objektech najdete v tématu [aplikace a instanční objekty](../active-directory/develop/app-objects-and-service-principals.md).
* Další informace o certifikátech a službách Azure najdete v tématu [Přehled certifikátů pro Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).
