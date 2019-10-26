---
title: Automatické aktualizace služby mobility v Azure do Azure – zotavení po havárii | Microsoft Docs
description: Přehled automatické aktualizace služby mobility při replikaci virtuálních počítačů Azure pomocí Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/24/2019
ms.author: rajanaki
ms.openlocfilehash: 79a11a58f11486f3eda0205e62e7a4a92ff070b2
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933938"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Automatické aktualizace služby mobility v Azure do Azure – replikace

Azure Site Recovery používá tempo měsíčního vydání k řešení problémů a vylepšení existujících funkcí nebo přidávání nových. Chcete-li zůstat ve službě aktuální, je nutné naplánovat nasazení opravy každý měsíc. Abyste se vyhnuli režii souvisejícím s každým upgradem, můžete místo toho Site Recovery spravovat aktualizace součástí.

Jak je uvedeno v části [Architektura zotavení po havárii z Azure do Azure](azure-to-azure-architecture.md), Služba mobility je nainstalovaná na všech virtuálních počítačích Azure, pro které je replikace povolená, a zároveň replikuje virtuální počítače z jedné oblasti Azure do jiné. Pokud používáte automatické aktualizace, každé nové vydání aktualizuje rozšíření služby mobility.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Jak fungují automatické aktualizace

Když ke správě aktualizací použijete Site Recovery, nasadí globální Runbook (používaný službami Azure) prostřednictvím účtu Automation, který se vytvoří ve stejném předplatném jako trezor. Každý trezor používá jeden účet Automation. Runbook vyhledá u každého virtuálního počítače v trezoru aktivní automatické aktualizace a upgraduje rozšíření služby mobility, pokud je k dispozici novější verze.

Výchozí plán Runbooku se opakuje každý den v 12:00 v časovém pásmu geograficky replikovaného virtuálního počítače. Můžete také změnit plán Runbooku prostřednictvím účtu Automation.

> [!NOTE]
> Počínaje kumulativní aktualizací 35 můžete zvolit existující účet Automation, který se bude používat pro aktualizace. Před touto aktualizací Site Recovery tento účet ve výchozím nastavení vytvořil. Tato možnost je dostupná, když povolíte replikaci pro virtuální počítač. Pokud toto nastavení změníte, bude platit pro všechny virtuální počítače Azure chráněné ve stejném trezoru.
 
> Zapnutí automatických aktualizací nevyžaduje restartování virtuálních počítačů Azure nebo vlivu na probíhající replikaci.

> Fakturace úlohy v účtu Automation vychází z počtu minut běhu úloh v měsíci. Ve výchozím nastavení jsou 500 minut zahrnuty jako volné jednotky pro účet Automation. Provádění úlohy trvá několik sekund od každou minutu každý den a je zahrnuto jako volné jednotky.

| Zahrnuté volné jednotky (každý měsíc) | Cena |
|---|---|
| Běhové prostředí úlohy 500 minut | ₹ 0.14 za minutu

## <a name="enable-automatic-updates"></a>Povolit automatické aktualizace

Můžete Site Recovery spravovat aktualizace následujícími způsoby.

### <a name="manage-as-part-of-the-enable-replication-step"></a>Spravovat jako součást kroku Povolit replikaci

Pokud povolíte replikaci pro virtuální počítač buď [od zobrazení virtuálního počítače](azure-to-azure-quickstart.md) , nebo [z trezoru služby Recovery Services](azure-to-azure-how-to-enable-replication.md), můžete povolit, aby Site Recovery spravovat aktualizace rozšíření Site Recovery, nebo ho spravovat ručně.

