---
title: Automatická aktualizace služby mobility v Azure Site Recovery
description: Přehled automatické aktualizace služby mobility při replikaci virtuálních počítačů Azure pomocí Azure Site Recovery.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/02/2020
ms.author: sideeksh
ms.openlocfilehash: 53c5dc4920b6c50ee3c900db9626f4d283f7b846
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89426414"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Automatické aktualizace služby mobility v Azure do Azure – replikace

Azure Site Recovery používá tempo měsíčního vydání k řešení problémů a vylepšení existujících funkcí nebo přidávání nových. Chcete-li zůstat ve službě aktuální, je nutné naplánovat nasazení opravy každý měsíc. Abyste se vyhnuli režie spojené s jednotlivými upgrademi, můžete Site Recovery spravovat aktualizace součástí.

Jak je uvedeno v části [Architektura zotavení po havárii z Azure do Azure](azure-to-azure-architecture.md), Služba mobility je nainstalovaná na všech virtuálních počítačích Azure, které mají povolenou replikaci z jedné oblasti Azure do jiné. Pokud používáte automatické aktualizace, každé nové vydání aktualizuje rozšíření služby mobility.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Jak fungují automatické aktualizace

Když ke správě aktualizací použijete Site Recovery, nasadí globální Runbook (používaný službami Azure) prostřednictvím účtu Automation, který se vytvoří ve stejném předplatném jako trezor. Každý trezor používá jeden účet Automation. U každého virtuálního počítače v trezoru vyhledává sada Runbook aktivní automatické aktualizace. Pokud je k dispozici novější verze rozšíření služby mobility, nainstalují se aktualizace.

Výchozí plán Runbooku probíhá denně v 12:00 v časovém pásmu geograficky replikovaného virtuálního počítače. Můžete také změnit plán Runbooku prostřednictvím účtu Automation.

