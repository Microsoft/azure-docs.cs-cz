---
title: Správa služby Azure Search pomocí skriptů prostředí Powershell – Azure Search
description: Správa služby Azure Search pomocí skriptů prostředí PowerShell. Vytvořit nebo aktualizovat službu Azure Search a správě klíče správce Azure Search
author: HeidiSteen
manager: cgronlun
tags: azure-resource-manager
services: search
ms.service: search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 08/15/2016
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: c05a2ceb7cc515691af91652c968b73c72029db4
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53313458"
---
# <a name="manage-your-azure-search-service-with-powershell"></a>Správa služby Azure Search pomocí Powershellu
> [!div class="op_single_selector"]
> * [Azure Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> 
> 

Toto téma popisuje příkazy prostředí PowerShell provádět mnoho úloh správy pro služby Azure Search. Projdeme vytvoření vyhledávací službu, je škálování a správu jeho klíče rozhraní API.
Tyto příkazy paralelní možnostmi správy dostupnými v [Management REST API služby Azure Search](https://docs.microsoft.com/rest/api/searchmanagement).

## <a name="prerequisites"></a>Požadavky
* Musíte mít Azure PowerShell 1.0 nebo vyšší. Pokyny najdete v tématu [nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview).
* Musíte být přihlášeni ke svému předplatnému Azure v prostředí PowerShell, jak je popsáno níže.

Nejprve je nutné přihlášení k Azure pomocí tohoto příkazu:

    Connect-AzureRmAccount

Zadejte e-mailovou adresu svého účtu Azure a jeho heslo v dialogovém okně pro přihlášení Microsoft Azure.

Případně můžete [přihlásit se interaktivně pomocí hlavního názvu služby](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

Pokud máte více předplatných Azure, musíte nastavit vašeho předplatného Azure. Chcete-li zobrazit seznam aktuálních předplatných. Spusťte tento příkaz.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Pokud chcete zadat předplatné, spusťte následující příkaz. V následujícím příkladu je název předplatného `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

## <a name="commands-to-help-you-get-started"></a>Příkazy, které vám pomůžou začít pracovat.
    $serviceName = "your-service-name-lowercase-with-dashes"
    $sku = "free" # or "basic" or "standard" for paid services
    $location = "West US"
    # You can get a list of potential locations with
    # (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Search'}).Locations
    $resourceGroupName = "YourResourceGroup" 
    # If you don't already have this resource group, you can create it with 
    # New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Register the ARM provider idempotently. This must be done once per subscription
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Search"

    # Create a new search service
    # This command will return once the service is fully created
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateUri "https://gallery.azure.com/artifact/20151001/Microsoft.Search.1.0.9/DeploymentTemplates/searchServiceDefaultTemplate.json" `
        -NameFromTemplate $serviceName `
        -Sku $sku `
        -Location $location `
        -PartitionCount 1 `
        -ReplicaCount 1

    # Get information about your new service and store it in $resource
    $resource = Get-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19

    # View your resource
    $resource

    # Get the primary admin API key
    $primaryKey = (Invoke-AzureRmResourceAction `
        -Action listAdminKeys `
        -ResourceId $resource.ResourceId `
        -ApiVersion 2015-08-19).PrimaryKey

    # Regenerate the secondary admin API Key
    $secondaryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/regenerateAdminKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action secondary).SecondaryKey

    # Create a query key for read only access to your indexes
    $queryKeyDescription = "query-key-created-from-powershell"
    $queryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/createQueryKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action $queryKeyDescription).Key

    # View your query key
    $queryKey

    # Delete query key
    Remove-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices/deleteQueryKey/$($queryKey)" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19

    # Scale your service up
    # Note that this will only work if you made a non "free" service
    # This command will not return until the operation is finished
    # It can take 15 minutes or more to provision the additional resources
    $resource.Properties.ReplicaCount = 2
    $resource | Set-AzureRmResource

    # Delete your service
    # Deleting your service will delete all indexes and data in the service
    $resource | Remove-AzureRmResource

## <a name="next-steps"></a>Další kroky
Teď, když je vaše služba vytvořena, je provést další kroky: sestavení [index](search-what-is-an-index.md), [dotazování indexu](search-query-overview.md)a nakonec vytvářet a spravovat vlastní vyhledávací aplikaci, která používá službu Azure Search.

* [Vytvoření indexu Azure Search na webu Azure Portal](search-create-index-portal.md)
* [Dotazování indexu Azure Search pomocí Průzkumníka služby Search na webu Azure Portal](search-explorer.md)
* [Nastavte indexer k načtení dat z jiné služby](search-indexer-overview.md)
* [Použití služby Azure Search v rozhraní .NET](search-howto-dotnet-sdk.md)
* [Analýza provozu Azure Search](search-traffic-analytics.md)