![Nastavení rozšíření](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Přepínání nastavení aktualizace rozšíření v rámci trezoru

1. V trezoru můžete přejít na **správu** > **Site Recovery infrastruktury**.
2. V části pro **Nastavení aktualizace rozšíření** **služby Azure Virtual Machines** > zapněte přepínač **Povolit Site Recovery pro správu** . Pokud ho chcete spravovat ručně, vypněte ho. 
3. Vyberte **Save** (Uložit).

![Nastavení aktualizace rozšíření](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important]
> Když vyberete možnost **povoluje Site Recovery správě**, nastavení se použije na všechny virtuální počítače v odpovídajícím trezoru.


> [!Note]
> Obě možnosti vás upozorní na účet Automation, který se používá ke správě aktualizací. Pokud tuto funkci používáte v trezoru poprvé, vytvoří se ve výchozím nastavení nový účet Automation. Alternativně můžete upravit nastavení a zvolit existující účet Automation. Všechna další povolení replikace ve stejném trezoru používají dříve vytvořenou replikaci. V současné době rozevírací seznam bude zobrazovat jenom účty Automation, které jsou ve stejné skupině prostředků jako trezor.  

> [!IMPORTANT]
> Níže uvedený skript musí být spuštěný v kontextu účtu Automation pro vlastní účet Automation, použijte tento skript:

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

### <a name="manage-updates-manually"></a>Ruční správa aktualizací

1. Pokud jsou na virtuálních počítačích nainstalované nové aktualizace služby mobility, zobrazí se toto oznámení: "nová aktualizace agenta replikace Site Recovery je k dispozici. Kliknutím nainstalujete

     ![Okno replikované položky](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)
2. Vyberte oznámení a otevřete stránku výběr virtuálního počítače.
3. Vyberte virtuální počítače, které chcete upgradovat, a pak vyberte **OK**. Služba mobility Update Service se spustí pro každý vybraný virtuální počítač.

     ![Seznam virtuálních počítačů replikovaných položek](./media/vmware-azure-install-mobility-service/update-okpng.png)


## <a name="common-issues-and-troubleshooting"></a>Běžné problémy a řešení potíží

Pokud dojde k potížím s automatickými aktualizacemi, zobrazí se v části **problémy s konfigurací** na řídicím panelu trezoru chybové oznámení.

Pokud nemůžete povolit automatické aktualizace, přečtěte si následující běžné chyby a doporučené akce:

- **Chyba**: nemáte oprávnění k vytvoření účtu spustit v Azure jako (instančního objektu) a udělení role přispěvateli objektu služby.

   **Doporučená akce**: Přesvědčte se, zda je přihlášený účet přiřazen jako Přispěvatel, a akci opakujte. Pokud chcete získat další informace o přiřazování oprávnění, přečtěte si část požadovaná oprávnění v tématu [použití portálu k vytvoření aplikace služby Azure AD a instančního objektu, který má přístup k prostředkům](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) .
 
   Chcete-li opravit většinu problémů po povolení automatických aktualizací, vyberte možnost **opravit**. Pokud není tlačítko opravit k dispozici, přečtěte si chybovou zprávu zobrazenou v podokně nastavení aktualizace rozšíření.

   ![Tlačítko pro opravu služby Site Recovery v nastavení aktualizace rozšíření](./media/azure-to-azure-autoupdate/repair.png)

- **Chyba**: účet Spustit jako nemá oprávnění pro přístup k prostředku služby Recovery Services.

    **Doporučená akce**: odstraňte a [znovu vytvořte účet Spustit jako](https://docs.microsoft.com/azure/automation/automation-create-runas-account). Nebo se ujistěte, že aplikace Azure Active Directory účtu Spustit jako pro automatizaci má přístup k prostředku Recovery Services.

- **Chyba**: účet Spustit jako nebyl nalezen. Jedna z těchto položek byla odstraněna nebo nebyla vytvořena-Azure Active Directory aplikace, instanční objekt, role, Asset certifikátu Automation, Asset připojení Automation – nebo kryptografický otisk není totožný mezi certifikátem a připojením. 

    **Doporučená akce**: odstraňte a [znovu vytvořte účet Spustit jako](https://docs.microsoft.com/azure/automation/automation-create-runas-account).

-  **Chyba**: platnost certifikátu spustit jako pro Azure, který používá účet Automation, brzy vyprší. 

    Certifikát podepsaný svým držitelem, který se vytvoří pro účet Spustit jako, vyprší jeden rok od data vytvoření. Před vypršením platnosti ho můžete kdykoli obnovit. Pokud jste se zaregistrovali k e-mailovým oznámením, obdržíte také e-maily, když se z vaší strany vyžaduje akce. Tato chyba se zobrazí po dvou měsících před datem vypršení platnosti a v případě vypršení platnosti certifikátu se změní na kritickou chybu. Po vypršení platnosti certifikátu nebudou Automatické aktualizace funkční, dokud neobnovíte stejné.

   **Doporučená akce**: Pokud chcete tento problém vyřešit, klikněte na opravit a pak na prodloužit certifikát.
    
   ![obnovit – certifikát](media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG)

> [!NOTE]
> Po obnovení certifikátu aktualizujte stránku, aby se aktuální stav aktualizoval.