> [!NOTE]
> Počínaje [kumulativní aktualizací 35](site-recovery-whats-new.md#updates-march-2019)můžete zvolit existující účet Automation, který se bude používat pro aktualizace. Před kumulativní aktualizací 35 Site Recovery ve výchozím nastavení vytvořil účet Automation. Tuto možnost můžete vybrat jenom v případě, že pro virtuální počítač povolíte replikaci. Není k dispozici pro virtuální počítač, který už má povolenou replikaci. Nastavení, které vyberete, se vztahuje na všechny virtuální počítače Azure chráněné ve stejném trezoru.

Zapnutí automatických aktualizací nevyžaduje restartování virtuálních počítačů Azure nebo vlivu na probíhající replikaci.

Fakturace úlohy v účtu Automation vychází z počtu minut běhu úloh v měsíci. Provádění úlohy trvá několik sekund od každou minutu každý den a je zahrnuto jako volné jednotky. Ve výchozím nastavení jsou 500 minut zahrnuty jako volné jednotky pro účet Automation, jak je znázorněno v následující tabulce:

| Zahrnuté volné jednotky (každý měsíc) | Cena |
|---|---|
| Běhové prostředí úlohy 500 minut | ₹ 0.14 za minutu

## <a name="enable-automatic-updates"></a>Povolit automatické aktualizace

Existuje několik způsobů, jak Site Recovery mohou spravovat aktualizace rozšíření:

- [Spravovat jako součást kroku Povolit replikaci](#manage-as-part-of-the-enable-replication-step)
- [Přepínání nastavení aktualizace rozšíření v rámci trezoru](#toggle-the-extension-update-settings-inside-the-vault)
- [Ruční správa aktualizací](#manage-updates-manually)

### <a name="manage-as-part-of-the-enable-replication-step"></a>Spravovat jako součást kroku Povolit replikaci

Pokud povolíte replikaci pro virtuální počítač buď [od zobrazení virtuálního počítače](azure-to-azure-quickstart.md) , nebo [z trezoru služby Recovery Services](azure-to-azure-how-to-enable-replication.md), můžete povolit, aby Site Recovery spravovat aktualizace rozšíření Site Recovery, nebo ho spravovat ručně.

:::image type="content" source="./media/azure-to-azure-autoupdate/enable-rep.png" alt-text="Nastavení rozšíření":::

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Přepínání nastavení aktualizace rozšíření v rámci trezoru

1. V trezoru Recovery Services klikněte na **Správa**  >  **Site Recovery infrastruktura**.
1. V **For Azure Virtual Machines**části  >  **Nastavení aktualizace rozšíření**Azure Virtual Machines  >  **umožňuje Site Recovery spravovat**, vyberte **zapnuto**.

   Pokud chcete rozšíření spravovat ručně, vyberte **vypnuto**.

1. Vyberte **Uložit**.

:::image type="content" source="./media/azure-to-azure-autoupdate/vault-toggle.png" alt-text="Nastavení aktualizace rozšíření":::

> [!IMPORTANT]
> Když vyberete možnost **povoluje Site Recovery správě**, nastavení se použije na všechny virtuální počítače v trezoru.

> [!NOTE]
> Obě možnosti vás upozorní na účet Automation, který se používá ke správě aktualizací. Pokud tuto funkci používáte v trezoru poprvé, vytvoří se ve výchozím nastavení nový účet Automation. Alternativně můžete upravit nastavení a zvolit existující účet Automation. Všechny následné akce, které trvalo replikace ve stejném trezoru, budou používat dřív vytvořený účet Automation. V současné době rozevírací nabídka bude zobrazovat jenom účty Automation, které jsou ve stejné skupině prostředků jako trezor.

> [!IMPORTANT]
> Následující skript musí běžet v kontextu účtu Automation.
Pro vlastní účet Automation použijte tento skript:

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

1. Pokud jsou na virtuálních počítačích nainstalované nové aktualizace služby mobility, zobrazí se následující oznámení: **k dispozici je nová aktualizace agenta replikace Site Recovery. Kliknutím nainstalujete.**

   :::image type="content" source="./media/vmware-azure-install-mobility-service/replicated-item-notif.png" alt-text="Okno replikované položky":::

1. Vyberte oznámení a otevřete stránku výběr virtuálního počítače.
1. Vyberte virtuální počítače, které chcete upgradovat, a pak vyberte **OK**. Služba mobility Update Service se spustí pro každý vybraný virtuální počítač.

   :::image type="content" source="./media/vmware-azure-install-mobility-service/update-okpng.png" alt-text="Seznam virtuálních počítačů replikovaných položek":::

## <a name="common-issues-and-troubleshooting"></a>Běžné problémy a řešení potíží

Pokud dojde k potížím s automatickými aktualizacemi, zobrazí se v části **problémy s konfigurací** na řídicím panelu trezoru chybové oznámení.

Pokud nemůžete povolit automatické aktualizace, přečtěte si následující běžné chyby a doporučené akce:

- **Chyba**: nemáte oprávnění k vytvoření účtu spustit v Azure jako (instančního objektu) a udělení role přispěvateli objektu služby.

  **Doporučená akce**: Přesvědčte se, zda je přihlášený účet přiřazen jako Přispěvatel, a akci opakujte. Další informace o přiřazování oprávnění naleznete v části požadovaná oprávnění v tématu [Postupy: použití portálu k vytvoření aplikace a instančního objektu služby Azure AD, který má přístup k prostředkům](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

  Chcete-li opravit většinu problémů po povolení automatických aktualizací, vyberte možnost **opravit**. Pokud není tlačítko opravit k dispozici, přečtěte si chybovou zprávu zobrazenou v podokně nastavení aktualizace rozšíření.

  :::image type="content" source="./media/azure-to-azure-autoupdate/repair.png" alt-text="Tlačítko pro opravu služby Site Recovery v nastavení aktualizace rozšíření":::

- **Chyba**: účet Spustit jako nemá oprávnění pro přístup k prostředku služby Recovery Services.

  **Doporučená akce**: odstraňte a [znovu vytvořte účet Spustit jako](../automation/manage-runas-account.md). Nebo se ujistěte, že aplikace Azure Active Directory účtu Spustit jako pro automatizaci má přístup k prostředku Recovery Services.

- **Chyba**: účet Spustit jako nebyl nalezen. Jedna z těchto položek byla odstraněna nebo nebyla vytvořena-Azure Active Directory aplikace, instanční objekt, role, Asset certifikátu Automation, Asset připojení Automation – nebo kryptografický otisk není totožný mezi certifikátem a připojením.

  **Doporučená akce**: odstraňte a [znovu vytvořte účet Spustit jako](../automation/manage-runas-account.md).

- **Chyba**: platnost certifikátu spustit jako pro Azure, který používá účet Automation, brzy vyprší.

  Certifikát podepsaný svým držitelem, který se vytvoří pro účet Spustit jako, vyprší jeden rok od data vytvoření. Před vypršením platnosti ho můžete kdykoli obnovit. Pokud jste se zaregistrovali k e-mailovým oznámením, obdržíte také e-maily, když se z vaší strany vyžaduje akce. Tato chyba se zobrazí po dvou měsících před datem vypršení platnosti a v případě vypršení platnosti certifikátu se změní na kritickou chybu. Po vypršení platnosti certifikátu nebudou Automatické aktualizace funkční, dokud neobnovíte stejné.

  **Doporučená akce**: Chcete-li tento problém vyřešit, vyberte možnost **opravit** a pak **Obnovte certifikát**.

  :::image type="content" source="./media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG" alt-text="obnovit – certifikát":::

  > [!NOTE]
  > Po obnovení certifikátu aktualizujte stránku, aby se zobrazil aktuální stav.
