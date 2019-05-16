---
title: Pomocí upozornění můžete aktivovat runbooku Azure Automation
description: Zjistěte, jak aktivovat sadu runbook spustit, když se vyvolá výstraha Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/29/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 8a1ae906a72d781f638fb171a409b860ffa6d501
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2019
ms.locfileid: "65517707"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Pomocí upozornění můžete aktivovat runbooku Azure Automation

Můžete použít [Azure Monitor](../azure-monitor/overview.md?toc=%2fazure%2fautomation%2ftoc.json) monitorovat základní metriky a protokoly pro většinu služby v Azure. Runbooky Azure Automation můžete volat pomocí [skupiny akcí](../azure-monitor/platform/action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) nebo pomocí klasických upozornění pro automatizaci úloh na základě výstrah. Tento článek popisuje, jak ke konfiguraci a spuštění sady runbook pomocí výstrah.

## <a name="alert-types"></a>Typy výstrah

Runbooky automation můžete používat tři typy výstrah:

* Výstrahy
* Upozornění protokolu aktivit
* Téměř v reálném čase upozornění na metriku

> [!NOTE]
> Ve společném schématu produktu výstrah standardizuje možnosti spotřeby oznámení výstrah v Azure ještě dnes. Tři typy výstrah v Azure ještě dnes (metrik, protokolů a protokolu aktivity) v minulosti, měli vlastní e-mailové šablony, schémata webhooku, atd. Další informace najdete v tématu [společné schéma produktu výstrah](../azure-monitor/platform/alerts-common-schema.md)

Pokud upozornění volá runbook, je vlastní volání požadavek HTTP POST webhooku. Text požadavku POST obsahuje nesprávný formát JSON objektu, který má užitečným vlastnostem, které se vztahují na upozornění. V následující tabulce jsou uvedeny odkazy na schéma datové části pro každý typ výstrahy:

