---
title: "Vytváření účtů Azure Automation Spustit jako | Dokumentace Microsoftu"
description: "Tento článek popisuje postup aktualizace účtu Automation a vytváření účtů Spustit jako pomocí PowerShellu nebo z portálu."
services: automation
documentationcenter: 
author: georgewallace
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: gwallace
ms.openlocfilehash: 467ff5ae3a9478c32ba212774cf7040f03dec00a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="update-your-automation-account-authentication-with-run-as-accounts"></a>Aktualizace ověřování účtu Automation o účty Spustit jako 
Existující účet Automation můžete aktualizovat z webu Azure Portal nebo pomocí PowerShellu, pokud jste postupovali takto:

* Vytvořili jste účet Automation, ale odmítli jste vytvoření účtu Spustit jako.
* Už máte účet Automation pro správu prostředků Resource Manageru a chcete ho aktualizovat, aby zahrnoval účet Spustit jako pro ověřování runbooků.
* Už máte účet Automation pro správu klasických prostředků a chcete ho aktualizovat, abyste mohli použít účet Spustit jako pro Classic a nemuseli vytvářet nový účet a migrovat na něj runbooky a prostředky.   

Tento proces vytvoří ve vašem účtu Automation následující položky.

**Pro účty Spustit jako:**

* Vytvoří aplikaci Azure AD s certifikátem podepsaným svým držitelem, vytvoří účet instančního objektu pro tuto aplikaci v Azure AD a přiřadí roli přispěvatele pro tento účet v aktuálním předplatném. Toto nastavení můžete změnit na roli Vlastník nebo libovolnou jinou roli. Další informace najdete v tématu [Řízení přístupu na základě role ve službě Azure Automation](automation-role-based-access-control.md).
* V příslušném účtu Automation vytvoří prostředek certifikátu Automation s názvem *AzureRunAsCertificate*. Prostředek certifikátu obsahuje privátní klíč certifikátu, který používá aplikace Azure AD.
* V příslušném účtu Automation vytvoří prostředek připojení Automation s názvem *AzureRunAsConnection*. Prostředek připojení obsahuje parametry applicationId, tenantId, subscriptionId a certificate thumbprint.

**Pro účet Spustit jako pro Classic:**

* V příslušném účtu Automation vytvoří prostředek certifikátu Automation s názvem *AzureClassicRunAsCertificate*. Prostředek certifikátu obsahuje privátní klíč certifikátu, který používá certifikát pro správu.
* V příslušném účtu Automation vytvoří prostředek připojení Automation s názvem *AzureClassicRunAsConnection*. Prostředek propojení obsahuje název a ID předplatného a název prostředku certifikátu.

