---
title: Aktivace sady Runbook Azure Automation pomocí výstrahy
description: Tento článek obsahuje informace o tom, jak aktivovat Runbook, který se spustí při vyvolání výstrahy Azure.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2021
ms.topic: conceptual
ms.openlocfilehash: 0483b171ee65ac55d65261140738bc5c1838873f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732288"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Aktivace sady Runbook Azure Automation pomocí výstrahy

Pomocí [Azure monitor](../azure-monitor/overview.md) můžete monitorovat metriky a protokoly základní úrovně pro většinu služeb v Azure. Můžete volat Azure Automation Runbooky pomocí [skupin akcí](../azure-monitor/platform/action-groups.md) pro automatizaci úloh na základě výstrah. V tomto článku se dozvíte, jak nakonfigurovat a spustit Runbook pomocí výstrah.

## <a name="alert-types"></a>Typy výstrah

Runbooky Automation můžete používat se třemi typy výstrah:

* Běžná upozornění
* Upozornění protokolu aktivit
* Výstrahy metriky téměř v reálném čase

> [!NOTE]
> Běžné schéma výstrah standardizace prostředí spotřeby pro oznamování výstrah v Azure ještě dnes. V minulosti měly tři typy výstrah v Azure ještě dnes (metrika, protokol a protokol aktivit) vlastní e-mailové šablony, schémata webhooků atd. Další informace najdete v tématu [běžné schéma výstrah](../azure-monitor/alerts/alerts-common-schema.md) .

Když výstraha zavolá Runbook, samotné volání je požadavek HTTP POST Webhooku. Tělo žádosti POST obsahuje objekt ve formátu JSON, který obsahuje užitečné vlastnosti, které se vztahují k výstraze. V následující tabulce jsou uvedeny odkazy na schéma datové části pro každý typ výstrahy:

