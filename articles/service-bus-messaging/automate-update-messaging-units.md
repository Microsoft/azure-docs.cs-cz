---
title: Azure Service Bus – automaticky aktualizovat jednotky zasílání zpráv
description: V tomto článku se dozvíte, jak můžete Azure Automation sadu Runbook použít k automatické aktualizaci jednotek zasílání zpráv Service Bus oboru názvů.
services: service-bus-messaging
ms.service: service-bus-messaging
documentationcenter: ''
author: spelluru
ms.topic: how-to
ms.date: 05/14/2020
ms.author: spelluru
ms.openlocfilehash: 5ece7beaea709c9b1e52cf2130484663da0aa4ac
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664547"
---
# <a name="automatically-update-messaging-units-of-an-azure-service-bus-namespace"></a>Automaticky aktualizovat jednotky zasílání zpráv Azure Service Bus oboru názvů 
V tomto článku se dozvíte, jak můžete automaticky aktualizovat [jednotky zasílání zpráv](service-bus-premium-messaging.md) Service Bus oboru názvů na základě využití prostředků (CPU nebo paměti). 

V příkladu v tomto článku se dozvíte, jak můžete zvýšit počet jednotek zasílání zpráv pro obor názvů Service Bus, když využití CPU v oboru názvů překročí 75%. Postup vysoké úrovně je následující:

1. Vytvořte Azure Automation sadu Runbook pomocí skriptu prostředí PowerShell, který škáluje (zvyšuje) počet jednotek zasílání zpráv pro Service Bus obor názvů. 
2. Vytvořte výstrahu o využití procesoru v oboru názvů Service Bus, který vyvolá skript prostředí PowerShell v případě, že využití CPU oboru názvů překročí 75%. 

> [!IMPORTANT]
> Tento článek se týká jenom úrovně **premium** Azure Service Bus. 


