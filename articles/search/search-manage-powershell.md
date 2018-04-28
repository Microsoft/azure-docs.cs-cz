---
title: Správa Azure Search pomocí skriptů prostředí Powershell | Microsoft Docs
description: Správa služby Azure Search pomocí skriptů prostředí PowerShell. Vytvořit nebo aktualizovat službu Azure Search a spravovat klíče správce Azure Search
author: HeidiSteen
manager: cgronlun
tags: azure-resource-manager
services: search
ms.service: search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 08/15/2016
ms.author: heidist
ms.openlocfilehash: 974f2df8c4c9c9730f176a1e8472199960b359f6
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="manage-your-azure-search-service-with-powershell"></a>Správa služby Azure Search pomocí prostředí PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> 
> 

Toto téma popisuje příkazy prostředí PowerShell provádět mnoho úloh správy pro služby vyhledávání systému Azure. Jsme provede procesem vytváření vyhledávací službu, je škálování a správu jeho klíče rozhraní API.
Tyto příkazy paralelní možnostmi správy dostupnými v [REST API správy služby Azure Search](http://msdn.microsoft.com/library/dn832684.aspx).

## <a name="prerequisites"></a>Požadavky
* Pokud nemáte Azure PowerShell 1.0 nebo novější. Pokyny najdete v tématu [nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview).
* Musíte být přihlášení k předplatnému Azure v prostředí PowerShell, jak je popsáno níže.

Nejdřív musíte přihlášení k Azure pomocí tohoto příkazu:

    Connect-AzureRmAccount

Zadejte e-mailovou adresu účtu Azure a jeho heslo v dialogovém okně pro přihlášení Microsoft Azure.

Případně můžete [přihlášení interaktivně pomocí objektu služby](../azure-resource-manager/resource-group-authenticate-service-principal.md).

Pokud máte víc předplatných Azure, budete muset nastavit vašeho předplatného Azure. Chcete-li zobrazit seznam aktuální předplatných, spusťte tento příkaz.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Pro určení předplatného, spusťte následující příkaz. V následujícím příkladu je název odběru `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

## <a name="commands-to-help-you-get-started"></a>Příkazy, které vám pomůžou začít.
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
Je teď vytvořená služby, můžete provést další kroky: sestavení [index](search-what-is-an-index.md), [dotazovat index](search-query-overview.md)a nakonec vytvořit a spravovat vlastní vyhledávací aplikaci, která používá službu Azure Search.

* [Vytvoření indexu Azure Search na portálu Azure](search-create-index-portal.md)
* [Dotazování indexu Azure Search pomocí Průzkumník služby Search na portálu Azure](search-explorer.md)
* [Nastavte indexer načíst data z jiných služeb](search-indexer-overview.md)
* [Použití služby Azure Search v rozhraní .NET](search-howto-dotnet-sdk.md)
* [Analýza provozu Azure Search](search-traffic-analytics.md)

