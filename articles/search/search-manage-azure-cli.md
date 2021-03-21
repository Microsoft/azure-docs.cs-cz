---
title: Skripty Azure CLI pomocí modulu příkaz AZ Search
titleSuffix: Azure Cognitive Search
description: Vytvořte a nakonfigurujte službu Azure Kognitivní hledání pomocí rozhraní příkazového řádku Azure CLI. Službu můžete škálovat směrem nahoru nebo dolů, spravovat správce a dotazovat klíče rozhraní API a dotazovat se na systémové informace.
manager: luisca
author: DerekLegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: ee6b0e1b745e86c72843af88c0f6d17f91512e15
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176752"
---
# <a name="manage-your-azure-cognitive-search-service-with-the-azure-cli"></a>Správa služby Azure Kognitivní hledání pomocí Azure CLI
> [!div class="op_single_selector"]
> * [Azure Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [Azure CLI](search-manage-azure-cli.md)
> * [REST API](/rest/api/searchmanagement/)
> * [.NET SDK](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)

Příkazy a skripty rozhraní příkazového řádku Azure můžete spustit v systému Windows, macOS, Linux nebo v [Azure Cloud Shell](../cloud-shell/overview.md) a vytvořit a nakonfigurovat Azure kognitivní hledání. Modul [**AZ Search**](/cli/azure/search) rozšiřuje rozhraní příkazového [řádku Azure](/cli/) s plnou paritou na [rozhraní REST API pro správu vyhledávání](/rest/api/searchmanagement) a možnost provádět následující úlohy:

> [!div class="checklist"]
> * [Seznam služeb vyhledávání v předplatném](#list-search-services)
> * [Vrátit informace o službě](#get-search-service-information)
> * [Vytvoření nebo odstranění služby](#create-or-delete-a-service)
> * [Vytvoření služby s privátním koncovým bodem](#create-a-service-with-a-private-endpoint)
> * [Znovu vygenerovat klíče rozhraní API pro správu](#regenerate-admin-keys)
> * [Vytvoření nebo odstranění rozhraní API pro dotazování klíčů](#create-or-delete-query-keys)
> * [Horizontální navýšení nebo snížení kapacity díky replikám a oddílům](#scale-replicas-and-partitions)
> * [Vytvoření sdíleného prostředku privátního propojení](#create-a-shared-private-link-resource)

V některých případech se otázky týkají úkolů, které *nejsou* uvedené na seznamu výše. V současné době nelze použít buď modul **hledání AZ** , nebo REST API správy ke změně názvu serveru, oblasti nebo úrovně. Vyhrazené prostředky jsou přiděleny při vytvoření služby. Například změna základního hardwaru (umístění nebo typu uzlu) vyžaduje novou službu. Podobně nejsou k dispozici žádné nástroje ani rozhraní API pro přenos obsahu, jako je například index, z jedné služby do jiné.

V rámci služby probíhá vytváření a Správa obsahu prostřednictvím [Search Service REST API](/rest/api/searchservice/) nebo [.NET SDK](/dotnet/api/overview/azure/search.documents-readme). I když pro obsah nejsou k dispozici žádné vyhrazené příkazy prostředí PowerShell, můžete psát skripty, které volají rozhraní REST API nebo rozhraní .NET API k vytváření a načítání indexů.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Seznam služeb v předplatném

Následující příkazy vycházejí z příkazu [**AZ Resource**](/cli/azure/resource)a vracejí informace o stávajících prostředcích a službách, které jsou už ve vašem předplatném zřízené. Pokud si nejste jisti, kolik služeb vyhledávání již bylo vytvořeno, tyto příkazy tyto příkazy vrátí, a tím ušetříte cestu k portálu.

První příkaz vrátí všechny vyhledávací služby.

```azurecli-interactive
az resource list --resource-type Microsoft.Search/searchServices --output table
```

V seznamu služeb vraťte informace o konkrétním prostředku.

```azurecli-interactive
az resource list --name <service-name>
```

## <a name="list-all-az-search-commands"></a>Vypíše všechny příkazy AZ Search.

Můžete zobrazit informace o podskupinách a příkazech, které jsou k dispozici v příkazu [**AZ Search**](/cli/azure/search) from v rámci rozhraní příkazového řádku. Případně si můžete projít [dokumentaci](/cli/azure/search).

Pokud chcete zobrazit podskupiny dostupné v rámci `az search` , spusťte následující příkaz.

```azurecli-interactive
az search --help
```

Odpověď by měla vypadat podobně jako v následujícím výstupu.

```
Group
    az search : Manage Azure Search services, admin keys and query keys.
        WARNING: This command group is in preview and under development. Reference and support
        levels: https://aka.ms/CLI_refstatus
Subgroups:
    admin-key                    : Manage Azure Search admin keys.
    private-endpoint-connection  : Manage Azure Search private endpoint connections.
    private-link-resource        : Manage Azure Search private link resources.
    query-key                    : Manage Azure Search query keys.
    service                      : Manage Azure Search services.
    shared-private-link-resource : Manage Azure Search shared private link resources.

For more specific examples, use: az find "az search"
```

V rámci každé podskupiny jsou k dispozici více příkazů. Příkazy k dispozici pro `service` podskupinu můžete zobrazit spuštěním následujícího řádku.

```azurecli-interactive
az search service --help
```

Můžete si také prohlédnout argumenty, které jsou k dispozici pro konkrétní příkaz.

```azurecli-interactive
az search service create --help
```

## <a name="get-search-service-information"></a>Získat informace o službě Search

Pokud znáte skupinu prostředků obsahující vaši vyhledávací službu, spusťte příkaz [**AZ Search Service show**](/cli/azure/search/service#az_search_service_show) a vraťte definici služby, včetně názvu, oblasti, úrovně a počtu replik a oddílů.

```azurecli-interactive
az search service show --name <service-name> --resource-group <resource-group-name>
```

## <a name="create-or-delete-a-service"></a>Vytvoření nebo odstranění služby

Chcete-li [vytvořit novou vyhledávací službu](search-create-service-portal.md), použijte příkaz [**AZ Search Service Create**](/cli/azure/search/service#az_search_service_show) .

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1
``` 

Výsledky by měly vypadat podobně jako následující výstup:

```
{
  "hostingMode": "default",
  "id": "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp",
  "identity": null,
  "location": "West US",
  "name": "my-demo-searchapp",
  "networkRuleSet": {
    "bypass": "None",
    "ipRules": []
  },
  "partitionCount": 1,
  "privateEndpointConnections": [],
  "provisioningState": "succeeded",
  "publicNetworkAccess": "Enabled",
  "replicaCount": 1,
  "resourceGroup": "demo-westus",
  "sharedPrivateLinkResources": [],
  "sku": {
    "name": "standard"
  },
  "status": "running",
  "statusDetails": "",
  "tags": null,
  "type": "Microsoft.Search/searchServices"
}
```

### <a name="create-a-service-with-ip-rules"></a>Vytvoření služby s pravidly IP

V závislosti na vašich požadavcích na zabezpečení můžete chtít vytvořit vyhledávací službu s [nakonfigurovanou bránou firewall protokolu IP](service-configure-firewall.md). Provedete to tak, že předáte adresy veřejných IP (v4) nebo rozsahy CIDR do `ip-rules` argumentu, jak je uvedeno níže. Pravidla by měla být oddělená čárkou ( `,` ) nebo středníkem ( `;` ).

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --ip-rules "55.5.63.73;52.228.215.197;101.37.221.205"
```

### <a name="create-a-service-with-a-system-assigned-managed-identity"></a>Vytvoření služby se spravovanou identitou přiřazenou systémem

V některých případech, například při [použití spravované identity pro připojení ke zdroji dat](search-howto-managed-identities-storage.md), budete muset zapnout [spravovanou identitu přiřazenou systémem](../active-directory/managed-identities-azure-resources/overview.md). To se provádí přidáním `--identity-type SystemAssigned` do příkazu.

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --identity-type SystemAssigned
```

## <a name="create-a-service-with-a-private-endpoint"></a>Vytvoření služby s privátním koncovým bodem

[Privátní koncové body](../private-link/private-endpoint-overview.md) pro Azure kognitivní hledání umožňují klientovi ve virtuální síti zabezpečený přístup k datům v indexu vyhledávání prostřednictvím [privátního odkazu](../private-link/private-link-overview.md). Privátní koncový bod používá IP adresu z [adresního prostoru virtuální sítě](../virtual-network/private-ip-addresses.md) pro vaši vyhledávací službu. Síťový provoz mezi klientem a vyhledávací službou prochází přes virtuální síť a privátní odkaz na páteřní síti Microsoftu, což eliminuje expozici veřejného Internetu. Další podrobnosti najdete v dokumentaci k [Vytvoření privátního koncového bodu pro Azure kognitivní hledání](service-create-private-endpoint.md)

Následující příklad ukazuje, jak vytvořit vyhledávací službu s privátním koncovým bodem.

Nejdřív nasaďte vyhledávací službu s `PublicNetworkAccess` nastavením na `Disabled` .

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --public-access Disabled
```

Dále vytvořte virtuální síť a soukromý koncový bod.

```azurecli-interactive
# Create the virtual network
az network vnet create \
    --resource-group <resource-group-name> \
    --location "West US" \
    --name <virtual-network-name> \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name <subnet-name> \
    --subnet-prefixes 10.1.0.0/24

# Update the subnet to disable private endpoint network policies
az network vnet subnet update \
    --name <subnet-name> \
    --resource-group <resource-group-name> \
    --vnet-name <virtual-network-name> \
    --disable-private-endpoint-network-policies true

# Get the id of the search service
id=$(az search service show \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --query [id] \
    --output tsv)

# Create the private endpoint
az network private-endpoint create \
    --name <private-endpoint-name> \
    --resource-group <resource-group-name> \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --private-connection-resource-id $id \
    --group-id searchService \
    --connection-name <private-link-connection-name>  
```

Nakonec vytvořte privátní zónu DNS. 

```azurecli-interactive
## Create private DNS zone
az network private-dns zone create \
    --resource-group <resource-group-name> \
    --name "privatelink.search.windows.net"

## Create DNS network link
az network private-dns link vnet create \
    --resource-group <resource-group-name> \
    --zone-name "privatelink.search.windows.net" \
    --name "myLink" \
    --virtual-network <virtual-network-name> \
    --registration-enabled false

## Create DNS zone group
az network private-endpoint dns-zone-group create \
   --resource-group <resource-group-name>\
   --endpoint-name <private-endpoint-name> \
   --name "myZoneGroup" \
   --private-dns-zone "privatelink.search.windows.net" \
   --zone-name "searchServiceZone"
```

Další informace o vytváření privátních koncových bodů v PowerShellu najdete v tomto [rychlém startu privátního propojení](https://docs.microsoft.com/azure/private-link/create-private-endpoint-cli) .

### <a name="manage-private-endpoint-connections"></a>Správa připojení privátního koncového bodu

Kromě vytvoření připojení privátního koncového bodu můžete také `show` , `update` a `delete` připojení.

Chcete-li načíst připojení privátního koncového bodu a zobrazit jeho stav, použijte příkaz [**AZ Search Private-Endpoint-Connection show**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_show).

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

Pokud chcete připojení aktualizovat, použijte příkaz [**AZ Search Search Private-Endpoint-Connection Update**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_update). Následující příklad nastaví připojení privátního koncového bodu na odmítnuto:

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
    --status Rejected \
    --description "Rejected" \
    --actions-required "Please fix XYZ"
```

Pokud chcete odstranit připojení privátního koncového bodu, použijte [**AZ Search Private-Endpoint-Connection Delete**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_delete).

```azurecli-interactive
az search private-endpoint-connection delete \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

## <a name="regenerate-admin-keys"></a>Znovu vygenerovat klíče správce

Pokud chcete převzít [klíče rozhraní API](search-security-api-keys.md)pro správu, použijte příkaz [**AZ Search admin-Key Renew**](/cli/azure/search/admin-key#az_search_admin_key_renew). Dva klíče správce se vytvoří s každou službou pro ověřený přístup. Klíče jsou požadovány při každém požadavku. Oba klíče správce jsou funkčně ekvivalentní, což poskytuje úplný přístup pro zápis do vyhledávací služby s možností načíst libovolné informace nebo vytvořit a odstranit libovolný objekt. Existují dva klíče, abyste je mohli použít při nahrazování druhé. 

V jednom okamžiku můžete znovu vygenerovat jenom jednu, zadanou buď jako `primary` klíč nebo `secondary` . U nepřerušované služby nezapomeňte aktualizovat veškerý kód klienta, aby používal sekundární klíč, a přitom přenášet primární klíč. Vyhněte se změnám klíčů, když jsou operace v letu.

V případě, že budete chtít znovu vygenerovat klíče bez aktualizace kódu klienta, požadavky, které používají starý klíč, nebudou úspěšné. Opětovné generování všech nových klíčů vám trvale nezamkne vaše služby a stále budete mít přístup ke službě prostřednictvím portálu. Po opětovném vygenerování primárního a sekundárního klíče můžete aktualizovat klientský kód tak, aby používal nové klíče a operace budou odpovídajícím způsobem pokračovat.

Služba generuje hodnoty pro klíče rozhraní API. Nemůžete zadat vlastní klíč pro použití Azure Kognitivní hledání. Podobně není k dispozici žádný uživatelsky definovaný název pro klíče rozhraní API pro správu. Odkazy na klíč jsou pevné řetězce, buď `primary` nebo `secondary` . 

```azurecli-interactive
az search admin-key renew \
    --resource-group <resource-group-name> \
    --service-name <search-service-name> \
    --key-kind primary
```

Výsledky by měly vypadat podobně jako v následujícím výstupu. Oba klíče jsou vráceny, i když pouze jednou změníte.

```   
{
  "primaryKey": <alphanumeric-guid>,
  "secondaryKey": <alphanumeric-guid>  
}
```

## <a name="create-or-delete-query-keys"></a>Vytvoření nebo odstranění klíčů dotazů

Pokud chcete vytvořit [klíče rozhraní API](search-security-api-keys.md) pro přístup jen pro čtení z klientských aplikací do indexu služby Azure kognitivní hledání, použijte [**příkaz AZ Search Query-Key Create**](/cli/azure/search/query-key#az_search_query_key_create). Klíče dotazů se používají k ověření pro konkrétní index pro účely načítání výsledků hledání. Klíče dotazů neudělují přístup jen pro čtení k ostatním položkám ve službě, jako je index, zdroj dat nebo indexer.

Nemůžete poskytnout klíč pro použití Azure Kognitivní hledání. Služba vygeneruje klíče rozhraní API.

```azurecli-interactive
az search query-key create \
    --name myQueryKey \
    --resource-group <resource-group-name> \
    --service-name <search-service-name>
```

## <a name="scale-replicas-and-partitions"></a>Škálování replik a oddílů

Chcete-li [zvýšit nebo snížit repliky a oddíly,](search-capacity-planning.md) použijte [**AZ Search Service Update**](/cli/azure/search/service#az_search_service_update). Zvýšením replik nebo oddílů se přidá do vašeho účtu, který má za pevnou i variabilní poplatky. Pokud máte dočasnou potřebu pro další výpočetní výkon, můžete zvýšit repliky a oddíly pro zpracování úloh. Oblast monitorování na stránce portálu přehledu obsahuje dlaždice pro latenci dotazů, dotazy za sekundu a omezování, které označují, jestli je aktuální kapacita adekvátní.

Přidání nebo odebrání rezdrojového může nějakou dobu trvat. Úpravy kapacity se vyskytují na pozadí, což umožňuje, aby existující úlohy pokračovaly. Další kapacita se používá pro příchozí požadavky, jakmile je připravená, a nevyžaduje žádnou další konfiguraci. 

Odebrání kapacity může být rušivé. Před snížením kapacity se doporučuje zastavit všechny úlohy indexování a indexerů, aby se předešlo vyřazeným žádostem. Pokud to není proveditelné, můžete zvážit snížení kapacity přírůstkově, jedné repliky a oddílu najednou, dokud nebudou dosaženy nové cílové úrovně.

Po odeslání příkazu neexistuje žádný způsob, jak ho ukončit. Než začnete s revizemi, budete muset počkat, než se příkaz dokončí.

```azurecli-interactive
az search service update \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --partition-count 6 \
    --replica-count 6
```

Kromě aktualizace počtu replik a oddílů můžete také aktualizovat `ip-rules` , `public-access` a `identity-type` .

## <a name="create-a-shared-private-link-resource"></a>Vytvoření sdíleného prostředku privátního propojení

Soukromé koncové body zabezpečených prostředků, které jsou vytvořené prostřednictvím rozhraní API služby Azure Kognitivní hledání, se označují jako *sdílené prostředky privátního propojení*. Důvodem je to, že sdílíte přístup k prostředku, třeba k účtu úložiště, který je integrovaný do [služby Azure Private Link](https://azure.microsoft.com/services/private-link/).

Pokud k indexování dat v Azure Kognitivní hledání používáte indexer a váš zdroj dat je v privátní síti, můžete vytvořit připojení k odchozímu [privátnímu koncovému bodu](../private-link/private-endpoint-overview.md) pro přístup k datům.

Úplný seznam prostředků Azure, pro které můžete vytvořit odchozí soukromé koncové body z Azure Kognitivní hledání najdete [tady](search-indexer-howto-access-private.md#shared-private-link-resources-management-apis) spolu s hodnotami **ID souvisejících skupin** .

Chcete-li vytvořit sdílený prostředek privátního propojení, použijte příkaz [**AZ Search Shared-Private-Link-Resource Create**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list). Před spuštěním tohoto příkazu Pamatujte na to, že pro zdroj dat může být potřeba některá konfigurace.

```azurecli-interactive
az search shared-private-link-resource create \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> \
    --group-id blob \
    --resource-id "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/myBlobStorage"  \
    --request-message "Please approve" 
```


Chcete-li načíst prostředky sdíleného privátního propojení a zobrazit jejich stav, použijte příkaz [**AZ Search Share-Private-Link-Resource list**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list).

```azurecli-interactive
az search shared-private-link-resource list \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

Abyste mohli použít tento příkaz, budete muset připojení schválit pomocí následujícího příkazu. ID připojení privátního koncového bodu bude nutné načíst z podřízeného prostředku. V tomto případě získáme ID připojení z AZ Storage.

```azurecli-interactive
id = (az storage account show -n myBlobStorage --query "privateEndpointConnections[0].id")

az network private-endpoint-connection approve --id $id
```

Chcete-li odstranit sdílený prostředek privátního propojení, použijte příkaz [**AZ Search Shared-Private-Link-Resource Delete**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_delete).

```azurecli-interactive
az search shared-private-link-resource delete \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

Úplné podrobnosti o nastavení sdílených prostředků privátního propojení najdete v dokumentaci k [vytváření připojení indexeru prostřednictvím privátního koncového bodu](search-indexer-howto-access-private.md).

## <a name="next-steps"></a>Další kroky

Sestavení [indexu](search-what-is-an-index.md), [dotazování indexu](search-query-overview.md) pomocí portálu, rozhraní REST API nebo sady .NET SDK.

* [Vytvoření indexu služby Azure Kognitivní hledání v Azure Portal](search-get-started-portal.md)
* [Nastavení indexeru pro načtení dat z jiných služeb](search-indexer-overview.md)
* [Dotazování indexu služby Azure Kognitivní hledání pomocí Průzkumníka služby Search v Azure Portal](search-explorer.md)
* [Jak používat Azure Kognitivní hledání v .NET](search-howto-dotnet-sdk.md)