## <a name="create-a-service-bus-namespace"></a>Vytvoření oboru názvů Service Bus
Vytvořte Service Bus obor názvů úrovně Premier. Pokud chcete vytvořit obor názvů, postupujte podle kroků v tématu [Vytvoření oboru názvů v Azure Portalovém](service-bus-quickstart-portal.md#create-a-namespace-in-the-azure-portal) článku. 

## <a name="create-an-azure-automation-account"></a>Vytvoření účtu Azure Automation
Vytvořte účet Azure Automation podle pokynů uvedených v článku [Vytvoření účtu Azure Automation](../automation/automation-quickstart-create-account.md) . 

## <a name="import-azservice-module-from-gallery"></a>Import AZ. Service Module z Galerie
Importujte `Az.Accounts` `Az.ServiceBus` moduly a z Galerie do účtu Automation. `Az.ServiceBus`Modul závisí na `Az.Accounts` modulu, proto musí být nainstalován jako první. 

Podrobné pokyny najdete v tématu [Import modulu z Galerie modulů](../automation/automation-runbook-gallery.md#import-a-module-from-the-module-gallery-with-the-azure-portal).

## <a name="create-and-publish-a-powershell-runbook"></a>Vytvoření a publikování Runbooku PowerShellu

1. Vytvořte PowerShellový Runbook podle pokynů v článku [Vytvoření powershellového Runbooku](../automation/automation-quickstart-create-runbook.md) . 

    Tady je ukázkový skript PowerShellu, který můžete použít ke zvýšení počtu jednotek zasílání zpráv pro obor názvů Service Bus. Tento skript PowerShellu v Runbooku Automation zvyšuje MUs z 1 na 2, 2 na 4 nebo 4 až 8. Povolené hodnoty pro tuto vlastnost jsou: 1, 2, 4 a 8. Můžete vytvořit další sadu Runbook, aby se snížily jednotky zasílání zpráv.

    Parametry **oboru názvů** a **resourceGroupName** slouží k testování skriptu odděleně od scénáře upozorňování. 
    
    Parametr **WebHookData** slouží k předání informací, jako je název skupiny prostředků, název prostředku atd. za běhu. 

    ```powershell
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData,
    
        [Parameter (Mandatory = $false)]
        [String] $namespaceName,
    
        [Parameter (Mandatory = $false)]
        [String] $resourceGroupName
    )
    
    
    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    
        # Get the alert schema ID
        $schemaId = $WebhookBody.schemaId

        # If it's a metric alert
        if ($schemaId -eq "AzureMonitorMetricAlert") {

            # Get the resource group name from the alert context
            $resourceGroupName = $AlertContext.resourceGroupName
            
            # Get the namespace name from the alert context
            $namespaceName = $AlertContext.resourceName
        }
    }
    
    # Connect to Azure account
    $connection = Get-AutomationConnection -Name AzureRunAsConnection
    
    while(!($connectionResult) -And ($logonAttempt -le 10))
    {
        $LogonAttempt++
        # Logging in to Azure...
        $connectionResult =    Connect-AzAccount `
                                    -ServicePrincipal `
                                    -Tenant $connection.TenantID `
                                    -ApplicationId $connection.ApplicationID `
                                    -CertificateThumbprint $connection.CertificateThumbprint
    
        Start-Sleep -Seconds 30
    }
    
    # Get the current capacity (number of messaging units) of the namespace
    $sbusns=Get-AzServiceBusNamespace `
        -Name $namespaceName `
        -ResourceGroupName $resourceGroupName
    
    $currentCapacity = $sbusns.Sku.Capacity
    
    # Increase the capacity
    # Capacity can be one of these values: 1, 2, 4, 8
    if ($currentCapacity -eq 1) {
        $newMU = 2
    }
    elseif ($currentCapacity -eq 2) {
        $newMU = 4
    }
    elseif ($currentCapacity -eq 4) {
        $newMU = 8    
    }
    else {
    
    }
    
    # Update the capacity of the namespace
    Set-AzServiceBusNamespace `
            -Location eastus `
            -SkuName Premium `
            -Name $namespaceName `
            -SkuCapacity $newMU `
            -ResourceGroupName $resourceGroupName

    ```
2. [Otestujte sešit](../automation/manage-runbooks.md#test-a-runbook) zadáním hodnot pro parametry **Namespace** a **resourceGroupName** . Potvrďte, že jsou jednotky zasílání zpráv v oboru názvů aktualizované. 
3. Po úspěšném otestování [sešit publikujte](..//automation/manage-runbooks.md#publish-a-runbook) , aby bylo možné později přidat jako akci pro výstrahu v oboru názvů. 

## <a name="create-an-alert-on-the-namespace-to-trigger-the-runbook"></a>Vytvoření upozornění na oboru názvů pro aktivaci Runbooku
Viz téma [použití výstrahy ke spuštění Azure Automation Runbooku](../automation/automation-create-alert-triggered-runbook.md) ke konfiguraci výstrahy pro Service Bus oboru názvů pro aktivaci sady Automation Runbook, kterou jste vytvořili. Můžete například vytvořit výstrahu o **využití procesoru na obor názvů** nebo **velikost paměti na metriku oboru názvů** a přidat akci, která spustí Runbook služby Automation, který jste vytvořili. Podrobnosti o těchto metrikách najdete v tématu [metriky využití prostředků](service-bus-metrics-azure-monitor.md#resource-usage-metrics).

Následující postup ukazuje, jak vytvořit výstrahu, která spustí Runbook služby Automation, když **využití CPU** oboru názvů překročí **75%**.

1. Na stránce **Service Bus obor** názvů pro váš obor názvů vyberte v nabídce vlevo možnost **výstrahy** a pak na panelu nástrojů vyberte **+ nové pravidlo výstrahy** . 
    
    ![Stránka výstrahy – tlačítko pro pravidlo nového upozornění](./media/automate-update-messaging-units/alerts-page.png)
2. Na stránce **vytvořit pravidlo výstrahy** klikněte na **vybrat podmínku**. 

    ![Stránka pro vytvoření pravidla výstrahy – výběr podmínky](./media/automate-update-messaging-units/alert-rule-select-condition.png) 
3. Na stránce **Konfigurovat logiku signálu** vyberte **procesor** pro signál. 

    ![Konfigurace logiky signálů – výběr procesoru](./media/automate-update-messaging-units/configure-signal-logic.png)
4. Zadejte **prahovou hodnotu** (v tomto příkladu je to **75%**) a vyberte **Hotovo**. 

    ![Konfigurace signálu procesoru](./media/automate-update-messaging-units/cpu-signal-configuration.png)
5. Nyní na **stránce vytvořit výstrahu**klikněte na **možnost vybrat skupinu akcí**.
    
    ![Vybrat skupinu akcí](./media/automate-update-messaging-units/select-action-group-button.png)
6. Na panelu nástrojů vyberte tlačítko **vytvořit skupinu akcí** . 

    ![Tlačítko pro vytvoření skupiny akcí](./media/automate-update-messaging-units/create-action-group-button.png)
7. Na stránce **Přidat skupinu akcí** proveďte následující kroky:
    1. Zadejte **název** skupiny akcí. 
    2. Zadejte **krátký název** skupiny akcí.
    3. Vyberte **předplatné** , ve kterém chcete vytvořit tuto skupinu akcí.
    4. Vyberte **skupinu prostředků**. 
    5. V části **Akce** zadejte **název akce**a jako **typ akce**vyberte **Automation Runbook** . 

        ![Přidat stránku skupiny akcí](./media/automate-update-messaging-units/add-action-group-page.png)
8. Na stránce **Konfigurace sady Runbook** proveďte následující kroky:
    1. V případě **zdroje sady Runbook**vyberte možnost **uživatel**. 
    2. V poli **předplatné**vyberte své **předplatné** Azure, které obsahuje účet Automation. 
    3. V případě **účtu Automation**vyberte svůj **účet Automation**.
    4. V případě **Runbooku**vyberte svou sadu Runbook. 
    5. Na stránce **Konfigurace sady Runbook** vyberte **OK** . 
        ![Konfigurace Runbooku](./media/automate-update-messaging-units/configure-runbook.png)
9. Na stránce **Přidat skupinu akcí** vyberte **OK** . 
5. Nyní na stránce **vytvořit pravidlo výstrahy** zadejte **název pravidla**a potom vyberte **vytvořit pravidlo upozornění**. 
    ![Vytvořit pravidlo výstrahy](./media/automate-update-messaging-units/create-alert-rule.png)

    > [!NOTE]
    > Když teď, když využití CPU oboru názvů překročí 75, výstraha aktivuje sadu Automation Runbook, která zvyšuje jednotky zasílání zpráv Service Bus oboru názvů. Podobně můžete vytvořit výstrahu pro jinou sadu Automation Runbook, která sníží počet jednotek zasílání zpráv, pokud využití CPU oboru názvů klesne pod 25. 

## <a name="next-steps"></a>Další kroky
Další informace o jednotkách zasílání zpráv najdete v tématu [zasílání zpráv](service-bus-premium-messaging.md) na úrovni Premium.