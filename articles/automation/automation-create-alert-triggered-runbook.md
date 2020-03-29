---
title: Spuštění runbooku Azure Automation pomocí výstrahy
description: Zjistěte, jak spustit runbook ke spuštění při vyvolání výstrahy Azure.
services: automation
ms.subservice: process-automation
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: df28116c588ed77f02c78a42a85feb91ca339e7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75366696"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Spuštění runbooku Azure Automation pomocí výstrahy

[Azure Monitor](../azure-monitor/overview.md?toc=%2fazure%2fautomation%2ftoc.json) můžete použít ke sledování metrik y základní úrovně a protokolů pro většinu služeb v Azure. Runbooky Azure Automation můžete volat pomocí [skupin akcí](../azure-monitor/platform/action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) nebo pomocí klasických výstrah k automatizaci úloh založených na výstrahách. Tento článek ukazuje, jak nakonfigurovat a spustit runbook pomocí výstrah.

## <a name="alert-types"></a>Typy výstrah

Runbooky automatizace můžete používat se třemi typy výstrah:

* Běžná upozornění
* Upozornění protokolu aktivit
* Upozornění na metriky téměř v reálném čase

> [!NOTE]
> Společné schéma výstrah standardizuje možnosti spotřeby pro oznámení výstrah v Azure dnes. Historicky tři typy výstrah v Azure dnes (metrika, protokol a protokol aktivit) měli své vlastní šablony e-mailu, webhook schémata, atd. Další informace najdete [v tématu Společné schéma výstrah](../azure-monitor/platform/alerts-common-schema.md)

Když výstraha volá runbook, skutečné volání je požadavek HTTP POST na webhooku. Tělo požadavku POST obsahuje objekt ve formátu JSON, který má užitečné vlastnosti, které souvisejí s výstrahou. V následující tabulce jsou uvedeny odkazy na schéma datové části pro každý typ výstrahy:

|Výstrahy  |Popis|Schéma datové části  |
|---------|---------|---------|
|[Běžná výstraha](../azure-monitor/platform/alerts-common-schema.md?toc=%2fazure%2fautomation%2ftoc.json)|Společné schéma výstrah, které standardizuje možnosti spotřeby pro oznámení výstrah v Azure dnes.|Běžné schéma datové části výstrahy|
|[Výstraha protokolu aktivit](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Odešle oznámení, když každá nová událost v protokolu aktivit Azure odpovídá konkrétní podmínky. Například když `Delete VM` dojde k operaci v **myProductionResourceGroup** nebo když se zobrazí nová událost Azure Service Health se stavem **Active.**| [Schéma datové části výstrahprotokolu aktivit](../azure-monitor/platform/activity-log-alerts-webhook.md)        |
|[Upozornění na metriku téměř v reálném čase](../azure-monitor/platform/alerts-metric-near-real-time.md?toc=%2fazure%2fautomation%2ftoc.json)    |Odešle oznámení rychleji než upozornění metriky, když jedna nebo více metrik na úrovni platformy splňuje zadané podmínky. Například pokud je hodnota **procesoru %** na virtuálním počítači větší než **90**a hodnota pro **síť v** je větší než **500 MB** za posledních 5 minut.| [Schéma užitečného zatížení metriky téměř v reálném čase](../azure-monitor/platform/alerts-webhooks.md#payload-schema)          |

Vzhledem k tomu, že data, která jsou k dispozici každý typ výstrahy se liší, každý typ výstrahy je zpracována odlišně. V další části se dozvíte, jak vytvořit runbook pro zpracování různých typů výstrah.

## <a name="create-a-runbook-to-handle-alerts"></a>Vytvoření runbooku pro zpracování výstrah

Chcete-li používat automatizaci s výstrahami, potřebujete runbook, který má logiku, která spravuje datovou část výstrahy JSON, která je předána do runbooku. Následující příklad runbooku musí být volán z výstrahy Azure.

Jak je popsáno v předchozí části, každý typ výstrahy má jiné schéma. Skript přebírá data webhooku `WebhookData` ve vstupním parametru sady Runbook z výstrahy. Potom skript vyhodnotí datovou část JSON k určení, který typ výstrahy byl použit.

Tento příklad používá výstrahu z virtuálního virtuálního mísy. Načte data virtuálního soudu z datové části a pak tyto informace použije k zastavení virtuálního soudu. Připojení musí být nastaveno v účtu Automation, kde je spuštěna sada Runbook. Při použití výstrah pro aktivaci runbooků je důležité zkontrolovat stav výstrahy v aktivované sady Runbook. Runbook se aktivuje pokaždé, když se výstraha změní stav. Výstrahy mají více stavů, `Activated` dva `Resolved`nejběžnější stavy jsou a . Zkontrolujte tento stav v logice runbooku, abyste zajistili, že váš runbook neběží více než jednou. Příklad v tomto článku ukazuje, `Activated` jak hledat pouze výstrahy.

Runbook používá účet **AzureRunAsConnection** [Run As](automation-create-runas-account.md) k ověření pomocí Azure k provedení akce správy proti virtuálnímu počítači.

Tento příklad slouží k vytvoření sady Runbook s názvem **Stop-AzureVmInResponsetoVMAlert**. Můžete upravit skript Prostředí PowerShell a použít jej s mnoha různými prostředky.

1. Přejděte na svůj účet Azure Automation.
2. V části **Automatizace procesů**vyberte **Runbook**.
3. V horní části seznamu runbooků vyberte **+ Vytvořit runbook**.
4. Na stránce **Přidat runbook** zadejte pro název sady Runbook **stop-AzureVmInResponsetoVMAlert.** Pro typ runbooku vyberte **PowerShell**. Potom vyberte **Create** (Vytvořit).  
5. Zkopírujte následující příklad PowerShellu na stránku **Úpravy.**

    ```powershell-interactive
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )
    $ErrorActionPreference = "stop"

    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema)
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "azureMonitorCommonAlertSchema") {
            # This is the common Metric Alert schema (released March 2019)
            $Essentials = [object] ($WebhookBody.data).essentials
            # Get the first target only as this script doesn't handle multiple
            $alertTargetIdArray = (($Essentials.alertTargetIds)[0]).Split("/")
            $SubId = ($alertTargetIdArray)[2]
            $ResourceGroupName = ($alertTargetIdArray)[4]
            $ResourceType = ($alertTargetIdArray)[6] + "/" + ($alertTargetIdArray)[7]
            $ResourceName = ($alertTargetIdArray)[-1]
            $status = $Essentials.monitorCondition
        }
        elseif ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # Schema not supported
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if (($status -eq "Activated") -or ($status -eq "Fired"))
        {
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Determine code path depending on the resourceType
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is an Resource Manager VM
                Write-Verbose "This is an Resource Manager VM." -Verbose

                # Authenticate to Azure with service principal and certificate and set subscription
                Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
                $ConnectionAssetName = "AzureRunAsConnection"
                Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
                $Conn = Get-AutomationConnection -Name $ConnectionAssetName
                if ($Conn -eq $null)
                {
                    throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
                }
                Write-Verbose "Authenticating to Azure with service principal." -Verbose
                Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the Resource Manager VM
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType not supported
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated' or 'Fired' so no action taken
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```

6. Vyberte **Publikovat,** chcete-li soubor Runbook uložit a publikovat.

## <a name="create-the-alert"></a>Vytvoření výstrahy

Výstrahy používají skupiny akcí, což jsou kolekce akcí, které jsou vyvolány výstrahou. Sady Runbook jsou pouze jednou z mnoha akcí, které můžete použít se skupinami akcí.

1. V účtu Automation vyberte v části Sledování **možnostI** **Výstrahy**.
1. Vyberte **+ Nové pravidlo upozornění**.
1. Klepněte na **tlačítko Vybrat** v části **Zdroj**. Na stránce **Vybrat prostředek** vyberte virtuální počítač, na který chcete upozornit, a klikněte na **Hotovo**.
1. Klepněte na tlačítko **Přidat podmínku** **pod podmínkou**. Vyberte signál, který chcete použít, například **Procento procesoru** a klepněte na **tlačítko Hotovo**.
1. Na stránce **Konfigurovat logiku signálu** zadejte **hodnotu Prahová hodnota** v části **Logika výstrah**a klepněte na tlačítko **Hotovo**.
1. V části **Skupiny akcí**vyberte **Vytvořit nový**.
1. Na stránce **Přidat skupinu akcí** pojmenujte skupinu akcí a krátký název.
1. Pojmenujte akci. Pro typ akce vyberte **Automation Runbook**.
1. Vyberte **Upravit podrobnosti**. Na stránce **Konfigurovat aplikaci Runbook** vyberte v části **Zdroj aplikace Runbook** **položku Uživatel**.  
1. Vyberte účet **předplatného** a **automatizace**a pak vyberte **runbook Stop-AzureVmInResponsetoVMAlert.**  
1. Vyberte **ano,** **chcete-li povolit společné schéma výstrah**.
1. Chcete-li vytvořit skupinu akcí, vyberte **ok**.

    ![Stránka Skupiny akcí přidání](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    Tuto skupinu akcí můžete použít ve [výstrahách protokolu aktivit](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) a [výstrahách téměř v reálném čase,](../azure-monitor/platform/alerts-overview.md?toc=%2fazure%2fautomation%2ftoc.json) které vytvoříte.

1. V části **Podrobnosti výstrahy**přidejte název a popis pravidla výstrahy a klepněte na **tlačítko Vytvořit pravidlo výstrah**.

## <a name="next-steps"></a>Další kroky

* Další informace o spuštění runbooku Automatizace pomocí webhooku najdete v [tématu Spuštění runbooku z webhooku](automation-webhooks.md).
* Podrobnosti o různých způsobech spuštění runbooku najdete v [tématu Spuštění runbooku](automation-starting-a-runbook.md).
* Informace o tom, jak vytvořit výstrahu protokolu aktivit, najdete v [tématu Vytvoření výstrah protokolu aktivit](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Informace o tom, jak vytvořit výstrahu téměř v reálném čase, najdete [v tématu Vytvoření pravidla výstrahy na webu Azure Portal](../azure-monitor/platform/alerts-metric.md?toc=/azure/azure-monitor/toc.json).
