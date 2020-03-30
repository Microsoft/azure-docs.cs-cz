---
title: Automatická aktualizace služby Mobility v Azure Site Recovery
description: Přehled automatické aktualizace služby Mobility při replikaci virtuálních počítačů Azure pomocí Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/24/2019
ms.author: rajanaki
ms.openlocfilehash: 3a9b0717368fa67f5a7dd477e018a68e048b6740
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451410"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Automatická aktualizace služby Mobility v replikaci Azure-azure

Azure Site Recovery používá měsíční vydání kadence opravit všechny problémy a vylepšit stávající funkce nebo přidat nové. Chcete-li zůstat aktuální se službou, musíte naplánovat nasazení opravy každý měsíc. Chcete-li se vyhnout režii spojené s každým upgradem, můžete místo toho povolit obnovení webu ke správě aktualizací součástí.

Jak je uvedeno v [architektuře zotavení po havárii Azure,](azure-to-azure-architecture.md)služba mobility se instaluje na všech virtuálních počítačích Azure ,pro které je povolená replikace replikace virtuálních počítačů z jedné oblasti Azure do jiné. Při použití automatických aktualizací každá nová verze aktualizuje rozšíření služby Mobility.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Jak fungují automatické aktualizace

Když ke správě aktualizací použijete Site Recovery, nasadí globální runbook (používaný službami Azure) prostřednictvím účtu automatizace vytvořeného ve stejném předplatném jako trezor. Každý trezor používá jeden účet automatizace. Runbook zkontroluje pro každý virtuální virtuální ms v trezoru aktivní automatické aktualizace a upgraduje rozšíření služby Mobility, pokud je k dispozici novější verze.

Výchozí plán runbooku se opakuje denně ve 12:00 v časovém pásmu geografické hodnoty replikovaného virtuálního soudu. Můžete také změnit plán runbooku prostřednictvím účtu automatizace.

> [!NOTE]
> Počínaje kumulativní aktualizací 35 můžete zvolit existující účet automatizace, který chcete použít pro aktualizace. Před touto aktualizací site recovery vytvořil tento účet ve výchozím nastavení. Všimněte si, že tuto možnost můžete vybrat jenom při povolení replikace pro virtuální ho. Není k dispozici pro replikaci virtuálního počítače. Nastavení, které vyberete, se bude vztahovat na všechny virtuální počítače Azure chráněné ve stejném trezoru.
 
> Zapnutí automatických aktualizací nevyžaduje restartování virtuálních počítačích Azure ani neovlivní probíhající replikaci.

> Fakturace úlohv účtu automatizace je založena na počtu minut za běhu úlohy použitých za měsíc. Ve výchozím nastavení jsou 500 minut zahrnuty jako volné jednotky pro účet automatizace. Spuštění úlohy trvá několik sekund na přibližně minutu každý den a je pokryta jako volné jednotky.

| Volné jednotky v ceně (každý měsíc) | Price |
|---|---|
| Doba běhu úlohy 500 minut | -0.14/min

## <a name="enable-automatic-updates"></a>Povolení automatických aktualizací

Obnovení webu můžete povolit ke správě aktualizací následujícími způsoby.

### <a name="manage-as-part-of-the-enable-replication-step"></a>Spravovat jako součást kroku povolit replikaci

Když povolíte replikaci pro virtuální hospodařící systém buď [počínaje zobrazením virtuálního zařízení,](azure-to-azure-quickstart.md) nebo [z trezoru služeb pro obnovení](azure-to-azure-how-to-enable-replication.md), můžete buď povolit obnovení webu ke správě aktualizací rozšíření Site Recovery nebo jeho ruční správě.

