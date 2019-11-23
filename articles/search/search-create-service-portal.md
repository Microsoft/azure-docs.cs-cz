---
title: 'Quickstart: Create a search service in the portal'
titleSuffix: Azure Cognitive Search
description: In this portal quickstart, learn how to set up an Azure Cognitive Search resource in the Azure portal. Choose resource groups, regions, and SKU or pricing tier.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: 79554429ddc9516eb2fbe9aa7b0e75c78fb17662
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406679"
---
# <a name="quickstart-create-an-azure-cognitive-search-service-in-the-portal"></a>Quickstart: Create an Azure Cognitive Search service in the portal

Azure Cognitive Search is a standalone resource used to plug in a search experience in custom apps. Although Azure Cognitive Search integrates easily with other Azure services, you can also use it as a standalone component, or integrate it with apps on network servers, or with software running on other cloud platforms.

In this article, learn how to create a resource in the [Azure portal](https://portal.azure.com/).

[![Animated GIF](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

Dáváte přednost prostředí PowerShell? Použijte [šablonu služby](https://azure.microsoft.com/resources/templates/101-azure-search-create/) Azure Resource Manageru. For help with getting started, see [Manage Azure Cognitive Search with PowerShell](search-manage-powershell.md).

## <a name="subscribe-free-or-paid"></a>Předplatné (bezplatné nebo placené)

[Otevřete si bezplatný účet Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) a použijte bezplatné kredity k vyzkoušení placených služeb Azure. Až kredity vyčerpáte, můžete si účet ponechat a dál používat bezplatné služby Azure, jako například Azure Websites. Nikdy vám nebudeme účtovat žádné poplatky, pokud si sami nezměníte nastavení a nezačnete používat placené služby.

Případně si můžete [aktivovat výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Díky předplatnému MSDN každý měsíc získáváte kredity, které můžete použít pro placené služby Azure. 

## <a name="find-azure-cognitive-search"></a>Find Azure Cognitive Search

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).
2. Click the plus sign ("+ Create Resource") in the top-left corner.
3. Use the search bar to find "Azure Cognitive Search" or navigate to the resource through **Web** > **Azure Cognitive Search**.

![Create a resource in the portal](./media/search-create-service-portal/find-search3.png "Create a resource in the portal")

## <a name="choose-a-subscription"></a>Choose a subscription

Setting the subscription ID and resource group is your first step. Pokud máte více než jedno předplatné, vyberte to, jehož součástí jsou také služby úložiště dat nebo souborů. Azure Cognitive Search can autodetect Azure Table and Blob storage, SQL Database, and Azure Cosmos DB for indexing via [*indexers*](search-indexer-overview.md), but only for services under the same subscription.

## <a name="set-a-resource-group"></a>Set a resource group

A resource group is required and is useful for managing resources all-up, including costs. A resource group can consist of one service, or multiple services used together. For example, if you are using Azure Cognitive Search to index an Azure Cosmos DB database, you could make both services part of the same resource group for management purposes. 

If you aren't combining resources into a single group, or if existing resource groups are filled with resources used in unrelated solutions, create a new resource group just for your Azure Cognitive Search resource. 

![Create a new resource group](./media/search-create-service-portal/new-resource-group.png "Vytvoření nové skupiny prostředků")

Over time, you can track current and projected costs all-up (as shown in the screenshot) or scroll down to view charges for individual resources. The following screenshot shows the kind of cost information you can eventually expect to see when you combine multiple resources into one group.

![Manage costs at the resource group level](./media/search-create-service-portal/resource-group-cost-management.png "Manage costs at the resource group level")

> [!TIP]
> Resource groups simplify cleanup because deleting a group also deletes the services within it. U prototypových projektů, které využívají více služeb, spojení všech služeb do stejné skupiny prostředků usnadňuje vyčištění po skončení projektu.

## <a name="name-the-service"></a>Name the service

In Instance Details, provide a service name in the **URL** field. The name is part of the URL endpoint against which API calls are issued: `https://your-service-name.search.windows.net`. For example, if you want the endpoint to be `https://myservice.search.windows.net`, you would enter `myservice`.

Požadavky na název služby:

* Musí být jedinečný v rámci oboru názvů search.windows.net.
* Délka musí být 2 až 60 znaků.
* Používejte malá písmena, číslice nebo pomlčky („-“).
* Nepoužívejte pomlčku („-“) v prvních 2 znacích nebo jako poslední znak.
* Nikde nepoužívejte po sobě jdoucí pomlčky („--“).

> [!TIP]
> If you think you'll be using multiple services, we recommend including the region (or location) in the service name as a naming convention. Services within the same region can exchange data at no charge, so if Azure Cognitive Search is in West US, and you have other services also in West US, a name like `mysearchservice-westus` can save you a trip to the properties page when deciding how to combine or attach resources.

## <a name="choose-a-location"></a>Choose a location

As an Azure service, Azure Cognitive Search can be hosted in datacenters around the world. The list of supported regions can be found in the [pricing page](https://azure.microsoft.com/pricing/details/search/). 

You can minimize or avoid bandwidth charges by choosing the same location for multiple services. For example, if you are indexing data provided by another Azure service (Azure storage, Azure Cosmos DB, Azure SQL Database), creating your Azure Cognitive Search service in the same region avoids bandwidth charges (there are no charges for outbound data when services are in the same region).

Additionally, if you are using AI enrichment, create your service in the same region as Cognitive Services. *Co-location of Azure Cognitive Search and Cognitive Services in the same region is a requirement for AI enrichment*.

> [!Note]
> Central India is currently unavailable for new services. For services already in Central India, you can scale up with no restrictions, and your service is fully supported in that region. The restriction on this region is temporary and limited to new services only. We will remove this note when the restriction no longer applies.

## <a name="choose-a-pricing-tier-sku"></a>Choose a pricing tier (SKU)

[Azure Cognitive Search is currently offered in multiple pricing tiers](https://azure.microsoft.com/pricing/details/search/): Free, Basic, or Standard. Každá úroveň má svou vlastní [kapacitu a limity](search-limits-quotas-capacity.md). Další pokyny získáte v tématu věnovaném [volbě cenové úrovně nebo SKU](search-sku-tier.md).

Basic and Standard are the most common choices for production workloads, but most customers start with the Free service. Key differences among tiers is partition size and speed, and limits on the number of objects you can create.

Remember that a pricing tier cannot be changed once the service is created. Pokud budete později potřebovat vyšší nebo nižší úroveň, budete muset službu znovu vytvořit.

## <a name="create-your-service"></a>Vytvoření služby

After you've provided the necessary inputs, go ahead and create the service. 

![Review and create the service](./media/search-create-service-portal/new-service3.png "Review and create the service")

Your service is deployed within minutes, which you can monitor through Azure notifications. Consider pinning the service to your dashboard for easy access in the future.

![Monitor and pin the service](./media/search-create-service-portal/monitor-notifications.png "Monitor and pin the service")

## <a name="get-a-key-and-url-endpoint"></a>Get a key and URL endpoint

Unless you are using the portal, programmatic access to your new service requires that you provide the URL endpoint and an authentication api-key.

1. On the **Overview** page, locate and copy the URL endpoint on the right side of the page.

2. On the **Keys** page, copy either one of the admin keys (they are equivalent). Admin api-keys are required for creating, updating, and deleting objects on your service. In contrast, query keys provide read-access to index content.

   ![Service overview page with URL endpoint](./media/search-create-service-portal/get-url-key.png "URL endpoint and other service details")

An endpoint and key are not needed for portal-based tasks. The portal is already linked to your Azure Cognitive Search resource with admin rights. For a portal walkthrough, start with [Quickstart: Create an Azure Cognitive Search index in the portal](search-get-started-portal.md).

## <a name="scale-your-service"></a>Škálování služby

Po zřízení můžete službu škálovat tak, aby vyhovovala vašim potřebám. If you chose the Standard tier for your Azure Cognitive Search service, you can scale your service in two dimensions: replicas and partitions. Pokud byste zvolili úroveň Basic, mohli byste přidávat pouze repliky. Při zřízení bezplatné služby škálování není k dispozici.

***Oddíly***: Umožňují službě ukládat a prohledávat více dokumentů.

***Repliky***: Umožňují službě zpracovat větší množství vyhledávacích dotazů.

Přidáním prostředků se zvýší vaše měsíční náklady. [Cenová kalkulačka](https://azure.microsoft.com/pricing/calculator/) vám pomůže porozumět důsledkům přidání prostředků na fakturaci. Mějte na paměti, že prostředky můžete upravit na základě zatížení. Například můžete navýšit prostředky kvůli vytvoření úplného počátečního indexu a později je pak snížit na úroveň vhodnější pro přírůstkové indexování.

> [!Important]
> Služba musí mít [2 repliky pro smlouvu SLA jen pro čtení a 3 repliky pro smlouvu SLA se čtením a zápisem](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Přejděte na stránku vyhledávací služby na webu Azure Portal.
2. V levém navigačním podokně vyberte **Nastavení** > **Škálování**.
3. Přetažením posuvníku můžete přidat prostředky obou typů.

![Add capacity](./media/search-create-service-portal/settings-scale.png "Add capacity through replicas and partitions")

> [!Note]
> Per-partition storage and speed increases at higher tiers. For more information, see [capacity and limits](search-limits-quotas-capacity.md).

## <a name="when-to-add-a-second-service"></a>Přidání druhé služby

Most customers use just one service provisioned at a tier providing the [right balance of resources](search-sku-tier.md). Jedna služba může hostovat více indexů v souladu s [maximálními limity vámi vybrané úrovně](search-capacity-planning.md), přičemž každý index je izolovaný od ostatních indexů. In Azure Cognitive Search, requests can only be directed to one index, minimizing the chance of accidental or intentional data retrieval from other indexes in the same service.

I když většina zákazníků používá jenom jednu službu, redundance služby může být nutná v případě následujících provozních požadavků:

* Zotavení po havárii (výpadek datového centra). Azure Cognitive Search does not provide instant failover in the event of an outage. Doporučení a pokyny najdete v tématu [Správa služby](search-manage.md).
* Z vašeho zkoumání modelování více tenantů vyplynulo, že využívání dalších služeb je optimální řešení. Další informace najdete v tématu [Návrh pro více tenantů](search-modeling-multitenant-saas-applications.md).
* For globally deployed applications, you might require an instance of Azure Cognitive Search in multiple regions to minimize latency of your application’s international traffic.

> [!NOTE]
> In Azure Cognitive Search, you cannot segregate indexing and querying operations; thus, you would never create multiple services for segregated workloads. Index je vždy dotazován ve službě, ve které byl vytvořen (nemůžete vytvořit index v jedné službě a zkopírovat ho do jiné).

Druhá služba není potřebná pro zajištění vysoké dostupnosti. Vysoká dostupnost pro dotazy se dosáhne, když použijete 2 nebo více replik v rámci stejné služby. Replica updates are sequential, which means at least one is operational when a service update is rolled out. For more information about uptime, see [Service Level Agreements](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Další kroky

After provisioning a service, you can continue in the portal to create your first index.

> [!div class="nextstepaction"]
> [Quickstart: Create an Azure Cognitive Search index in the portal](search-get-started-portal.md)
