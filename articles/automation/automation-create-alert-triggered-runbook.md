---
title: Použít výstrahu pro spuštění runbooku automatizace Azure
description: Zjistěte, jak se aktivovat sadu runbook spustit, když je vydána výstraha Azure.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 6116bc5b29d2c3b07114582623fd8575346dc84e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Použít výstrahu pro spuštění runbooku automatizace Azure

Můžete použít [Azure monitorování](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md?toc=%2fazure%2fautomation%2ftoc.json) monitorování základní úroveň metriky a protokoly pro většinu služby v Azure. Azure Automation runbook můžete volat pomocí [skupiny akcí](../monitoring-and-diagnostics/monitoring-action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) nebo pomocí klasického výstrahy k automatizaci úloh na základě výstrah. Tento článek ukazuje, jak nakonfigurovat a spuštění sady runbook pomocí výstrah.

## <a name="alert-types"></a>Typy výstrah

Sady automation runbook můžete použít s tři typy výstrah:
* Classic metriky výstrahy
* Upozornění protokolu aktivit
* Téměř v reálném čase metriky výstrahy

Když výstrahu volá sadu runbook, je vlastní volání požadavek HTTP POST webhooku. Text požadavku POST obsahuje objekt uvedena ve správném formátu JSON, který má užitečné vlastnosti, které se vztahují k výstraze. V následující tabulce najdete odkazy na schéma datové části pro jednotlivé typy výstrah:

|Výstrahy  |Popis|Datová část schématu  |
|---------|---------|---------|
|[Classic metriky výstrahy](../monitoring-and-diagnostics/insights-alerts-portal.md?toc=%2fazure%2fautomation%2ftoc.json)    |Odešle oznámení, když všechny platformy úrovni metrika splňuje určité podmínky. Například, pokud hodnota **% využití procesoru** na virtuální počítač je větší než **90** za posledních 5 minut.| [Schéma metriky výstrahy datové části – třída](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)         |
|[Výstraha aktivity protokolu](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Odešle oznámení, když všechny nové události v protokolu aktivita Azure odpovídá konkrétní podmínky. Například když `Delete VM` operace probíhá v **myProductionResourceGroup** nebo když novou událost stavu služby Azure s **Active** stav se zobrazí.| [Schéma výstrahy datové části protokolu činnosti](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)        |
|[Téměř v reálném čase metriky výstrahy](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Odešle oznámení rychleji než metriky výstrahy, když jeden nebo více metriky úrovně platformu splňují určitá kritéria. Například, pokud hodnota **% využití procesoru** na virtuální počítač je větší než **90**a hodnota **sítě v** je větší než **500 MB** za poslední 5 počet minut.| [Téměř v reálném čase metriky výstrahy datové schéma](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)          |

Vzhledem k tomu, že data, která poskytuje každý typ výstrahy se liší, každý typ výstrahy se proto liší. V další části zjistěte, jak vytvořit sadu runbook pro zpracování různých typů výstrah.

## <a name="create-a-runbook-to-handle-alerts"></a>Vytvoření sady runbook pro zpracování výstrahy

Chcete-li použití automatizace s výstrahami, musíte sadu runbook, která má logiku, která spravuje výstrahy datové části JSON, který je předán do runbooku. Následující ukázkový runbook musí být volána z Azure výstrahy. 

Jak je popsáno v předchozí části, každý typ výstrahy má jiné schéma. Skript provede v datech webhooku v `WebhookData` vstupní parametr runbooku z výstrahy. Skript potom vyhodnotí datové části JSON k určení, který typ výstrahy byl použit. 

Tento příklad používá výstrahu z virtuálního počítače. Načte data virtuálního počítače z datové části a potom použije tyto informace k zastavení virtuálního počítače. Připojení musí být nastavené v účtu služby Automation kde spuštění sady runbook.

Sada runbook používá **AzureRunAsConnection** [účet Spustit jako](automation-create-runas-account.md) k ověření pomocí Azure k provedení akce správy u virtuálního počítače.

Použijte tento příklad k vytvoření runbook s názvem **Stop-AzureVmInResponsetoVMAlert**. Můžete upravit skript prostředí PowerShell a jeho použití s mnoha různých prostředků.

1. Přejděte na svůj účet Azure Automation.
1. V části **AUTOMATIZACE procesu**, vyberte **Runbooky**.
1. V horní části seznamu sad runbook, vyberte **přidat runbook**. 
1. Na stránce **Přidat runbook** vyberte **Rychlé vytvoření**.
1. Název sady runbook, zadejte **Stop-AzureVmInResponsetoVMAlert**. Typ runbooku, vyberte **prostředí PowerShell**. Potom vyberte **Create** (Vytvořit).  
1. Zkopírujte následující příklad PowerShell do **upravit** podokně. 

    ```powershell-interactive
    <#
    .SYNOPSIS
    This runbook stops a resource management VM in response to an Azure alert trigger.

    .DESCRIPTION
    This runbook stops a resource management VM in response to an Azure alert trigger.
    The input is alert data that has the information required to identify which VM to stop.
    
    DEPENDENCIES
    - The runbook must be called from an Azure alert via a webhook.
    
    REQUIRED AUTOMATION ASSETS
    - An Automation connection asset called "AzureRunAsConnection" that is of type AzureRunAsConnection.
    - An Automation certificate asset called "AzureRunAsCertificate".

    .PARAMETER WebhookData
    Optional. (The user doesn't need to enter anything, but the service always passes an object.)
    This is the data that's sent in the webhook that's triggered from the alert.

    .NOTES
    AUTHOR: Azure Automation Team
    LASTEDIT: 2017-11-22
    #>

    [OutputType("PSAzureOperationResponse")]

    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )

    $ErrorActionPreference = "stop"

    if ($WebhookData)
    {
        # Get the data object from WebhookData.
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema).
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # The schema isn't supported.
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if ($status -eq "Activated")
        {
            $ResourceType = $AlertContext.resourceType
            $ResourceGroupName = $AlertContext.resourceGroupName
            $SubId = $AlertContext.subscriptionId
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Use this only if this is a resource management VM.
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is the VM.
                Write-Verbose "This is a resource management VM." -Verbose

                # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.
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

                # Stop the VM.
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType isn't supported.
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated', so no action taken.
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```
1. Vyberte **publikovat** uložte a publikovat sadu runbook.

## <a name="create-an-action-group"></a>Vytvořit skupinu akce

Skupinu akcí je kolekce akcí, které jsou aktivovány výstrahu. Runbooky jsou pouze jedním z mnoha akce, které můžete použít se skupinami akce.

1. Na portálu Azure vyberte **monitorování** > **nastavení** > **skupiny akcí**.
1. Vyberte **přidat akci skupinu**a potom zadejte požadované informace:  
    1. V **název skupiny akce** pole, zadejte název.
    1. V **krátký název** pole, zadejte název. Krátký název se používá namísto názvu skupiny úplné akce při odesílání oznámení pomocí této akce skupiny.
    1. **Předplatné** pole je automaticky vyplněno vaším aktuálním předplatným. Toto je předplatné, ve kterém je akce skupinu uložit.
    1. Vyberte skupinu prostředků, ve kterém je akce skupinu uložit.

V tomto příkladu vytvoříte dvě akce: akci sady runbook a akci oznámení.

### <a name="runbook-action"></a>Akce sady Runbook

Vytvoření sady runbook akce ve skupině akce:

1. V části **akce**, pro **název akce**, zadejte název pro akci. Pro **typ akce**, vyberte **sady Automation Runbook**.
1. V části **podrobnosti**, vyberte **upravit podrobnosti**.  
1. Na **konfigurace Runbook** v části **zdroj Runbooku**, vyberte **uživatele**.  
1. Vyberte vaše **předplatné** a **účet Automation**a pak vyberte **Stop-AzureVmInResponsetoVMAlert** sady runbook.  
1. Po dokončení vyberte **OK**.

### <a name="notification-action"></a>Akce oznámení

Chcete-li vytvořit akci oznámení ve skupině akce:

1. V části **akce**, pro **název akce**, zadejte název pro akci. Pro **typ akce**, vyberte **e-mailu**.  
1. V části **podrobnosti** vybrat, **upravit podrobnosti**.  
1. Na **e-mailu** zadejte e-mailovou adresu chcete použít pro oznámení a pak vyberte **OK**. Přidání e-mailovou adresu kromě runbook jako akce je užitečné. Upozornění se zobrazí při spuštění runbooku.  

    Skupině akce by měl vypadat podobně jako na následujícím obrázku:

   ![Přidat stránku skupiny akce](./media/automation-create-alert-triggered-runbook/add-action-group.png)
1. Chcete-li vytvořit skupinu akce, vyberte **OK**.

Můžete použít tuto skupinu akce v [aktivity protokolu výstrahy](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) a [téměř v reálném čase výstrahy](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal) , kterou vytvoříte.

## <a name="classic-alert"></a>Classic výstrahy

Classic výstrahy jsou založené na metriky a nepoužívejte skupiny akcí. Můžete však nastavit runbook akci založenou na classic výstrahy. 

Vytvoření klasického výstrahy:

1. Vyberte **Přidat upozornění metriky**.
1. Název metriky upozornění **myVMCPUAlert**. Zadejte stručný popis výstrahy.
1. Metriky podmínka upozornění, vyberte **větší než**. Pro **prahová hodnota** hodnotu, vyberte **10**. Pro **období** hodnotu, vyberte **za posledních pět minut**.
1. V části **akce**, vyberte **spuštění sady runbook z této výstrahy**.
1. Na **konfigurace Runbook** stránky, pro **zdroj Runbooku**, vyberte **uživatele**. Vyberte svůj účet automation a pak vyberte **Stop-AzureVmInResponsetoVMAlert** sady runbook. Vyberte **OK**.
1. Chcete-li uložit pravidlo výstrahy, vyberte **OK**.

## <a name="next-steps"></a>Další postup

* Další informace o spouštění sady runbook automatizaci pomocí webhook, jehož najdete v tématu [spuštění runbooku z webhook, jehož](automation-webhooks.md).
* Podrobnosti o různých způsobech spouštění sady runbook najdete v tématu [spuštění sady runbook](automation-starting-a-runbook.md).
* Naučte se vytvořit výstrahu protokolu aktivit, najdete v tématu [vytvořit aktivitu protokolu výstrahy](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Naučte se vytvářet upozornění na near v reálném čase, najdete v tématu [vytvořit pravidlo výstrahy na portálu Azure](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal).