|Výstrahy  |Popis|Schéma datové části  |
|---------|---------|---------|
|[Běžná výstraha](../azure-monitor/alerts/alerts-common-schema.md)|Běžné schéma výstrah, které spojuje prostředí spotřeby pro oznamování výstrah v Azure ještě dnes.|Společné schéma datové části výstrahy|
|[Upozornění protokolu aktivit](../azure-monitor/alerts/activity-log-alerts.md)    |Pošle oznámení, pokud jakákoli nová událost v protokolu aktivit Azure odpovídá specifickým podmínkám. Například při `Delete VM` výskytu operace v **myProductionResourceGroup** nebo při zobrazení nové události Azure Service Health s aktivním stavem.| [Schéma datové části upozornění protokolu aktivit](../azure-monitor/alerts/activity-log-alerts-webhook.md)        |
|[Výstraha metriky téměř v reálném čase](../azure-monitor/alerts/alerts-metric-near-real-time.md)    |Pokud jedna nebo více metrik na úrovni platformy splňuje zadané podmínky, pošle oznámení rychleji než výstrahy metriky. Například pokud je hodnota pro **procesor%** na virtuálním počítači větší než 90 a hodnota pro **síť v** je větší než 500 MB za posledních 5 minut.| [Schéma datové části výstrah téměř v reálném čase](../azure-monitor/alerts/alerts-webhooks.md#payload-schema)          |

Vzhledem k tomu, že se data poskytnutá jednotlivými typy výstrah liší, je každý typ výstrahy zpracováván jinak. V další části se dozvíte, jak vytvořit Runbook pro zpracování různých typů výstrah.

## <a name="create-a-runbook-to-handle-alerts"></a>Vytvoření Runbooku pro zpracování výstrah

Chcete-li použít automatizaci s výstrahami, potřebujete sadu Runbook s logikou, která spravuje datovou část JSON výstrahy, která je předána sadě Runbook. Následující příklad Runbooku musí být volán z výstrahy Azure.

Jak je popsáno v předchozí části, každý typ výstrahy má jiné schéma. Skript převezme data Webhooku z výstrahy ve `WebhookData` vstupním parametru Runbooku. Potom skript vyhodnocuje datovou část JSON a určí, který typ výstrahy se používá.

V tomto příkladu se používá výstraha z virtuálního počítače. Načte data virtuálního počítače z datové části a pak tyto informace použije k zastavení virtuálního počítače. Připojení musí být nastaveno v účtu Automation, ve kterém je sada Runbook spuštěna. Pokud používáte výstrahy pro aktivaci runbooků, je důležité kontrolovat stav upozornění v aktivovaném Runbooku. Sada Runbook se spustí při každé změně stavu výstrahy. Výstrahy mají více stavů, přičemž tyto dva nejběžnější jsou aktivované a vyřešené. Zkontrolujte stav v logice Runbooku, aby se zajistilo, že se Runbook nespustí víckrát než jednou. V příkladu v tomto článku se dozvíte, jak vyhledat výstrahy se stavem aktivováno.

Sada Runbook používá `AzureRunAsConnection` [účet Spustit jako](./automation-security-overview.md) prostředku připojení k ověření v Azure k provedení akce správy na virtuálním počítači.

Tento příklad slouží k vytvoření sady Runbook s názvem **stop-AzureVmInResponsetoVMAlert**. Skript PowerShellu můžete upravit a použít ho s mnoha různými prostředky.

1. Přejít na účet Azure Automation.
2. V části **Automatizace procesu** vyberte **Runbooky**.
3. V horní části seznamu sad Runbook vyberte **+ vytvořit Runbook**.
4. Na stránce **Přidat Runbook** zadejte **stop-AzureVmInResponsetoVMAlert** pro název Runbooku. Jako typ Runbooku vyberte **PowerShell**. Potom vyberte **Vytvořit**.  
5. Zkopírujte následující příklad PowerShellu do stránky pro **Úpravy** .

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
                Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the Resource Manager VM
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
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

6. Vyberte **publikovat** a Runbook uložte a publikujte.

## <a name="create-the-alert"></a>Vytvoření výstrahy

Výstrahy používají skupiny akcí, což jsou kolekce akcí, které výstraha aktivovala. Sady Runbook jsou pouze jednou z mnoha akcí, které lze použít se skupinami akcí.

1. Ve svém účtu Automation v části **monitorování** vyberte **výstrahy** .
1. Vyberte **+ Nové pravidlo upozornění**.
1. Klikněte na **Vybrat** v části **prostředek**. Na stránce **Vybrat prostředek** vyberte svůj virtuální počítač, který se má varovat vypnout, a klikněte na **Hotovo**.
1. V části **Podmínka** klikněte na **Přidat podmínku** . Vyberte signál, který chcete použít, například **Procento procesoru** a klikněte na **Hotovo**.
1. Na stránce **Konfigurovat logiku signálu** zadejte **mezní hodnotu** v části **logika výstrahy** a klikněte na **Hotovo**.
1. V části **skupiny akcí** vyberte **vytvořit novou**.
1. Na stránce **Přidat skupinu akcí** zadejte název a krátký název skupiny akcí.
1. Dejte akci název. Jako typ akce vyberte **sada Automation Runbook**.
1. Vyberte **Upravit podrobnosti**. Na stránce **Konfigurovat Runbook** vyberte v části **zdroj Runbooku** možnost **uživatel**.  
1. Vyberte **předplatné** a **účet Automation** a potom vyberte Runbook **stop-AzureVmInResponsetoVMAlert** .  
1. Pokud chcete **Povolit společné schéma výstrah**, vyberte **Ano** .
1. Pokud chcete vytvořit skupinu akcí, vyberte **OK**.

    ![Přidat stránku skupiny akcí](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    Tuto skupinu akcí můžete použít v [upozorněních protokolu aktivit](../azure-monitor/alerts/activity-log-alerts.md) a v [téměř vytvořených výstrahách v reálném čase](../azure-monitor/alerts/alerts-overview.md) .

1. V části **Podrobnosti výstrahy** přidejte název a popis pravidla výstrahy a klikněte na **vytvořit pravidlo upozornění**.

## <a name="next-steps"></a>Další kroky

* Pokud chcete spustit Runbook pomocí Webhooku, přečtěte si téma [Spuštění runbooku z Webhooku](automation-webhooks.md).
* Další způsoby, jak spustit sadu Runbook, najdete v tématu [Spuštění Runbooku](./start-runbooks.md).
* Pokud chcete vytvořit upozornění protokolu aktivit, přečtěte si téma [vytvoření výstrah protokolu](../azure-monitor/alerts/activity-log-alerts.md)aktivit.
* Informace o tom, jak vytvořit upozornění téměř v reálném čase, najdete [v tématu Vytvoření pravidla výstrahy v Azure Portal](../azure-monitor/alerts/alerts-metric.md?toc=/azure/azure-monitor/toc.json).
* Referenční informace k rutinám PowerShellu najdete v tématu [AZ. Automation](/powershell/module/az.automation).
