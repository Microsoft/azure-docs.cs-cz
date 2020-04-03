---
title: Automatická aktualizace služby Mobility v Azure Site Recovery
description: Přehled automatické aktualizace služby Mobility při replikaci virtuálních počítačů Azure pomocí Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/02/2020
ms.author: rajanaki
ms.openlocfilehash: 67298ecf0c17feee2d36bb8774cae37b1ca81381
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618964"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Automatická aktualizace služby Mobility v replikaci Azure-azure

Azure Site Recovery používá měsíční vydání kadence opravit všechny problémy a vylepšit stávající funkce nebo přidat nové. Chcete-li zůstat aktuální se službou, musíte naplánovat nasazení opravy každý měsíc. Chcete-li se vyhnout režii spojené s každým upgradem, můžete povolit obnovení webu ke správě aktualizací součástí.

Jak je uvedeno v [architektuře zotavení po havárii Azure,](azure-to-azure-architecture.md)služba Mobility se nainstaluje na všech virtuálních počítačích Azure ,, které mají povolenou replikaci z jedné oblasti Azure do jiné. Při použití automatických aktualizací každá nová verze aktualizuje rozšíření služby Mobility.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Jak fungují automatické aktualizace

Když ke správě aktualizací použijete Site Recovery, nasadí globální runbook (používaný službami Azure) prostřednictvím účtu automatizace vytvořeného ve stejném předplatném jako trezor. Každý trezor používá jeden účet automatizace. Pro každý virtuální virtuální ms v trezoru, runbook zkontroluje aktivní automatické aktualizace. Pokud je k dispozici novější verze rozšíření služby Mobility, je aktualizace nainstalována.

Výchozí plán runbooku se vyskytuje denně v 12:00 v časovém pásmu geografie replikovaného virtuálního soudu. Můžete také změnit plán runbooku prostřednictvím účtu automatizace.

