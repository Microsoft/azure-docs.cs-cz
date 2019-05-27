---
title: Automatická aktualizace služby Mobility zotavení po havárii Azure do Azure | Dokumentace Microsoftu
description: Přehled automatické aktualizace služby Mobility při replikaci virtuálních počítačů Azure pomocí Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/20/2019
ms.author: rajanaki
ms.openlocfilehash: 1d36145b2a38c0f1106b4468eab226996e270ae1
ms.sourcegitcommit: d73c46af1465c7fd879b5a97ddc45c38ec3f5c0d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65922169"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Automatická aktualizace služby Mobility v replikaci z Azure do Azure

Azure Site Recovery používá měsíční vydávání verzí opravte všechny problémy a vylepšení stávajících funkcí nebo přidat nové značky. Zůstat aktuální ve službě, je nutné naplánovat nasazení oprava každý měsíc. Pokud chcete vyhnout režie spojené s každou upgrade, můžete místo toho povolit Site Recovery můžete spravovat aktualizace součástí.

Jak je uvedeno v [architektury pro zotavení po havárii Azure do Azure](azure-to-azure-architecture.md), služba Mobility je nainstalovaná na všech virtuálních počítačích Azure (VM) u kterých je povolená replikace při replikaci virtuálních počítačů z jedné oblasti Azure do jiné. Pokud používáte automatické aktualizace, každé nové verzi aktualizace rozšíření služby Mobility.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Pracovní funkce Automatické aktualizace

Když použijete Site Recovery provádět správu aktualizací, nasadí globální sady runbook (používané službami Azure) přes účet automation, který je vytvořen ve stejném předplatném jako trezor. Každý trezor používá jeden účet služby automation. Sada runbook kontroluje u každého virtuálního počítače v trezoru pro aktivní automatické aktualizace a provede upgrade rozšíření služby Mobility, pokud je dostupná novější verze.

Výchozí plán sad runbook se opakuje každý den v 12:00 hodin v časovém pásmu geograficky replikovaný virtuální počítač. Můžete také změnit plán sad runbook pomocí účtu služby automation.

> [!NOTE]
> Počínaje 35 kumulativní aktualizace, můžete stávající účet automation, který chcete použít pro aktualizace. Před aktualizací této Site Recovery tento účet byl vytvořen ve výchozím nastavení. Tato možnost je dostupná, když povolíte replikaci pro virtuální počítač. Pokud změníte nastavení, bude platit pro všechny virtuální počítače Azure chráněná ve stejném trezoru.
 
> Zapnout automatické aktualizace nebude vyžadovat restartování virtuálních počítačů Azure nebo mít vliv na probíhající replikaci.

> Úloha fakturace v účtu automation je podle počtu počet minut běhu úloh, která je použita v daném měsíci. Ve výchozím nastavení jsou zahrnuty jako volných jednotek pro účet automation 500 minut. Provádění úlohy trvá několik sekund na přibližně minutu denně a které pokrývá jako jednotky na úrovni free.

| Zahrnuté volné jednotky (měsíčně) | Cena |
|---|---|
| 500 minut doby běhu úloh | ₹0.14 / min

## <a name="enable-automatic-updates"></a>Povolit automatické aktualizace

Můžete povolit Site Recovery ke správě aktualizací následujícími způsoby.

### <a name="manage-as-part-of-the-enable-replication-step"></a>Spravovat jako součást kroku povolení replikace

Když povolíte replikaci pro virtuální počítač buď počáteční [ze zobrazení virtuálního počítače](azure-to-azure-quickstart.md) nebo [z trezoru služby recovery services](azure-to-azure-how-to-enable-replication.md), můžete povolit buď Site Recovery můžete spravovat aktualizace pro rozšíření služby Site Recovery nebo spravovat účet ručně.