![Nastavení rozšíření](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Přepnutí nastavení aktualizace rozšíření uvnitř úložiště

1. Uvnitř trezoru přejděte na **Spravovat** > **infrastrukturu pro obnovení webu**.
2. V části Pro**nastavení aktualizace rozšíření** **virtuálních počítačů** > Azure zapněte přepínač **Povolit obnovení webu.** Chcete-li ji spravovat ručně, vypněte ji. 
3. Vyberte **Uložit**.

![Nastavení aktualizace rozšíření](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important]
> Když zvolíte **Povolit obnovení webu ke správě**, nastavení se použije na všechny virtuální aplikace v odpovídajícím trezoru.


> [!Note]
> Obě možnosti vás upozorní na účet automatizace používaný pro správu aktualizací. Pokud tuto funkci používáte v trezoru poprvé, ve výchozím nastavení se vytvoří nový účet automatizace. Můžete také přizpůsobit nastavení a zvolit existující účet automatizace. Všechny následné povolit replikace ve stejném trezoru použít dříve vytvořené jeden. V seznamu aktuálně budou uvedeny pouze účty automatizace, které jsou ve stejné skupině prostředků jako trezor.  

> [!IMPORTANT]
> Níže uvedený skript je třeba spustit v kontextu účtu automatizace Pro vlastní účet automatizace použijte následující skript:

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

$AzureRMProfile = Get-Module -ListAvailable -Name AzureRM.Profile | Select Name, Version, Path
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

1. Pokud jsou na virtuálních počítačích nainstalovány nové aktualizace pro službu Mobility, zobrazí se následující oznámení: "Je k dispozici aktualizace agenta replikace obnovení nové lokality. Klikněte pro instalaci"

     ![Okno Replikované položky](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)
2. Vyberte oznámení, chcete-li otevřít stránku výběru virtuálního zařízení.
3. Vyberte virtuální chod, které chcete upgradovat, a pak vyberte **OK**. Služba Aktualizovat mobilitu se spustí pro každý vybraný virtuální virtuální ms.

     ![Seznam replikovaných položek virtuálních věcí](./media/vmware-azure-install-mobility-service/update-okpng.png)


## <a name="common-issues-and-troubleshooting"></a>Běžné problémy a řešení potíží

Pokud dojde k problému s automatickými aktualizacemi, zobrazí se v části Problémy s **konfigurací** v řídicím panelu trezoru oznámení o chybě.

Pokud se vám nepodařilo povolit automatické aktualizace, podívejte se na následující běžné chyby a doporučené akce:

- **Chyba**: Nemáte oprávnění k vytvoření účtu Azure Run As (instanční objekt) a udělit roli přispěvatele instančního objektu.

   **Doporučená akce**: Ujistěte se, že je přihlášený účet přiřazen jako přispěvatel, a akci opakujte. Naleznete v části požadovaná oprávnění v [článku Pomocí portálu vytvořte aplikaci a instanční objekt Azure AD, který má přístup k prostředkům, kde](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) najdete další informace o přiřazování oprávnění.
 
   Chcete-li vyřešit většinu problémů po povolení automatických aktualizací, vyberte **možnost Opravit**. Pokud tlačítko opravy není k dispozici, přečtěte si chybovou zprávu zobrazenou v podokně nastavení aktualizace rozšíření.

   ![Tlačítko Opravy služby Obnovení sítě v nastavení aktualizace rozšíření](./media/azure-to-azure-autoupdate/repair.png)

- **Chyba**: Účet Spustit jako nemá oprávnění k přístupu k prostředku služeb pro obnovení.

    **Doporučená akce**: Odstraňte a [znovu vytvořte účet Spustit jako](https://docs.microsoft.com/azure/automation/automation-create-runas-account). Nebo se ujistěte, že automatizace spustit jako účet Azure Active Directory aplikace má přístup k prostředku služby pro obnovení.

- **Chyba**: Spustit jako účet nebyl nalezen. Jeden z nich byl odstraněn nebo nebyl vytvořen – aplikace Azure Active Directory, Instanční objekt, Role, Prostředek certifikátu automatizace, Prostředek připojení automatizace – nebo kryptografický otisk není shodný mezi certifikátem a připojením. 

    **Doporučená akce**: Odstraňte a [znovu vytvořte účet Spustit jako](https://docs.microsoft.com/azure/automation/automation-create-runas-account).

-  **Chyba**: Brzy vyprší platnost certifikátu Azure Run as, který používá účet automatizace. 

    Platnost certifikátu podepsaného svým držitelem, který je vytvořen pro účet Spustit jako, vyprší jeden rok od data vytvoření. Před vypršením platnosti ho můžete kdykoli obnovit. Pokud jste se zaregistrovali k odběru e-mailových oznámení, budete také dostávat e-maily, když je vyžadována akce z vaší strany. Tato chyba se zobrazí dva měsíce před datem vypršení platnosti a změní se na kritickou chybu, pokud platnost certifikátu vypršela. Po vypršení platnosti certifikátu nebude automatická aktualizace funkční, dokud ji neobnovíte.

   **Doporučená akce**: Kliknutím na tlačítko Oprava a potom na tlačítko Obnovit certifikát tento problém vyřešíte.
    
   ![obnovit certifikát](media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG)

> [!NOTE]
> Po obnovení certifikátu aktualizujte stránku tak, aby byl aktualizován aktuální stav.
