---
title: Škálování hostitelů relací Azure Automation virtuální plochy Windows (Classic) – Azure
description: Jak automaticky škálovat hostitele relací virtuálních počítačů s Windows (Classic) pomocí Azure Automation.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fd14af6c95654708f339f4a68cd333d0e3162553
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89078176"
---
# <a name="scale-windows-virtual-desktop-classic-session-hosts-using-azure-automation"></a>Škálování hostitelů relací virtuálních počítačů s Windows (Classic) pomocí Azure Automation

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows (Classic), která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows.

Celkové náklady na nasazení virtuálních klientů s Windows můžete snížit tak, že změníte velikost virtuálních počítačů (VM). To znamená vypnutí a zrušení přidělení virtuálních počítačů hostitele relace v době mimo špičku a jejich opětovné zapnutí a přerozdělení během špičky.

V tomto článku se dozvíte o nástroji pro škálování vytvořeném pomocí účtu Azure Automation a aplikaci Azure Logic Apps, která automaticky škáluje virtuální počítače hostitele relací ve vašem prostředí virtuálních počítačů s Windows. Pokud se chcete dozvědět, jak používat nástroj pro škálování, přeskočte dopředu s [požadavky](#prerequisites).

## <a name="how-the-scaling-tool-works"></a>Jak nástroj pro škálování funguje

Nástroj pro škálování nabízí možnost automatizace s nízkými náklady pro zákazníky, kteří chtějí optimalizovat náklady na virtuální počítače hostitele relace.

Nástroj pro škálování můžete použít k těmto akcím:

- Naplánování spouštění a zastavování virtuálních počítačů na základě špičky a Off-Peak pracovní doby.
- Horizontální navýšení kapacity virtuálních počítačů na základě počtu relací na jádro procesoru.
- Škálování virtuálních počítačů během Off-Peak hodin ponechte minimální počet spuštěných virtuálních počítačů hostitele relace.

Nástroj pro škálování používá kombinaci Azure Automation účtu, Runbooku PowerShellu, Webhooku a aplikace logiky Azure k fungování. Když se nástroj spustí, aplikace logiky Azure zavolá Webhook a spustí Azure Automation sadu Runbook. Sada Runbook potom vytvoří úlohu.

Během špičky využívání úlohy zkontroluje aktuální počet relací a kapacitu virtuálního počítače aktuálně spuštěného hostitele relace pro každý fond hostitelů. Tyto informace používá k výpočtu, jestli virtuální počítače hostitele spuštěné relace můžou podporovat existující relace na základě parametru *SessionThresholdPerCPU* definovaného pro soubor **CreateOrUpdateAzLogicApp.ps1** . Pokud virtuální počítače hostitele relace nepodporují existující relace, spustí úloha další virtuální počítače hostitele relace ve fondu hostitelů.

>[!NOTE]
>*SessionThresholdPerCPU* neomezuje počet relací na virtuálním počítači. Tento parametr určuje, zda je nutné spustit nové virtuální počítače pro vyrovnávání zatížení připojení. Pokud chcete omezit počet relací, musíte podle pokynů [set-RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) nakonfigurovat parametr *MaxSessionLimit* odpovídajícím způsobem.

Během doby mimo špičku úloha určuje, kolik virtuálních počítačů hostitelů relace by se mělo vypnout na základě parametru *MinimumNumberOfRDSH* . Pokud nastavíte parametr *LimitSecondsToForceLogOffUser* na nenulovou kladnou hodnotu, úloha nastaví virtuální počítače hostitele relace na režim vyprázdnění, aby nedocházelo k tomu, aby se nové relace připojovaly k hostitelům. Úloha potom upozorní každého aktuálně přihlášeného uživatele, aby ušetřil svou práci, čekala nakonfigurovanou dobu a pak vynutila odhlášení uživatelů. Po odhlášení všech uživatelských relací na virtuálním počítači hostitele relace dojde k vypnutí tohoto virtuálního počítače. Po vypnutí virtuálního počítače se úloha resetuje v režimu vyprazdňování hostitele relace.

>[!NOTE]
>Pokud jste ručně nastavili virtuální počítač hostitele relace na režim vyprázdnění, úloha nebude spravovat virtuální počítač hostitele relace. Pokud je virtuální počítač hostitele relace spuštěný a nastavený na režim vyprázdnění, bude se považovat za nedostupný, což způsobí, že úloha spustí další virtuální počítače pro zpracování zatížení. Než ručně nastavíte režim vyprázdnění, doporučujeme vám označit všechny virtuální počítače Azure. Značku můžete pojmenovat pomocí parametru *MaintenanceTagName* při pozdějším vytvoření plánovače aplikace Azure Logic App. Značky vám pomůžou tyto virtuální počítače odlišit od těch, které spravuje Nástroj pro škálování. Nastavení značky údržby také zabrání nástroji škálování v provádění změn na virtuálním počítači, dokud neodeberete značku.

Pokud nastavíte parametr *LimitSecondsToForceLogOffUser* na hodnotu nula, úloha umožní nastavení konfigurace relace v zadaných zásadách skupiny zpracovávat odhlašování uživatelských relací. Chcete-li zobrazit tyto zásady skupiny, přejděte na zásady **Konfigurace počítače**  >    >  **šablony pro správu**  >  **součásti systému Windows**  >  **Vzdálená plocha**  >  **hostitel relace vzdálené plochy**  >  **časových omezení relace**. Pokud na virtuálním počítači hostitele relace existují aktivní relace, úloha ponechá virtuální počítač hostitele relace spuštěný. Pokud neexistují žádné aktivní relace, úloha vypne virtuální počítač hostitele relace.

V každém okamžiku úloha také převezme *MaxSessionLimit* fondu hostitelů do účtu, aby zjistil, jestli je aktuální počet relací větší než 90% maximální kapacity. V takovém případě bude úloha spustit další virtuální počítače hostitele relace.

Úloha se pravidelně spouští na základě nastaveného intervalu opakování. Tento interval můžete změnit na základě velikosti prostředí pro virtuální počítače s Windows, ale nezapomeňte, že spouštění a vypínání virtuálních počítačů může nějakou dobu trvat, takže si nezapomeňte započítat zpoždění. Doporučujeme nastavit interval opakování na každých 15 minut.

Nástroj má však také následující omezení:

- Toto řešení se vztahuje jenom na virtuální počítače hostitelů s více relacemi ve fondu.
- Toto řešení spravuje virtuální počítače v jakékoli oblasti, ale dá se použít jenom ve stejném předplatném jako účet Azure Automation a aplikaci Azure Logic.
- Maximální doba běhu úlohy v Runbooku je 3 hodiny. Pokud spuštění nebo zastavení virtuálních počítačů ve fondu hostitelů trvá déle, úloha se nezdaří. Další podrobnosti najdete v tématu [sdílené prostředky](../../automation/automation-runbook-execution.md#fair-share).

>[!NOTE]
>Nástroj pro škálování řídí režim vyrovnávání zatížení fondu hostitelů, který aktuálně mění velikost. Nástroj používá režim vyrovnávání zatížení pro špičku i dobu mimo špičku.

## <a name="prerequisites"></a>Předpoklady

Než začnete s nastavením nástroje pro škálování, ujistěte se, že máte připravené následující akce:

- [Tenant a fond hostitelů virtuálních počítačů s Windows](create-host-pools-arm-template.md)
- Virtuální počítače fondu hostitele relace nakonfigurované a zaregistrované ve službě Virtual Desktop systému Windows
- Uživatel s [přístupem Přispěvatel](../../role-based-access-control/role-assignments-portal.md) v předplatném Azure

Počítač, který použijete k nasazení nástroje, musí mít:

- Windows PowerShell 5,1 nebo novější
- Microsoft AZ PowerShell Module

Pokud máte všechno připravené, můžeme začít.

## <a name="create-or-update-an-azure-automation-account"></a>Vytvoří nebo aktualizuje účet Azure Automation.

>[!NOTE]
>Pokud už máte účet Azure Automation se sadou Runbook, na kterém běží starší verze skriptu škálování, stačí vám postupovat podle pokynů níže, abyste se ujistili, že je aktualizovaný.

Nejdřív budete potřebovat účet Azure Automation ke spuštění Runbooku PowerShellu. Tento postup popisuje i v případě, že máte existující účet Azure Automation, který chcete použít k nastavení Runbooku PowerShellu. Postup nastavení této funkce:

1. Otevřete Windows PowerShell.

2. Spusťte následující rutinu, abyste se přihlásili ke svému účtu Azure.

    ```powershell
    Login-AzAccount
    ```

    >[!NOTE]
    >Váš účet musí mít oprávnění přispěvatele v předplatném Azure, ve kterém chcete nasadit nástroj pro škálování.

3. Spuštěním následující rutiny Stáhněte skript pro vytvoření účtu Azure Automation:

    ```powershell
    New-Item -ItemType Directory -Path "C:\Temp" -Force
    Set-Location -Path "C:\Temp"
    $Uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/CreateOrUpdateAzAutoAccount.ps1"
    # Download the script
    Invoke-WebRequest -Uri $Uri -OutFile ".\CreateOrUpdateAzAutoAccount.ps1"
    ```

4. Spuštěním následující rutiny spusťte skript a vytvořte účet Azure Automation. Můžete buď vyplnit hodnoty pro parametry, nebo je opatřit poznámkami a použít jejich výchozí hodnoty.

    ```powershell
    $Params = @{
         "AADTenantId"           = "<Azure_Active_Directory_tenant_ID>"   # Optional. If not specified, it will use the current Azure context
         "SubscriptionId"        = "<Azure_subscription_ID>"              # Optional. If not specified, it will use the current Azure context
         "ResourceGroupName"     = "<Resource_group_name>"                # Optional. Default: "WVDAutoScaleResourceGroup"
         "AutomationAccountName" = "<Automation_account_name>"            # Optional. Default: "WVDAutoScaleAutomationAccount"
         "Location"              = "<Azure_region_for_deployment>"
         "WorkspaceName"         = "<Log_analytics_workspace_name>"       # Optional. If specified, Log Analytics will be used to configure the custom log table that the runbook PowerShell script can send logs to
    }

    .\CreateOrUpdateAzAutoAccount.ps1 @Params
    ```

5. Výstup rutiny bude obsahovat identifikátor URI Webhooku. Ujistěte se, že zachováte záznam identifikátoru URI, protože ho použijete jako parametr při nastavování plánu spouštění pro aplikaci Azure Logic.

6. Pokud jste zadali parametr pro Log Analytics **pracovního prostoru** parametru, bude výstup rutiny zahrnovat taky ID pracovního prostoru Log Analytics a jeho primární klíč. Nezapomeňte si pamatovat identifikátor URI, protože ho budete muset později použít jako parametr při nastavování plánu spouštění pro aplikaci Azure Logic.

7. Až nastavíte účet Azure Automation, přihlaste se ke svému předplatnému Azure a ujistěte se, že se Váš účet Azure Automation a relevantní sada Runbook objevily v zadané skupině prostředků, jak je znázorněno na následujícím obrázku:

    >[!div class="mx-imgBorder"]
    >![Obrázek stránky s přehledem Azure zobrazující nově vytvořený Azure Automation účet a sadu Runbook.](media/automation-account.png)

    Pokud chcete zjistit, jestli je Webhook tam, kde by měl být, vyberte název Runbooku. V dalším kroku přejdete do části prostředky Runbooku a vyberete **Webhooky**.

## <a name="create-an-azure-automation-run-as-account"></a>Vytvoření účtu Azure Automation spustit jako

Teď, když máte účet Azure Automation, budete také muset vytvořit účet Azure Automation spustit jako, pokud ho ještě nemáte. Tento účet umožní nástroji přístup k prostředkům Azure.

[Azure Automation účet Spustit jako](../../automation/manage-runas-account.md) poskytuje ověřování pro správu prostředků v Azure pomocí rutin Azure. Když vytvoříte účet Spustit jako, vytvoří se v Azure Active Directory nový uživatel instančního objektu a přiřadí roli přispěvatele k uživatelskému objektu služby na úrovni předplatného. Účet Spustit jako pro Azure je skvělým způsobem, jak bezpečně ověřit pomocí certifikátů a hlavního názvu služby, aniž byste museli ukládat uživatelské jméno a heslo do objektu přihlašovacích údajů. Další informace o ověřování účtu Spustit jako najdete v tématu [omezení oprávnění účtu Spustit jako](../../automation/manage-runas-account.md#limit-run-as-account-permissions).

Každý uživatel, který je členem role správců předplatného a spolusprávcem předplatného, může vytvořit účet Spustit jako.

Vytvoření účtu Spustit jako ve vašem účtu Azure Automation:

1. V Azure Portal vyberte **všechny služby**. V seznamu prostředků zadejte a vyberte **účty Automation**.

2. Na stránce **účty Automation** vyberte název účtu Azure Automation.

3. V podokně na levé straně okna vyberte **účty Spustit jako** v části **Nastavení účtu** .

4. Vyberte **účet Spustit jako pro Azure**. Po zobrazení podokna **Přidat účet Spustit jako pro Azure** si přečtěte informace v přehledu a pak výběrem **vytvořit** spusťte proces vytváření účtů.

5. Počkejte několik minut, než Azure vytvoří účet Spustit jako. Průběh vytváření můžete sledovat v nabídce v části oznámení.

6. Po dokončení procesu se v zadaném Azure Automation účtu vytvoří Asset s názvem **AzureRunAsConnection** . Vyberte **účet Spustit jako pro Azure**. Asset připojení obsahuje ID aplikace, ID tenanta, ID předplatného a kryptografický otisk certifikátu. Zapamatujte si ID aplikace, protože ho použijete později. Na stránce **připojení** můžete také najít stejné informace. Pokud chcete přejít na tuto stránku, v podokně na levé straně okna vyberte v části **sdílené prostředky** možnost **připojení** a klikněte na prostředek připojení s názvem **AzureRunAsConnection**.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Vytvoření přiřazení role na virtuálním počítači s Windows

Dále je potřeba vytvořit přiřazení role, aby **AzureRunAsConnection** mohl komunikovat s virtuálním počítačem s Windows. Nezapomeňte použít PowerShell pro přihlášení pomocí účtu, který má oprávnění k vytváření přiřazení rolí.

Nejdřív Stáhněte a importujte [modul PowerShellu virtuálního počítače s Windows](/powershell/windows-virtual-desktop/overview/) , který chcete použít v relaci PowerShellu, pokud jste to ještě neudělali. Spusťte následující rutiny PowerShellu, abyste se připojili k virtuální ploše Windows a zobrazili své klienty.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

# If your tenant is not in "Default Tenant Group", uncomment the following line and specify the name of your tenant group
# Set-RdsContext -TenantGroupName "<Tenant_Group_Name>"

Get-RdsTenant
```

Po nalezení klienta s fondy hostitelů, které chcete škálovat, postupujte podle pokynů v části [Vytvoření účtu Spustit jako Azure Automation](#create-an-azure-automation-run-as-account) pro shromáždění ID aplikace **AzureRunAsConnection** a použijte název tenanta virtuálních počítačů s Windows, který jste získali z předchozí rutiny v následující rutině pro vytvoření přiřazení role:

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId "<applicationid>" -TenantName "<tenantname>"
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Vytvoření aplikace logiky Azure a plánu spuštění

Nakonec budete muset vytvořit aplikaci logiky Azure a nastavit plán spouštění pro nový nástroj pro škálování.

1. Otevřete Windows PowerShell.

2. Spusťte následující rutinu, abyste se přihlásili ke svému účtu Azure.

    ```powershell
    Login-AzAccount
    ```

3. Spuštěním následující rutiny Stáhněte skript pro vytvoření aplikace Azure Logic.

    ```powershell
    New-Item -ItemType Directory -Path "C:\Temp" -Force
    Set-Location -Path "C:\Temp"
    $Uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/CreateOrUpdateAzLogicApp.ps1"
    # Download the script
    Invoke-WebRequest -Uri $Uri -OutFile ".\CreateOrUpdateAzLogicApp.ps1"
    ```

4. Spuštěním následující rutiny se přihlaste k virtuálnímu počítači s Windows pomocí účtu, který má oprávnění vlastníka VP nebo RDS Přispěvatel.

    ```powershell
    Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

    # If your tenant is not in "Default Tenant Group", uncomment the following line and specify the name of your tenant group
    # Set-RdsContext -TenantGroupName "<Tenant_Group_Name>"
    ```

5. Spuštěním následujícího skriptu PowerShellu vytvořte aplikaci Azure Logic a plán spuštění pro fond hostitelů.

    >[!NOTE]
    >Tento skript budete muset spustit pro každý fond hostitelů, který chcete automaticky škálovat, ale potřebujete jenom jeden Azure Automation účet.

    ```powershell
    $AADTenantId = (Get-AzContext).Tenant.Id

    $AzSubscription = Get-AzSubscription | Out-GridView -OutputMode:Single -Title "Select your Azure Subscription"
    Select-AzSubscription -Subscription $AzSubscription.Id

    $ResourceGroup = Get-AzResourceGroup | Out-GridView -OutputMode:Single -Title "Select the resource group for the new Azure Logic App"

    $RDBrokerURL = (Get-RdsContext).DeploymentUrl
    $WVDTenant = Get-RdsTenant | Out-GridView -OutputMode:Single -Title "Select your WVD tenant"
    $WVDHostPool = Get-RdsHostPool -TenantName $WVDTenant.TenantName | Out-GridView -OutputMode:Single -Title "Select the host pool you'd like to scale"

    $LogAnalyticsWorkspaceId = Read-Host -Prompt "If you want to use Log Analytics, enter the Log Analytics Workspace ID returned by when you created the Azure Automation account, otherwise leave it blank"
    $LogAnalyticsPrimaryKey = Read-Host -Prompt "If you want to use Log Analytics, enter the Log Analytics Primary Key returned by when you created the Azure Automation account, otherwise leave it blank"
    $RecurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"
    $BeginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"
    $EndPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"
    $TimeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"
    $SessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"
    $MinimumNumberOfRDSH = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"
    $MaintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don't want to be managed by this scaling tool"
    $LimitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, any session host VM that has user sessions, will be left untouched"
    $LogOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"
    $LogOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

    $AutoAccount = Get-AzAutomationAccount | Out-GridView -OutputMode:Single -Title "Select the Azure Automation account"
    $AutoAccountConnection = Get-AzAutomationConnection -ResourceGroupName $AutoAccount.ResourceGroupName -AutomationAccountName $AutoAccount.AutomationAccountName | Out-GridView -OutputMode:Single -Title "Select the Azure RunAs connection asset"

    $WebhookURIAutoVar = Get-AzAutomationVariable -Name 'WebhookURI' -ResourceGroupName $AutoAccount.ResourceGroupName -AutomationAccountName $AutoAccount.AutomationAccountName

    $Params = @{
         "AADTenantId"                   = $AADTenantId                             # Optional. If not specified, it will use the current Azure context
         "SubscriptionID"                = $AzSubscription.Id                       # Optional. If not specified, it will use the current Azure context
         "ResourceGroupName"             = $ResourceGroup.ResourceGroupName         # Optional. Default: "WVDAutoScaleResourceGroup"
         "Location"                      = $ResourceGroup.Location                  # Optional. Default: "West US2"
         "RDBrokerURL"                   = $RDBrokerURL                             # Optional. Default: "https://rdbroker.wvd.microsoft.com"
         "TenantGroupName"               = $WVDTenant.TenantGroupName               # Optional. Default: "Default Tenant Group"
         "TenantName"                    = $WVDTenant.TenantName
         "HostPoolName"                  = $WVDHostPool.HostPoolName
         "LogAnalyticsWorkspaceId"       = $LogAnalyticsWorkspaceId                 # Optional. If not specified, script will not log to the Log Analytics
         "LogAnalyticsPrimaryKey"        = $LogAnalyticsPrimaryKey                  # Optional. If not specified, script will not log to the Log Analytics
         "ConnectionAssetName"           = $AutoAccountConnection.Name              # Optional. Default: "AzureRunAsConnection"
         "RecurrenceInterval"            = $RecurrenceInterval                      # Optional. Default: 15
         "BeginPeakTime"                 = $BeginPeakTime                           # Optional. Default: "09:00"
         "EndPeakTime"                   = $EndPeakTime                             # Optional. Default: "17:00"
         "TimeDifference"                = $TimeDifference                          # Optional. Default: "-7:00"
         "SessionThresholdPerCPU"        = $SessionThresholdPerCPU                  # Optional. Default: 1
         "MinimumNumberOfRDSH"           = $MinimumNumberOfRDSH                     # Optional. Default: 1
         "MaintenanceTagName"            = $MaintenanceTagName                      # Optional.
         "LimitSecondsToForceLogOffUser" = $LimitSecondsToForceLogOffUser           # Optional. Default: 1
         "LogOffMessageTitle"            = $LogOffMessageTitle                      # Optional. Default: "Machine is about to shutdown."
         "LogOffMessageBody"             = $LogOffMessageBody                       # Optional. Default: "Your session will be logged off. Please save and close everything."
         "WebhookURI"                    = $WebhookURIAutoVar.Value
    }

    .\CreateOrUpdateAzLogicApp.ps1 @Params
    ```

    Po spuštění skriptu se aplikace Azure Logic by měla zobrazit ve skupině prostředků, jak je znázorněno na následujícím obrázku.

    >[!div class="mx-imgBorder"]
    >![Obrázek stránky s přehledem pro ukázkovou aplikaci Azure Logic.](../media/logic-app.png)

    Chcete-li provést změny v plánu spuštění, jako je například změna intervalu opakování nebo časového pásma, přejít do plánovače automatického škálování aplikace logiky Azure a vybrat **Upravit** a přejít do návrháře Azure Logic App.

    >[!div class="mx-imgBorder"]
    >![Obrázek návrháře aplikace logiky Azure Nabídky opakování a Webhooku, které umožňují uživateli upravit časy opakování a soubor Webhooku, jsou otevřené.](../media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Správa nástroje pro škálování

Teď, když jste vytvořili Nástroj pro škálování, získáte přístup k jeho výstupu. Tato část popisuje několik funkcí, které můžete najít užitečné.

### <a name="view-job-status"></a>Zobrazení stavu úlohy

Můžete zobrazit souhrnnou stav všech úloh sady Runbook nebo zobrazit podrobnější stav konkrétní úlohy Runbooku v Azure Portal.

Napravo od vybraného účtu Azure Automation v části Statistika úlohy můžete zobrazit seznam souhrnů všech úloh sady Runbook. Po otevření stránky **úlohy** na levé straně okna se zobrazí stav aktuální úlohy, časy spuštění a časy dokončení.

>[!div class="mx-imgBorder"]
>![Snímek obrazovky se stránkou stavu úlohy.](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Zobrazení protokolů a škálování výstupu nástroje

Protokoly o škálování na více instancí a operace škálování na více instancí můžete zobrazit otevřením Runbooku a výběrem úlohy.

Přejděte do sady Runbook ve vaší skupině prostředků, která je hostitelem účtu Azure Automation a vyberte **Přehled**. Na stránce Přehled vyberte úlohu v části **nedávné úlohy** , abyste zobrazili její výstup nástroje pro změnu velikosti, jak je znázorněno na následujícím obrázku.

>[!div class="mx-imgBorder"]
>![Obrázek okna výstup pro nástroj pro škálování](media/tool-output.png)

### <a name="check-the-runbook-script-version-number"></a>Zkontroluje číslo verze skriptu Runbooku.

Verzi skriptu sady Runbook, kterou používáte, můžete zjistit tak, že otevřete soubor sady Runbook ve vašem účtu Azure Automation a vyberete **zobrazení**. Na pravé straně obrazovky se zobrazí skript pro Runbook. Ve skriptu uvidíte číslo verze ve formátu `v#.#.#` pod `SYNOPSIS` oddílem. Nejnovější číslo verze najdete [tady](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/wvd-scaling-script/basicScale.ps1#L1). Pokud se ve skriptu Runbooku nezobrazuje číslo verze, znamená to, že používáte starší verzi skriptu a hned byste ji měli aktualizovat. Pokud potřebujete aktualizovat skript Runbooku, postupujte podle pokynů v tématu [Vytvoření nebo aktualizace účtu Azure Automation](#create-or-update-an-azure-automation-account).

### <a name="reporting-issues"></a>Vytváření sestav – problémy

Při nahlášení problému budete muset zadat následující informace, které nám pomůžou při řešení potíží:

- Úplný protokol na kartě **všechny protokoly** v úloze, která způsobila problém. Pokud se chcete dozvědět, jak získat protokol, postupujte podle pokynů v tématu [zobrazení protokolů a škálování výstupu nástroje](#view-logs-and-scaling-tool-output). Pokud se v protokolu nacházejí citlivé nebo soukromé informace, můžete je před odesláním problému do nás odebrat.

- Verze skriptu sady Runbook, který používáte. Informace o tom, jak získat číslo verze, najdete v tématu [Zkontrolujte číslo verze skriptu Runbooku](#check-the-runbook-script-version-number) .

- Číslo verze každého z následujících modulů prostředí PowerShell nainstalovaných ve vašem Azure Automationm účtu. Pokud chcete tyto moduly najít, otevřete Azure Automation účet, v části **sdílené prostředky** v podokně na levé straně okna vyberte **moduly** a pak vyhledejte název modulu.
    - Az.Accounts
    - Az.Compute
    - Az.Resources
    - Az.Automation
    - OMSIngestionAPI
    - Microsoft. RDInfra. RDPowershell

- Datum vypršení platnosti [účtu Spustit jako](#create-an-azure-automation-run-as-account). Pokud to chcete najít, otevřete účet Azure Automation a v části **Nastavení účtu** vyberte **účty Spustit jako** v podokně na levé straně okna. Datum vypršení platnosti by mělo být pod **účtem Azure spustit jako**.

### <a name="log-analytics"></a>Log Analytics

Pokud jste se rozhodli použít Log Analytics, můžete všechna data protokolu zobrazit ve vlastním protokolu s názvem **WVDTenantScale_CL** v části **vlastní protokoly** v zobrazení **protokolů** v pracovním prostoru Log Analytics. Uvádíme několik ukázkových dotazů, které můžete najít užitečné.

- Pokud chcete zobrazit všechny protokoly pro fond hostitelů, zadejte následující dotaz.

    ```Kusto
    WVDTenantScale_CL
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- Pokud chcete zobrazit celkový počet aktuálně spuštěných virtuálních počítačů hostitelů relací a aktivních uživatelských relací ve fondu hostitelů, zadejte následující dotaz.

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "Number of running session hosts:"
         or logmessage_s contains "Number of user sessions:"
         or logmessage_s contains "Number of user sessions per Core:"
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- Pokud chcete zobrazit stav všech virtuálních počítačů hostitelů relací v hostitelském fondu, zadejte následující dotaz.

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "Session host:"
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- Pokud chcete zobrazit všechny chyby a upozornění, zadejte následující dotaz.

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "ERROR:" or logmessage_s contains "WARN:"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

## <a name="report-issues"></a>Nahlášení potíží

Sestavy problémů pro nástroj škálování jsou aktuálně zpracovávány podpora Microsoftu. Při vytváření sestavy problému nezapomeňte postupovat podle pokynů v tématu [hlášení problémů](#reporting-issues). Pokud máte svůj názor na nástroj nebo chcete požádat o nové funkce, otevřete na [stránce GitHubu služby Vzdálená](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4-WVD-scaling-tool)plocha problém GitHub označený "4-WVD-reškálování-Tool".