|Upozornění  |Popis|Datová část schématu  |
|---------|---------|---------|
|[Běžné upozornění](../azure-monitor/platform/alerts-common-schema.md?toc=%2fazure%2fautomation%2ftoc.json)|Běžné výstrah schématu, který standardizuje možnosti spotřeby oznámení výstrah v Azure ještě dnes.|[Společné schéma datová část oznámení](../azure-monitor/platform/alerts-common-schema-definitions.md?toc=%2fazure%2fautomation%2ftoc.json#sample-alert-payload)|
|[Upozornění protokolu aktivit](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Odešle oznámení, když žádné nové události v protokolu aktivit Azure odpovídá konkrétní podmínky. Například, když `Delete VM` probíhá operace **myProductionResourceGroup** nebo při vytvoření nové události služby Azure Service Health s **aktivní** stav se zobrazí.| [Schéma datové části upozornění protokolu aktivit](../azure-monitor/platform/activity-log-alerts-webhook.md)        |
|[Téměř v reálném čase upozornění metriky](../azure-monitor/platform/alerts-metric-near-real-time.md?toc=%2fazure%2fautomation%2ftoc.json)    |Rychlejší než upozornění na metriku odešle oznámení, když jeden nebo více metriky na úrovni platformy splní zadané podmínky. Například, když hodnota **využití procesoru %** na virtuálním počítači je větší než **90**a hodnota pro **sítě v** je větší než **500 MB** dobu posledních 5 minut.| [Téměř v reálném čase upozornění metriky datové části schématu](../azure-monitor/platform/alerts-webhooks.md#payload-schema)          |

Vzhledem k tomu, že data, která je poskytována každý typ výstrahy se liší, je jinak zpracovává každý typ výstrahy. V další části se dozvíte, jak vytvořit sadu runbook pro zpracování různých typech výstrah.

## <a name="create-a-runbook-to-handle-alerts"></a>Vytvoření sady runbook ke zpracování upozornění

Použití služby Automation s výstrahami, budete potřebovat sadu runbook, která obsahuje logiku, která spravuje výstrah datovou část JSON, který je předán do runbooku. Následující ukázkový runbook musí být volána z Azure výstrahy.

Jak je popsáno v předchozí části, každý typ výstrahy má jiné schéma. Skript používá data webhooku v `WebhookData` vstupní parametr runbooku z upozornění. Skript potom vyhodnotí jako datovou část JSON k určení, které typ výstrahy byla použita.

Tento příklad používá výstrahu z virtuálního počítače. Načte data virtuálního počítače z datové části a tuto informaci následně používá k zastavení virtuálního počítače. Připojení musí být nastavit v účtu Automation kde spuštění sady runbook. Při použití výstrahy k aktivaci sady runbook, je důležité zkontrolovat stav výstrahy v sadě runbook, který se aktivuje. Sada runbook spustí pokaždé, když výstraha změní stav. Výstrahy mají více stavů, dva nejběžnější stavy jsou `Activated` a `Resolved`. Zkontrolujte tento stav v logice sady runbook k zajištění, že vaše sada runbook neběží více než jednou. V příkladu v tomto článku ukazuje, jak hledat `Activated` pouze výstrahy.

Sada runbook používá **AzureRunAsConnection** [účet Spustit jako](automation-create-runas-account.md) k ověřování pomocí Azure k provedení akce správy pro virtuální počítač.

Použijte tento příklad k vytvoření sady runbook volá **Stop-AzureVmInResponsetoVMAlert**. Můžete upravit skript Powershellu a jeho použití s mnoha různými prostředky.

1. Přejděte ke svému účtu Azure Automation.
2. V části **automatizace procesů**vyberte **sady Runbook**.
3. V horní části seznamu sad runbook, vyberte **+ vytvořit runbook**.
4. Na **přidat Runbook** zadejte **Stop-AzureVmInResponsetoVMAlert** pro název sady runbook. Typ runbooku, vyberte **Powershellu**. Potom vyberte **Create** (Vytvořit).  
5. Zkopírujte následující příklad Powershellu do **upravit** stránky.

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

6. Vyberte **publikovat** uložte a publikujte runbook.

## <a name="create-the-alert"></a>Vytvořit upozornění

Upozornění použití skupin akcí, což jsou kolekce akce, které jsou aktivované výstrahy. Sady Runbook jsou pouze jedna z mnoha akce, které můžete použít skupiny akcí.

1. Ve vašem účtu Automation vyberte **výstrahy** pod **monitorování**.
1. Vyberte **+ nové pravidlo upozornění**.
1. Klikněte na tlačítko **vyberte** pod **prostředků**. Na **vyberte prostředek** stránky, vyberte svůj virtuální počítač k upozornění na vypnutí a klikněte na tlačítko **provádí**.
1. Klikněte na tlačítko **přidat podmínku** pod **podmínku**. Vyberte signál, který chcete použít, například **využití CPU** a klikněte na tlačítko **provádí**.
1. Na **konfigurovat logiku signálů** stránky, zadejte vaše **prahová hodnota** pod **upozornění logiky**a klikněte na tlačítko **provádí**.
1. V části **skupiny akcí**vyberte **vytvořit nový**.
1. Na **přidat skupinu akcí** stránce, přidělte skupině Akce, názvu a krátký název.
1. Zadejte název akce. Typ akce vyberte **sady Automation Runbook**.
1. Vyberte **upravit podrobnosti**. Na **nakonfigurovat sadu Runbook** stránce v části **zdroj Runbooku**vyberte **uživatele**.  
1. Vyberte vaše **předplatné** a **účtu Automation**a pak vyberte **Stop-AzureVmInResponsetoVMAlert** sady runbook.  
1. Vyberte **Ano** pro **povolit ve společném schématu produktu výstrah**.
1. Chcete-li vytvořit skupinu akcí, vyberte **OK**.

    ![Přidat stránku skupiny akcí](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    Tato skupina akcí v můžete použít [upozornění protokolu aktivit](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) a [téměř v reálném čase dostávat](../azure-monitor/platform/alerts-overview.md?toc=%2fazure%2fautomation%2ftoc.json) , který vytvoříte.

1. V části **podrobnosti výstrahy**, přidejte název pravidla upozornění a popis a klikněte na tlačítko **vytvořit pravidlo upozornění**.

## <a name="next-steps"></a>Další postup

* Další informace o spuštění runbooku Automation pomocí webhooku, najdete v části [spuštění runbooku z webhooku](automation-webhooks.md).
* Podrobnosti o různých způsobech spouštění sady runbook najdete v tématu [spuštění sady runbook](automation-starting-a-runbook.md).
* Další postup vytvoření upozornění protokolu aktivit najdete v tématu [vytvoření upozornění protokolu aktivit](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Zjistěte, jak vytvořit téměř v reálném čase výstrahy, najdete v článku [vytvořit pravidlo upozornění na webu Azure Portal](../azure-monitor/platform/alerts-metric.md?toc=/azure/azure-monitor/toc.json).