## <a name="prerequisites"></a>Požadavky
Pokud se rozhodnete [k vytvoření účtu Spustit jako použít PowerShell](#create-run-as-account-using-powershell), tento postup vyžaduje:

* Windows 10 a Windows Server 2016 s nainstalovanými moduly Azure Resource Manageru verze 3.4.1 nebo novější. Skript PowerShellu nepodporuje dřívější verze Windows.
* Azure PowerShell 1.0 nebo novější. Informace o vydání PowerShellu 1.0 najdete v článku [Postup instalace a konfigurace Azure PowerShellu](/powershell/azureps-cmdlets-docs).
* Účet Automation, na který se odkazuje jako na hodnotu parametrů *-AutomationAccountName* a *-ApplicationDisplayName*.

Abyste získali hodnoty pro parametry *SubscriptionID*, *ResourceGroup* a *AutomationAccountName*, které jsou pro skript povinné, postupujte takto:

1. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Automation**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Účty Automation**.
2. Na stránce účtu Automation vyberte váš účet Automation a potom v části **Nastavení účtu** vyberte **Vlastnosti**.  
3. Hodnoty na stránce **Vlastnosti** si poznamenejte.<br><br> ![Podokno vlastností účtu Automation](media/automation-create-runas-account/automation-account-properties.png)  

### <a name="required-permissions-to-update-your-automation-account"></a>Požadovaná oprávnění k aktualizaci účtu Automation
Pokud chcete aktualizovat účet Automation, musíte mít následující specifická oprávnění vyžadovaná k dokončení tohoto tématu.   
 
* Váš uživatelský účet AD musí být přidána do role s oprávnění na role Přispěvatel pro Microsoft.Automation prostředky podle uvedených v článku [řízení přístupu na základě Role ve službě Azure Automation](automation-role-based-access-control.md#contributor).  
* Uživatelé ve vašem tenantovi Azure AD, kteří nejsou správci, můžou [registrovat aplikace AD](../azure-resource-manager/resource-group-create-service-principal-portal.md#check-azure-subscription-permissions), pokud je možnost **Uživatelé můžou registrovat aplikace** na stránce **Uživatelská nastavení** pro vašeho tenanta Azure AD nastavená na **Ano**. Pokud je nastavení Registrace aplikací nastaveno na **Ne**, uživatel provádějící tuto akci musí být globálním správcem služby Azure AD.

Pokud před přidáním do role globálního správce nebo spolusprávce nejste členem instance Active Directory příslušného předplatného, budete do služby Active Directory přidaní jako host. V takové situaci se zobrazí upozornění Nemáte oprávnění k vytvoření... v okně **Přidání účtu Automation**. Uživatele, kteří byli nejdřív přidaní do role globálního správce nebo spolusprávce, je možné z instance Active Directory předplatného odebrat a potom je znovu přidat – tak se z nich ve službě Active Directory stanou úplní uživatelé. Takovou situaci můžete ověřit v podokně **Azure Active Directory** na webu Azure Portal. Vyberte **Uživatelé a skupiny**, potom **Všichni uživatelé** a po výběru konkrétního uživatele vyberte **Profil**. Hodnota atributu **Typ uživatele** v profilu uživatele by neměla být **Host**.

## <a name="create-run-as-account-from-the-portal"></a>Vytvoření účtu Spustit jako z portálu
V této části provedete následující kroky a aktualizujete svůj účet Azure Automation na webu Azure Portal. Vytvoříte samostatně účet Spustit jako a účet Spustit jako pro Azure Classic. Pokud nepotřebujete spravovat klasické prostředky, můžete vytvořit jenom účet Spustit jako pro Azure.  

1. Přihlaste se k webu Azure Portal pomocí účtu, který je členem role správců předplatného a spolusprávcem předplatného.
2. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Automation**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Účty Automation**.
3. Na stránce **Účty Automation** vyberte ze seznamu svůj účet Automation.
4. V levém podokně vyberte **Účty Spustit jako** v části **Nastavení účtu**.  
5. Podle toho, který účet požadujete, vyberte buď **Účet Spustit jako pro Azure**, nebo **Účet Spustit jako pro Azure Classic**. Po výběru se zobrazí podokno **Přidat účet Spustit jako pro Azure** nebo **Přidat účet Spustit jako pro Azure Classic**. Jakmile zkontrolujete souhrnné informace, klikněte na **Vytvořit** a pokračujte ve vytváření účtu Spustit jako.  
6. Zatímco Azure vytváří účet Spustit jako, můžete průběh sledovat v nabídce v části **Oznámení**. Zobrazí se také nápis informující, že je účet vytvořený. Dokončení tohoto procesu může trvat několik minut.  

## <a name="create-run-as-account-using-powershell-script"></a>Vytvoření účtu Spustit jako pomocí skriptu PowerShellu
Tento skript PowerShellu zahrnuje podporu následujících konfigurací:

* Vytvoření účtu Spustit v Azure jako pomocí certifikátu podepsaného svým držitelem
* Vytvoření účtu Spustit jako a účtu Spustit jako pro Classic pomocí certifikátu podepsaného svým držitelem
* Vytvoření účtu Spustit jako a účtu Spustit jako pro Classic pomocí certifikátu, který vydala vaše podniková certifikační autorita
* Vytvoření účtu Spustit jako a účtu Spustit jako pro Classic pomocí certifikátu podepsaného svým držitelem v cloudu Azure Government

>[!NOTE]
> Pokud při vytváření účtu Spustit jako pro Classic vyberete libovolnou z těchto možností, po spuštění skriptu nahrajte veřejný certifikát (soubor s příponou .cer) do úložiště správy předplatného, ve kterém byl účet Automation vytvořený.
> 

1. Uložte následující skript do počítače. V tomto příkladu ho uložte s názvem *New-RunAsAccount.ps1*.

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
        [String] $EnterpriseCertPathForRunAsAccount,
        
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
        
        $startDate = Get-Date
        $endDate = (Get-Date $PfxCert.GetExpirationDateString()).AddDays(-1)
        
        #Create an Azure AD application, AD App Credential, AD ServicePrincipal
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId) 
        $ApplicationCredential = New-AzureRmADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $startDate -EndDate $endDate 
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id
        
        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
        Sleep -s 15
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
        
    Login-AzureRmAccount -Environment $EnvironmentName 
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

2. Na svém počítači na obrazovce **Start** spusťte **Windows PowerShell** se zvýšenými uživatelskými právy.
3. V prostředí příkazového řádku se zvýšenými oprávněními přejděte do složky, která obsahuje skript vytvořený v kroku 1.  
4. Spusťte tento skript s využitím hodnot parametrů pro požadovanou konfiguraci.

    **Vytvoření účtu Spustit v Azure jako pomocí certifikátu podepsaného svým držitelem**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false`

    **Vytvoření účtu Spustit jako a účtu Spustit jako pro Classic pomocí certifikátu podepsaného svým držitelem**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true`

    **Vytvoření účtu Spustit jako a účtu Spustit jako pro Classic pomocí podnikového certifikátu**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>`

    **Vytvoření účtu Spustit jako a účtu Spustit jako pro Classic pomocí certifikátu podepsaného svým držitelem v cloudu Azure Government**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment`

    > [!NOTE]
    > Po spuštění skriptu se zobrazí výzva k ověření pomocí Azure. Přihlaste se pomocí účtu, který je členem role správců předplatného a spolusprávcem předplatného.
    >
    >

Po úspěšném spuštění skriptu je třeba počítat s následujícím:
* Pokud jste vytvořili účet Spustit jako pro Classic s využitím veřejného certifikátu podepsaného svým držitelem (soubor .cer), skript ho vytvoří a uloží ve složce dočasných souborů ve vašem počítači pod profilem uživatele *%USERPROFILE%\AppData\Local\Temp*, který používáte ke spuštění relace PowerShellu.
* Pokud jste vytvořili účet Spustit jako pro Classic s využitím podnikového veřejného certifikátu (soubor .cer), použijte tento certifikát. Postupujte podle pokynů pro [certifikát správy rozhraní API se nahrávají na portálu Azure](../azure-api-management-certs.md)a potom ověřit konfiguraci přihlašovacích údajů s prostředky nasazení classic pomocí [ukázkový kód pro ověření s prostředky Azure nasazení Classic](automation-verify-runas-authentication.md#classic-run-as-authentication). 
* Pokud jste *nevytvořili* účet Spustit jako pro Classic, použijte k ověření pomocí prostředků Resource Manageru a ke kontrole konfigurace přihlašovacích údajů [ukázkový kód pro ověření s využitím prostředků správy služeb](automation-verify-runas-authentication.md#automation-run-as-authentication).

## <a name="limiting-run-as-account-permissions"></a>Omezení oprávnění účtu spustit jako

K řízení cílení automatizace s prostředky ve službě Azure Automation, účet Spustit jako ve výchozím nastavení je oprávnění přispěvatele v rámci předplatného. Pokud potřebujete omezit, co můžete dělat instančního objektu RunAs, můžete odebrat účet z role Přispěvatel k odběru a přidejte jej jako Přispěvatel do skupiny prostředků, které chcete zadat.

Na portálu Azure vyberte **odběry** a zvolte předplatné účtu Automation. Vyberte **přístup k ovládacímu prvku (IAM)** a vyhledejte objekt služby pro svůj účet Automation (to vypadá \<AutomationAccountName\>identifikátor _unique). Vyberte účet a klikněte na tlačítko **odebrat** ho odebrat z předplatného.

![Přispěvatelé předplatného](media/automation-create-runas-account/automation-account-remove-subscription.png)

Objekt služby přidat do skupiny prostředků, vyberte skupinu prostředků ve službě Azure portálu a vyberte možnost **přístup k ovládacímu prvku (IAM)**. Vyberte **přidat**, otevře se **přidat oprávnění** stránky. Pro **Role**, vyberte **Přispěvatel**. V **vyberte** textového pole zadejte název objektu služby pro účet Spustit jako a vyberte ho ze seznamu. Kliknutím na **Uložit** uložte změny. To lze proveďte pro skupiny prostředků, které chcete poskytnout vaše spustit v Azure Automation jako služba hlavní přístup.

## <a name="next-steps"></a>Další postup
* Další informace o objektech služby najdete v tématu [objekty aplikací a hlavní objekty služeb](../active-directory/active-directory-application-objects.md).
* Další informace o certifikátech a službám Azure najdete v tématu [Přehled certifikátů pro Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).
