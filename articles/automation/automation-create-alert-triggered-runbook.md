---
title: Pomocí upozornění můžete aktivovat runbooku Azure Automation
description: Zjistěte, jak aktivovat sadu runbook spustit, když se vyvolá výstraha Azure.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/18/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0df08eaa2de27dbcc1ea93db1e040d81d071bf80
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46125999"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Pomocí upozornění můžete aktivovat runbooku Azure Automation

Můžete použít [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md?toc=%2fazure%2fautomation%2ftoc.json) monitorovat základní metriky a protokoly pro většinu služby v Azure. Runbooky Azure Automation můžete volat pomocí [skupiny akcí](../monitoring-and-diagnostics/monitoring-action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) nebo pomocí klasických upozornění pro automatizaci úloh na základě výstrah. Tento článek popisuje, jak ke konfiguraci a spuštění sady runbook pomocí výstrah.

## <a name="alert-types"></a>Typy výstrah

Runbooky automation můžete používat tři typy výstrah:
* Klasického upozornění metrik
* Upozornění protokolu aktivit
* Téměř v reálném čase upozornění na metriku

Pokud upozornění volá runbook, je vlastní volání požadavek HTTP POST webhooku. Text požadavku POST obsahuje nesprávný formát JSON objektu, který má užitečným vlastnostem, které se vztahují na upozornění. V následující tabulce jsou uvedeny odkazy na schéma datové části pro každý typ výstrahy:

|Výstrahy  |Popis|Datová část schématu  |
|---------|---------|---------|
|[Klasických upozornění na metriku](../monitoring-and-diagnostics/insights-alerts-portal.md?toc=%2fazure%2fautomation%2ftoc.json)    |Když jakékoliv úrovni platformy metriky splní určitou podmínku, pošle se oznámení. Například, když hodnota **využití procesoru %** na virtuálním počítači je větší než **90** za posledních 5 minut.| [Schéma tříd metriky upozornění datové části](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)         |
|[Upozornění protokolu aktivit](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Odešle oznámení, když žádné nové události v protokolu aktivit Azure odpovídá konkrétní podmínky. Například, když `Delete VM` probíhá operace **myProductionResourceGroup** nebo při vytvoření nové události služby Azure Service Health s **aktivní** stav se zobrazí.| [Schéma datové části upozornění protokolu aktivit](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)        |
|[Téměř v reálném čase upozornění metriky](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Rychlejší než upozornění na metriku odešle oznámení, když jeden nebo více metriky na úrovni platformy splní zadané podmínky. Například, když hodnota **využití procesoru %** na virtuálním počítači je větší než **90**a hodnota pro **sítě v** je větší než **500 MB** dobu posledních 5 minut.| [Téměř v reálném čase upozornění metriky datové části schématu](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)          |

Vzhledem k tomu, že data, která je poskytována každý typ výstrahy se liší, je jinak zpracovává každý typ výstrahy. V další části se dozvíte, jak vytvořit sadu runbook pro zpracování různých typech výstrah.

## <a name="create-a-runbook-to-handle-alerts"></a>Vytvoření sady runbook ke zpracování upozornění

Použití služby Automation s výstrahami, budete potřebovat sadu runbook, která obsahuje logiku, která spravuje výstrah datovou část JSON, který je předán do runbooku. Následující ukázkový runbook musí být volána z Azure výstrahy.

Jak je popsáno v předchozí části, každý typ výstrahy má jiné schéma. Skript používá data webhooku v `WebhookData` vstupní parametr runbooku z upozornění. Skript potom vyhodnotí jako datovou část JSON k určení, které typ výstrahy byla použita.

Tento příklad používá výstrahu z virtuálního počítače. Načte data virtuálního počítače z datové části a tuto informaci následně používá k zastavení virtuálního počítače. Připojení musí být nastavit v účtu Automation kde spuštění sady runbook. Při použití výstrahy k aktivaci sady runbook, je důležité zkontrolovat stav výstrahy v sadě runbook, který se aktivuje. Sada runbook spustí pokaždé, když výstraha změní stav. Výstrahy mají více stavů, dva nejběžnější stavy jsou `Activated` a `Resolved`. Zkontrolujte tento stav v logice sady runbook k zajištění, že vaše sada runbook neběží více než jednou. V příkladu v tomto článku ukazuje, jak hledat `Activated` pouze výstrahy.

Sada runbook používá **AzureRunAsConnection** [účet Spustit jako](automation-create-runas-account.md) k ověřování pomocí Azure k provedení akce správy pro virtuální počítač.

Použijte tento příklad k vytvoření sady runbook volá **Stop-AzureVmInResponsetoVMAlert**. Můžete upravit skript Powershellu a jeho použití s mnoha různými prostředky.

1. Přejděte ke svému účtu Azure Automation.
1. V části **AUTOMATIZACE PROCESŮ**vyberte **sady Runbook**.
1. V horní části seznamu sad runbook, vyberte **přidat runbook**. 
1. Na stránce **Přidat runbook** vyberte **Rychlé vytvoření**.
1. Název sady runbook, zadejte **Stop-AzureVmInResponsetoVMAlert**. Typ runbooku, vyberte **Powershellu**. Potom vyberte **Create** (Vytvořit).  
1. Zkopírujte následující příklad Powershellu do **upravit** podokně. 

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
                Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
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
1. Vyberte **publikovat** uložte a publikujte runbook.

## <a name="create-an-action-group"></a>Vytvořit skupinu akcí

Skupiny akcí je kolekce akce, které jsou aktivované výstrahy. Sady Runbook jsou pouze jedna z mnoha akce, které můžete použít skupiny akcí.

1. Na webu Azure Portal, vyberte **monitorování** > **nastavení** > **skupiny akcí**.
1. Vyberte **přidat skupinu akcí**a potom zadejte požadované informace:  
    1. V **název skupiny akcí** pole, zadejte název.
    1. V **krátký název** pole, zadejte název. Krátký název se používá namísto názvu skupiny úplné akce odeslání oznámení pomocí tato skupina akcí.
    1. **Předplatné** pole se automaticky vyplní s vaším aktuálním předplatným. Toto je předplatné, ve kterém se uloží skupiny akcí.
    1. Vyberte skupinu prostředků, ve kterém se uloží skupiny akcí.

V tomto příkladu vytvoříte dvě akce: akci sady runbook a akci oznámení.

### <a name="runbook-action"></a>Akci sady Runbook

Chcete-li vytvořit akci sady runbook do skupiny akcí:

1. V části **akce**, pro **název akce**, zadejte název této akce. Pro **typ akce**vyberte **sady Automation Runbook**.
1. V části **podrobnosti**vyberte **upravit podrobnosti**.  
1. Na **nakonfigurovat sadu Runbook** stránce v části **zdroj Runbooku**vyberte **uživatele**.  
1. Vyberte vaše **předplatné** a **účtu Automation**a pak vyberte **Stop-AzureVmInResponsetoVMAlert** sady runbook.  
1. Až budete hotovi, vyberte **OK**.

### <a name="notification-action"></a>Akce oznámení

Chcete-li vytvořit akci oznámení do skupiny akcí:

1. V části **akce**, pro **název akce**, zadejte název této akce. Pro **typ akce**vyberte **e-mailu**.  
1. V části **podrobnosti** vybrat, **upravit podrobnosti**.  
1. Na **e-mailu** stránky, zadejte e-mailovou adresu pro oznámení a pak vyberte **OK**. Jako akci přidání e-mailovou adresu kromě sady runbook je užitečné. Při spuštění runbooku, se zobrazí oznámení.  

    Vaše skupina akcí by měl vypadat jako na následujícím obrázku:

   ![Přidat stránku skupiny akcí](./media/automation-create-alert-triggered-runbook/add-action-group.png)
1. Chcete-li vytvořit skupinu akcí, vyberte **OK**.

Tato skupina akcí v můžete použít [upozornění protokolu aktivit](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) a [téměř v reálném čase dostávat](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal) , který vytvoříte.

## <a name="classic-alert"></a>Upozornění Classic

Upozornění Classic jsou na základě metrik a nepoužívejte skupin akcí. Však můžete nastavit runbook akce na základě klasického upozornění. 

Vytvoření klasického upozornění:

1. Vyberte **Přidat upozornění metriky**.
1. Pojmenujte upozornění metriky **myVMCPUAlert**. Zadejte stručný popis výstrahy.
1. Metriky vyskytl výstražný stav, vyberte **větší než**. Pro **prahová hodnota** hodnotu, vyberte **10**. Pro **období** hodnotu, vyberte **za posledních pět minut**.
1. V části **provést akci**vyberte **spuštění sady runbook z tohoto upozornění**.
1. Na **nakonfigurovat sadu Runbook** stránky, pro **zdroj Runbooku**vyberte **uživatele**. Vyberte svůj účet automation a pak vyberte **Stop-AzureVmInResponsetoVMAlert** sady runbook. Vyberte **OK**.
1. Chcete-li uložit pravidlo upozornění, vyberte **OK**.

## <a name="next-steps"></a>Další postup

* Další informace o spuštění runbooku Automation pomocí webhooku, najdete v části [spuštění runbooku z webhooku](automation-webhooks.md).
* Podrobnosti o různých způsobech spouštění sady runbook najdete v tématu [spuštění sady runbook](automation-starting-a-runbook.md).
* Další postup vytvoření upozornění protokolu aktivit najdete v tématu [vytvoření upozornění protokolu aktivit](../monitoring-and-diagnostics/monitoring-activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Zjistěte, jak vytvořit téměř v reálném čase výstrahy, najdete v článku [vytvořit pravidlo upozornění na webu Azure Portal](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md?toc=%2fazure%2fautomation%2ftoc.json#create-an-alert-rule-with-the-azure-portal).