> [!NOTE]
> Počínaje [kumulativní aktualizací 35](site-recovery-whats-new.md#updates-march-2019)můžete zvolit existující účet automatizace, který chcete použít pro aktualizace. Před kumulativní aktualizací 35 site recovery ve výchozím nastavení vytvořil účet automatizace. Tuto možnost můžete vybrat jenom v případě, že povolíte replikaci pro virtuální ho. Není k dispozici pro virtuální hod, který už má povolenou replikaci. Nastavení, které vyberete, se vztahuje na všechny virtuální počítače Azure chráněné ve stejném trezoru.

Zapnutí automatických aktualizací nevyžaduje restartování virtuálních počítačích Azure ani neovlivní probíhající replikaci.

Fakturace úlohv účtu automatizace je založena na počtu minut za běhu úlohy použitých za měsíc. Spuštění úlohy trvá několik sekund na přibližně minutu každý den a je pokryta jako volné jednotky. Ve výchozím nastavení jsou 500 minut zahrnuty jako volné jednotky pro účet automatizace, jak je znázorněno v následující tabulce:

| Volné jednotky v ceně (každý měsíc) | Price |
|---|---|
| Doba běhu úlohy 500 minut | -0.14/min

## <a name="enable-automatic-updates"></a>Povolení automatických aktualizací

Aktualizace rozšíření můžete spravovat několika způsoby:

- [Spravovat jako součást kroku povolit replikaci](#manage-as-part-of-the-enable-replication-step)
- [Přepnutí nastavení aktualizace rozšíření uvnitř úložiště](#toggle-the-extension-update-settings-inside-the-vault)
- [Ruční správa aktualizací](#manage-updates-manually)

### <a name="manage-as-part-of-the-enable-replication-step"></a>Spravovat jako součást kroku povolit replikaci

Když povolíte replikaci pro virtuální hospodařící systém buď [počínaje zobrazením virtuálního zařízení,](azure-to-azure-quickstart.md) nebo [z trezoru služeb pro obnovení](azure-to-azure-how-to-enable-replication.md), můžete buď povolit obnovení webu ke správě aktualizací rozšíření Site Recovery nebo jeho ruční správě.

:::image type="content" source="./media/azure-to-azure-autoupdate/enable-rep.png" alt-text="Nastavení rozšíření":::

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Přepnutí nastavení aktualizace rozšíření uvnitř úložiště

1. V trezoru služby Recovery Services přejděte na **spravovat** > **infrastrukturu obnovení webu**.
1. V části Pro**nastavení** > aktualizace rozšíření **virtuálních počítačů** > Azure**povolte správu obnovení webu**vyberte **Zapnuto**.

   Chcete-li rozšíření spravovat ručně, vyberte **možnost Vypnout**.

1. Vyberte **Uložit**.

:::image type="content" source="./media/azure-to-azure-autoupdate/vault-toggle.png" alt-text="Nastavení aktualizace rozšíření":::

> [!IMPORTANT]
> Když zvolíte **Povolit obnovení webu ke správě**, nastavení se použije na všechny virtuální aplikace v úschovně.

> [!NOTE]
> Obě možnosti vás upozorní na účet automatizace používaný pro správu aktualizací. Pokud tuto funkci používáte v trezoru poprvé, ve výchozím nastavení se vytvoří nový účet automatizace. Můžete také přizpůsobit nastavení a zvolit existující účet automatizace. Všechny následné taks povolit replikaci ve stejném trezoru bude používat dříve vytvořený účet automatizace. V nabídce v současné době bude uveden pouze seznam účtů automatizace, které jsou ve stejné skupině prostředků jako trezor.

> [!IMPORTANT]
> Následující skript je třeba spustit v kontextu účtu automatizace.
Pro vlastní účet automatizace použijte následující skript:

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

1. Pokud jsou na virtuálních počítačích nainstalovány nové aktualizace pro službu Mobility, zobrazí se následující oznámení: **Je k dispozici aktualizace agenta replikace obnovení nové lokality. Klepnutím nainstalujete.**

   :::image type="content" source="./media/vmware-azure-install-mobility-service/replicated-item-notif.png" alt-text="Okno Replikované položky":::

1. Vyberte oznámení, chcete-li otevřít stránku výběru virtuálního zařízení.
1. Vyberte virtuální chod, které chcete upgradovat, a pak vyberte **OK**. Služba Aktualizovat mobilitu se spustí pro každý vybraný virtuální virtuální ms.

   :::image type="content" source="./media/vmware-azure-install-mobility-service/update-okpng.png" alt-text="Seznam replikovaných položek virtuálních věcí":::

## <a name="common-issues-and-troubleshooting"></a>Běžné problémy a řešení potíží

Pokud dojde k problému s automatickými aktualizacemi, zobrazí se v části Problémy s **konfigurací** v řídicím panelu trezoru oznámení o chybě.

Pokud nemůžete povolit automatické aktualizace, podívejte se na následující běžné chyby a doporučené akce:

- **Chyba**: Nemáte oprávnění k vytvoření účtu Azure Run As (instanční objekt) a udělit roli přispěvatele instančního objektu.

  **Doporučená akce**: Ujistěte se, že je přihlášený účet přiřazen jako přispěvatel, a akci opakujte. Další informace o přiřazování oprávnění naleznete v části Požadovaná oprávnění v části [Postup: Pomocí portálu vytvořte aplikaci azure a instancí, která má přístup k prostředkům](/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions).

  Chcete-li vyřešit většinu problémů po povolení automatických aktualizací, vyberte **možnost Opravit**. Pokud tlačítko opravy není k dispozici, přečtěte si chybovou zprávu zobrazenou v podokně nastavení aktualizace rozšíření.

  :::image type="content" source="./media/azure-to-azure-autoupdate/repair.png" alt-text="Tlačítko Opravy služby Obnovení sítě v nastavení aktualizace rozšíření":::

- **Chyba**: Účet Spustit jako nemá oprávnění k přístupu k prostředku služeb pro obnovení.

  **Doporučená akce**: Odstraňte a [znovu vytvořte účet Spustit jako](/azure/automation/automation-create-runas-account). Nebo se ujistěte, že automatizace spustit jako účet Azure Active Directory aplikace přístup k prostředku služby pro obnovení.

- **Chyba**: Spustit jako účet nebyl nalezen. Jeden z nich byl odstraněn nebo nebyl vytvořen – aplikace Azure Active Directory, Instanční objekt, Role, Prostředek certifikátu automatizace, Prostředek připojení automatizace – nebo kryptografický otisk není shodný mezi certifikátem a připojením.

  **Doporučená akce**: Odstraňte a [znovu vytvořte účet Spustit jako](/azure/automation/automation-create-runas-account).

- **Chyba**: Brzy vyprší platnost certifikátu Azure Run as, který používá účet automatizace.

  Platnost certifikátu podepsaného svým držitelem, který je vytvořen pro účet Spustit jako, vyprší jeden rok od data vytvoření. Před vypršením platnosti ho můžete kdykoli obnovit. Pokud jste se zaregistrovali k odběru e-mailových oznámení, budete také dostávat e-maily, když je vyžadována akce z vaší strany. Tato chyba se zobrazí dva měsíce před datem vypršení platnosti a změní se na kritickou chybu, pokud platnost certifikátu vypršela. Po vypršení platnosti certifikátu nebude automatická aktualizace funkční, dokud ji neobnovíte.

  **Doporučená akce**: Chcete-li tento problém vyřešit, vyberte **možnost Opravit** a **obnovit certifikát**.

  :::image type="content" source="./media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG" alt-text="obnovit certifikát":::

  > [!NOTE]
  > Po obnovení certifikátu aktualizujte stránku a zobrazte aktuální stav.