![Nastavení rozšíření](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Přepnout rozšíření aktualizovat nastavení uvnitř tohoto trezoru

1. Uvnitř tohoto trezoru, přejděte na **spravovat** > **infrastruktura Site Recovery**.
2. V části **pro Azure Virtual Machines** > **nastavení aktualizací rozšíření**, zapněte **povolit Site Recovery spravovat** přepínací tlačítko. Chcete-li spravovat ručně, vypněte jej. 
3. Vyberte **Uložit**.

![Nastavení aktualizací rozšíření](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important]
> Při výběru **povolit Site Recovery spravovat**, toto nastavení platí pro všechny virtuální počítače v odpovídající úložiště.


> [!Note]
> Možnost vás upozorní na účtu automation pro správu aktualizací. Pokud používáte tuto funkci v trezoru poprvé, se standardně vytvoří nový účet automation. Alternativně můžete přizpůsobit nastavení a zvolte existující účet automation. Všechny následné povolení replikace ve stejném trezoru použijte dříve vytvořenou.

Vlastní automatizace účet pomocí následujícího skriptu:

```azurepowershell
param(
    [Parameter(Mandatory=$true)]
    [String] $VaultResourceId,

    [Parameter(Mandatory=$true)]
    [ValidateSet("Enabled",'Disabled')]
    [Alias("Enabled or Disabled")]
    [String] $AutoUpdateAction,

    [Parameter(Mandatory=$false)]
    [String] $AutomationAccountArmId
)

$SiteRecoveryRunbookName = "Modify-AutoUpdateForVaultForPatner"
$TaskId = [guid]::NewGuid().ToString()
$SubscriptionId = "00000000-0000-0000-0000-000000000000"
$AsrApiVersion = "2018-01-10"
$RunAsConnectionName = "AzureRunAsConnection"
$ArmEndPoint = "https://management.azure.com"
$AadAuthority = "https://login.windows.net/"
$AadAudience = "https://management.core.windows.net/"
$AzureEnvironment = "AzureCloud"
$Timeout = "160"

function Throw-TerminatingErrorMessage
{
    Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
    )

    throw ("Message: {0}, TaskId: {1}.") -f $Message, $TaskId
}

function Write-Tracing
{
    Param
    (
        [Parameter(Mandatory=$true)]      
        [ValidateSet("Informational", "Warning", "ErrorLevel", "Succeeded", IgnoreCase = $true)]
        [String]
        $Level,

        [Parameter(Mandatory=$true)]
        [String]
        $Message,

        [Switch]
        $DisplayMessageToUser
    )

    Write-Output $Message

}

function Write-InformationTracing
{
    Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
    )

    Write-Tracing -Message $Message -Level Informational -DisplayMessageToUser
}

function ValidateInput()
{
    try
    {
        if(!$VaultResourceId.StartsWith("/subscriptions", [System.StringComparison]::OrdinalIgnoreCase))
        {
            $ErrorMessage = "The vault resource id should start with /subscriptions."
            throw $ErrorMessage
        }

        $Tokens = $VaultResourceId.SubString(1).Split("/")
        if(!($Tokens.Count % 2 -eq 0))
        {
            $ErrorMessage = ("Odd Number of tokens: {0}." -f $Tokens.Count)
            throw $ErrorMessage
        }

        if(!($Tokens.Count/2 -eq 4))
        {
            $ErrorMessage = ("Invalid number of resource in vault ARM id expected:4, actual:{0}." -f ($Tokens.Count/2))
            throw $ErrorMessage
        }

        if($AutoUpdateAction -ieq "Enabled" -and [string]::IsNullOrEmpty($AutomationAccountArmId))
        {
            $ErrorMessage = ("The automation account ARM id should not be null or empty when AutoUpdateAction is enabled.")
            throw $ErrorMessage
        }
    }
    catch
    {
        $ErrorMessage = ("ValidateInput failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

function Initialize-SubscriptionId()
{
    try
    {
        $Tokens = $VaultResourceId.SubString(1).Split("/")

        $Count = 0
        $ArmResources = @{}
        while($Count -lt $Tokens.Count)
        {
            $ArmResources[$Tokens[$Count]] = $Tokens[$Count+1]
            $Count = $Count + 2
        }
        
        return $ArmResources["subscriptions"]
    }
    catch
    {
        Write-Tracing -Level ErrorLevel -Message ("Initialize-SubscriptionId: failed with [Exception: {0}]." -f $_.Exception) -DisplayMessageToUser
        throw
    }
}

function Invoke-InternalRestMethod($Uri, $Headers, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-RestMethod -Uri $Uri -Headers $Headers    
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }

            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}

function Invoke-InternalWebRequest($Uri, $Headers, $Method, $Body, $ContentType, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-WebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -UseBasicParsing
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }

            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}

function Get-Header([ref]$Header, $AadAudience, $AadAuthority, $RunAsConnectionName){
    try 
    {
        $RunAsConnection = Get-AutomationConnection -Name $RunAsConnectionName
        $TenantId = $RunAsConnection.TenantId
        $ApplicationId = $RunAsConnection.ApplicationId
        $CertificateThumbprint = $RunAsConnection.CertificateThumbprint
        $Path = "cert:\CurrentUser\My\{0}" -f $CertificateThumbprint
        $Secret = Get-ChildItem -Path $Path
        $ClientCredential = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.ClientAssertionCertificate(
                $ApplicationId,
                $Secret)

        # Trim the forward slash from the AadAuthority if it exist.
        $AadAuthority = $AadAuthority.TrimEnd("/")
        $AuthContext = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext(
            "{0}/{1}" -f $AadAuthority, $TenantId )
        $AuthenticationResult = $authContext.AcquireToken($AadAudience, $Clientcredential)
        $Header.Value['Content-Type'] = 'application\json'
        $Header.Value['Authorization'] = $AuthenticationResult.CreateAuthorizationHeader()
        $Header.Value["x-ms-client-request-id"] = $TaskId + "/" + (New-Guid).ToString() + "-" + (Get-Date).ToString("u")
    }
    catch
    {
        $ErrorMessage = ("Get-BearerToken: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

function Get-ProtectionContainerToBeModified([ref] $ContainerMappingList)
{
    try 
    {
        Write-InformationTracing ("Get protection container mappings : {0}." -f $VaultResourceId)
        $ContainerMappingListUrl = $ArmEndPoint + $VaultResourceId + "/replicationProtectionContainerMappings" + "?api-version=" + $AsrApiVersion
        
        Write-InformationTracing ("Getting the bearer token and the header.")
        Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
        
        $Result = @()
        Invoke-InternalRestMethod -Uri $ContainerMappingListUrl -Headers $header -Result ([ref]$Result)
        $ContainerMappings = $Result[0]

        Write-InformationTracing ("Total retrieved container mappings: {0}." -f $ContainerMappings.Value.Count)
        foreach($Mapping in $ContainerMappings.Value)
        {
            if(($Mapping.properties.providerSpecificDetails -eq $null) -or ($Mapping.properties.providerSpecificDetails.instanceType -ine "A2A"))
            {
                Write-InformationTracing ("Mapping properties: {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the provider does not match." -f ($Mapping.Id))
                continue;
            }

            if($Mapping.Properties.State -ine "Paired")
            {
                Write-InformationTracing ("Ignoring container mapping: {0} as the state is not paired." -f ($Mapping.Id))
                continue;
            }

            Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties.providerSpecificDetails))
            $MappingAutoUpdateStatus = $Mapping.properties.providerSpecificDetails.agentAutoUpdateStatus
            $MappingAutomationAccountArmId = $Mapping.properties.providerSpecificDetails.automationAccountArmId
            $MappingHealthErrorCount = $Mapping.properties.HealthErrorDetails.Count

            if($AutoUpdateAction -ieq "Enabled" -and
                ($MappingAutoUpdateStatus -ieq "Enabled") -and
                ($MappingAutomationAccountArmId -ieq $AutomationAccountArmId) -and
                ($MappingHealthErrorCount -eq 0))
            {
                Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the auto update is already enabled and is healthy." -f ($Mapping.Id))
                continue;
            }

            ($ContainerMappingList.Value).Add($Mapping.id)
        }
    }
    catch
    {
        $ErrorMessage = ("Get-ProtectionContainerToBeModified: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

$OperationStartTime = Get-Date
$ContainerMappingList = New-Object System.Collections.Generic.List[System.String]
$JobsInProgressList = @()
$JobsCompletedSuccessList = @()
$JobsCompletedFailedList = @()
$JobsFailedToStart = 0
$JobsTimedOut = 0
$Header = @{}

$AzureRMProfile = Get-Module -ListAvailable -Name Az.Accounts | Select Name, Version, Path
$AzureRmProfileModulePath = Split-Path -Parent $AzureRMProfile.Path
Add-Type -Path (Join-Path $AzureRmProfileModulePath "Microsoft.IdentityModel.Clients.ActiveDirectory.dll")

$Inputs = ("Tracing inputs VaultResourceId: {0}, Timeout: {1}, AutoUpdateAction: {2}, AutomationAccountArmId: {3}." -f $VaultResourceId, $Timeout, $AutoUpdateAction, $AutomationAccountArmId)
Write-Tracing -Message $Inputs -Level Informational -DisplayMessageToUser
$CloudConfig = ("Tracing cloud configuration ArmEndPoint: {0}, AadAuthority: {1}, AadAudience: {2}." -f $ArmEndPoint, $AadAuthority, $AadAudience)
Write-Tracing -Message $CloudConfig -Level Informational -DisplayMessageToUser
$AutomationConfig = ("Tracing automation configuration RunAsConnectionName: {0}." -f $RunAsConnectionName)
Write-Tracing -Message $AutomationConfig -Level Informational -DisplayMessageToUser

ValidateInput
$SubscriptionId = Initialize-SubscriptionId
Get-ProtectionContainerToBeModified ([ref]$ContainerMappingList)

$Input = @{
  "properties"= @{
    "providerSpecificInput"= @{
        "instanceType" = "A2A"
        "agentAutoUpdateStatus" = $AutoUpdateAction
        "automationAccountArmId" = $AutomationAccountArmId
    }
  }
}
$InputJson = $Input |  ConvertTo-Json

if ($ContainerMappingList.Count -eq 0)
{
    Write-Tracing -Level Succeeded -Message ("Exiting as there are no container mappings to be modified.") -DisplayMessageToUser
    exit
}

Write-InformationTracing ("Container mappings to be updated has been retrieved with count: {0}." -f $ContainerMappingList.Count)

try
{
    Write-InformationTracing ("Start the modify container mapping jobs.")
    ForEach($Mapping in $ContainerMappingList)
    {
    try {
            $UpdateUrl = $ArmEndPoint + $Mapping + "?api-version=" + $AsrApiVersion
            Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
            
            $Result = @()
            Invoke-InternalWebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -Result ([ref]$Result)
            $Result = $Result[0]

            $JobAsyncUrl = $Result.Headers['Azure-AsyncOperation']
            Write-InformationTracing ("The modify container mapping job invoked with async url: {0}." -f $JobAsyncUrl)
            $JobsInProgressList += $JobAsyncUrl;

            # Rate controlling the set calls to maximum 60 calls per minute.
            # ASR throttling for set calls is 200 in 1 minute.
            Start-Sleep -Milliseconds 1000
        }
        catch{
            Write-InformationTracing ("The modify container mappings job creation failed for: {0}." -f $Ru)
            Write-InformationTracing $_
            $JobsFailedToStart++
        }
    }

    Write-InformationTracing ("Total modify container mappings has been initiated: {0}." -f $JobsInProgressList.Count)
}
catch
{
    $ErrorMessage = ("Modify container mapping jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

try
{
    while($JobsInProgressList.Count -ne 0)
    {
        Sleep -Seconds 30
        $JobsInProgressListInternal = @()
        ForEach($JobAsyncUrl in $JobsInProgressList)
        {
            try
            {
                Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
                $Result = Invoke-RestMethod -Uri $JobAsyncUrl -Headers $header
                $JobState = $Result.Status
                if($JobState -ieq "InProgress")
                {
                    $JobsInProgressListInternal += $JobAsyncUrl
                }
                elseif($JobState -ieq "Succeeded" -or `
                    $JobState -ieq "PartiallySucceeded" -or `
                    $JobState -ieq "CompletedWithInformation")
                {
                    Write-InformationTracing ("Jobs succeeded with state: {0}." -f $JobState)
                    $JobsCompletedSuccessList += $JobAsyncUrl
                }
                else
                {
                    Write-InformationTracing ("Jobs failed with state: {0}." -f $JobState)
                    $JobsCompletedFailedList += $JobAsyncUrl
                }
            }
            catch
            {
                Write-InformationTracing ("The get job failed with: {0}. Ignoring the exception and retrying the next job." -f $_.Exception)

                # The job on which the tracking failed, will be considered in progress and tried again later.
                $JobsInProgressListInternal += $JobAsyncUrl
            }

            # Rate controlling the get calls to maximum 120 calls each minute.
            # ASR throttling for get calls is 10000 in 60 minutes.
            Start-Sleep -Milliseconds 500
        }

        Write-InformationTracing ("Jobs remaining {0}." -f $JobsInProgressListInternal.Count)

        $CurrentTime = Get-Date
        if($CurrentTime -gt $OperationStartTime.AddMinutes($Timeout))
        {
            Write-InformationTracing ("Tracing modify cloud pairing jobs has timed out.")
            $JobsTimedOut = $JobsInProgressListInternal.Count
            $JobsInProgressListInternal = @()
        }

        $JobsInProgressList = $JobsInProgressListInternal
    }
}
catch
{
    $ErrorMessage = ("Tracking modify cloud pairing jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage  -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage 
}

Write-InformationTracing ("Tracking modify cloud pairing jobs completed.")
Write-InformationTracing ("Modify cloud pairing jobs success: {0}." -f $JobsCompletedSuccessList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed: {0}." -f $JobsCompletedFailedList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed to start: {0}." -f $JobsFailedToStart)
Write-InformationTracing ("Modify cloud pairing jobs timedout: {0}." -f $JobsTimedOut)

if($JobsTimedOut -gt  0)
{
    $ErrorMessage = "One or more modify cloud pairing jobs has timedout."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)   
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}
elseif($JobsCompletedSuccessList.Count -ne $ContainerMappingList.Count)
{
    $ErrorMessage = "One or more modify cloud pairing jobs failed."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

Write-Tracing -Level Succeeded -Message ("Modify cloud pairing completed.") -DisplayMessageToUser
```

### <a name="manage-updates-manually"></a>Ruční Správa aktualizací

1. Pokud jsou k dispozici nové aktualizace pro službou Mobility nainstalovanou na vašich virtuálních počítačů, se zobrazí následující oznámení: "Je k dispozici nová aktualizace agenta replikace Site recovery. Klikněte na tlačítko nainstalovat"

     ![Okno replikované položky](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)
2. Vyberte oznámení a otevřete stránku výběr VM.
3. Vyberte virtuální počítače, které chcete upgradovat a pak vyberte **OK**. Služba Mobility aktualizace spustí pro každý vybraný virtuální počítač.

     ![Replikované položky seznamu virtuálních počítačů](./media/vmware-azure-install-mobility-service/update-okpng.png)


## <a name="common-issues-and-troubleshooting"></a>Běžné problémy a řešení potíží

Pokud se vyskytl problém s automatickými aktualizacemi, zobrazí se oznámení o chybě v části **problémy s konfigurací** v řídicím panelu trezoru.

Pokud nelze povolit automatické aktualizace, přečtěte si následující běžné chyby a doporučené akce:

- **Chyba:** Nemáte oprávnění vytvořit účet Spustit jako pro Azure (instanční objekt) a udělit tomuto instančnímu objektu roli Přispěvatel.

   **Doporučená akce**: Ujistěte se, že účet přihlášení je přiřazen jako přispěvatele a zkuste to znovu. Přečtěte si část požadovaná oprávnění v [použití portálu k vytvoření aplikace a instančního objektu, který má přístup k prostředkům Azure AD](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) Další informace o přiřazování oprávnění.
 
   Chcete-li opravit většinu problémů, když povolíte automatické aktualizace, vyberte **opravit**. Pokud není k dispozici tlačítko opravit, zobrazuje se chybová zpráva zobrazí v podokně nastavení aktualizací rozšíření.

   ![Site Recovery service tlačítko Opravit v nastavení aktualizací rozšíření](./media/azure-to-azure-autoupdate/repair.png)

- **Chyba:** Účet Spustit jako nemá oprávnění pro přístup k prostředku služby recovery services.

    **Doporučená akce**: Odstranit a potom [znovu vytvořte účet Spustit jako](https://docs.microsoft.com/azure/automation/automation-create-runas-account). Nebo, ujistěte se, že aplikace Azure Active Directory účtu Automation spustit jako má přístup k prostředku služby recovery services.

- **Chyba:** Účet Spustit jako se nenašel. Jeden z nich byl odstraněn nebo není vytvořená: aplikace Azure Active Directory, instanční objekt, Role, prostředek certifikátu služby Automation, asset připojení Automation – nebo kryptografický otisk není mezi certifikátem a připojením identický. 

    **Doporučená akce**: Odstranit a potom [znovu vytvořte účet Spustit jako](https://docs.microsoft.com/azure/automation/automation-create-runas-account).

-  **Chyba:** Azure spustit jako certifikát používaný účet automation brzy vyprší platnost. 

    Certifikát podepsaný svým držitelem, který je vytvořený pro účet Spustit jako, vyprší jeden rok od data vytvoření. Před vypršením platnosti ho můžete kdykoli obnovit. Pokud jste si zaregistrovali službu e-mailových oznámení, také dostanete e-mailů při akce je zapotřebí ve směru z vaší strany. Tato chyba se zobrazí 2 měsíce před datem vypršení platnosti a se změní na kritickou chybu, pokud vypršela platnost certifikátu. Jakmile platnost certifikátu vypršela, automatické aktualizace nebude funkční, dokud si neprodloužíte stejné.

   **Doporučená akce**: Klikněte na "Opravit" a potom obnovit certifikát k vyřešení tohoto problému.
    
   ![obnovení certifikátu](media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG)

> [!NOTE]
> Po obnovení certifikátu, aktualizujte prosím stránku tak, aby se aktualizuje aktuální stav.
