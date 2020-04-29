---
title: Konfigurace DNS privátního koncového bodu Azure
description: Informace o konfiguraci DNS privátního koncového bodu Azure
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: allensu
ms.openlocfilehash: 477a5ffa971120d1a98c09ac4ae8ebda1c82b770
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82209022"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Konfigurace DNS privátního koncového bodu Azure


Při připojování k prostředku privátního propojení pomocí plně kvalifikovaného názvu domény (FQDN) jako součásti připojovacího řetězce je důležité správně nakonfigurovat nastavení DNS tak, aby se přeložilo na přidělenou privátní IP adresu. Existující služby Azure už můžou mít konfiguraci DNS, která se má použít při připojování přes Veřejný koncový bod. To je nutné přepsat pro připojení pomocí privátního koncového bodu. 
 
Síťové rozhraní přidružené k privátnímu koncovému bodu obsahuje úplnou sadu informací potřebných ke konfiguraci DNS, včetně plně kvalifikovaného názvu domény a privátních IP adres přidělených pro daný prostředek privátního propojení. 
 
Pomocí následujících možností můžete nakonfigurovat nastavení DNS privátních koncových bodů: 
- **Použijte soubor hostitele (doporučeno pouze pro testování)**. K přepsání DNS můžete použít soubor hostitele na virtuálním počítači.  
- **Použijte privátní ZÓNU DNS**. K přepsání překladu DNS pro daný privátní koncový bod můžete použít [soukromé zóny DNS](../dns/private-dns-privatednszone.md) . Privátní zóna DNS se dá propojit s vaší virtuální sítí a vyřešit konkrétní domény.
- **Použijte vlastní server DNS**. Pomocí vlastního serveru DNS můžete přepsat překlad DNS pro daný prostředek privátního propojení. Pokud je váš [Server DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) hostovaný ve virtuální síti, můžete vytvořit předávací pravidlo DNS pro použití privátní zóny DNS a zjednodušit tak konfiguraci všech prostředků privátního propojení.
 
> [!IMPORTANT]
> Nedoporučujeme potlačit zónu, která je aktivně používána k řešení veřejných koncových bodů. Připojení k prostředkům nebude možné správně přeložit bez předávání DNS do veřejného serveru DNS. Pokud se chcete vyhnout problémům, vytvořte jiný název domény nebo použijte navrhovaný název pro každou službu níže. 

## <a name="azure-services-dns-zone-configuration"></a>Konfigurace zóny DNS služeb Azure
Služba Azure vytvoří záznam DNS (CNAME) kanonického názvu (CNAME) na veřejném serveru DNS pro přesměrování řešení na navržené názvy privátních domén. Řešení bude možné přepsat privátní IP adresou vašich privátních koncových bodů. 
 
Vaše aplikace nemusí měnit adresu URL připojení. Při pokusu o překlad pomocí veřejné služby DNS se server DNS teď přeloží na vaše soukromé koncové body. Proces nemá vliv na vaše stávající aplikace. 

Pro služby Azure použijte doporučené názvy zón, jak je popsáno v následující tabulce:

|Typ prostředku privátního propojení   |Vytváření  |Název zóny  |
|---------|---------|---------|
|SQL DB (Microsoft. SQL/servery)    |  SQL Server (sqlServer)        |   privatelink.database.windows.net       |
|Azure synapse Analytics (Microsoft. SQL/servery)    |  SQL Server (sqlServer)        | privatelink.database.windows.net |
|Účet úložiště (Microsoft. Storage/storageAccounts)    |  BLOB (objekt blob, blob_secondary)        |    privatelink.blob.core.windows.net      |
|Účet úložiště (Microsoft. Storage/storageAccounts)    |    Tabulka (tabulka, table_secondary)      |   privatelink.table.core.windows.net       |
|Účet úložiště (Microsoft. Storage/storageAccounts)    |    Queue (Queue, queue_secondary)     |   privatelink.queue.core.windows.net       |
|Účet úložiště (Microsoft. Storage/storageAccounts)   |    Soubor (soubor, file_secondary)      |    privatelink.file.core.windows.net      |
|Účet úložiště (Microsoft. Storage/storageAccounts)     |  Web (web, web_secondary)        |    privatelink.web.core.windows.net      |
|Data Lake Gen2 systému souborů (Microsoft. Storage/storageAccounts)  |  Data Lake Gen2 systému souborů (DFS, dfs_secondary)        |     privatelink.dfs.core.windows.net     |
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|SQL    |privatelink.documents.azure.com|
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|MongoDB    |privatelink.mongo.cosmos.azure.com|
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|Cassandra|privatelink.cassandra.cosmos.azure.com|
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|Gremlin    |privatelink.gremlin.cosmos.azure.com|
|Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)|Table|privatelink.table.cosmos.azure.com|
|Azure Database for PostgreSQL – jeden server (Microsoft. DBforPostgreSQL/servery)|postgresqlServer|privatelink.postgres.database.azure.com|
|Azure Database for MySQL (Microsoft. DBforMySQL/servery)|mysqlServer|privatelink.mysql.database.azure.com|
|Azure Database for MariaDB (Microsoft. DBforMariaDB/servery)|mariadbServer|privatelink.mariadb.database.azure.com|
|Azure Key Vault (trezor a trezory Microsoft.|Trezor|privatelink.vaultcore.azure.net|
|Azure Kubernetes Service – rozhraní Kubernetes API (Microsoft. ContainerService/managedClusters)    | managedCluster | {GUID}. privatelink. {region}. azmk8s. IO|
|Azure Search (Microsoft. Search/searchServices)|searchService|privatelink.search.windows.net|   
|Azure Container Registry (Microsoft. ContainerRegistry/Registry) | registry | privatelink.azurecr.io |
|Konfigurace aplikace Azure (Microsoft. Appconfiguration/configurationStores)| configurationStore | privatelink.azconfig.io|
|Azure Backup (Microsoft. RecoveryServices/trezory)| Trezor |privatelink. {region}. Backup. windowsazure. com|
|Centrum událostí Azure (Microsoft. EventHub/obory názvů)| namespace |privatelink.servicebus.windows.net|
|Azure Service Bus (Microsoft. ServiceBus/obory názvů) | namespace |privatelink.servicebus.windows.net|
|Azure Relay (Microsoft. Relay/obory názvů) | namespace |privatelink.servicebus.windows.net|
|Azure Event Grid (Microsoft. EventGrid/témata)     | téma | výklad. {region}. privatelink. eventgrid. Azure. NET|
|Azure Event Grid (Microsoft. EventGrid/domény) | doména | Domain. {region}. privatelink. eventgrid. Azure. NET |
|Azure WebApps (Microsoft. Web/weby)    | webovém | privatelink.azurewebsites.net |
|Azure Machine Learning (Microsoft. MachineLearningServices/pracovní prostory)    | Pracovní prostor | privatelink.api.azureml.ms |
 


## <a name="dns-configuration-scenarios"></a>Scénáře konfigurace DNS

Plně kvalifikovaný název domény služeb řeší veřejnou IP adresu, musíte změnit konfiguraci DNS a vyřešit tak soukromou IP adresu privátního koncového bodu.

Služba DNS je důležitou součástí, která aplikaci správně funguje, a to tak, že ji převedete správným způsobem na IP adresu privátního koncového bodu.

V závislosti na vašich preferencích jsou k dispozici následující scénáře pro integraci překladu DNS:

- [Virtual Network úlohy bez vlastního serveru DNS](#virtual-network-workloads-without-custom-dns-server)


## <a name="virtual-network-workloads-without-custom-dns-server"></a>Virtual Network úlohy bez vlastního serveru DNS

Tato konfigurace je vhodná pro úlohy virtuální sítě bez vlastního serveru DNS. V tomto scénáři se klient dotazuje na IP adresu privátního koncového bodu do Azure poskytnuté DNS [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md). Azure DNS bude zodpovědná za překlad DNS privátních zón DNS.


 > [!NOTE]
> Tento scénář používá doporučenou zónu Privátní DNS služby Azure SQL Database. Pro ostatní služby můžete model upravit pomocí následujícího odkazu na [konfiguraci zóny DNS služeb Azure](#azure-services-dns-zone-configuration).

Ke správné konfiguraci byste potřebovali tyto prostředky:

- Klientská virtuální síť

- Privátní DNS [privatelink.Database.Windows.NET](../dns/private-dns-privatednszone.md) zóny s [typem záznamu](../dns/dns-zones-records.md#record-types)

- Informace o privátním koncovém bodu (název záznamu a privátní IP adresa)

Následující diagram znázorňuje sekvenci překladu názvů DNS z úloh virtuální sítě pomocí privátní zóny DNS.

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="Jedna virtuální síť a služba DNS poskytovaná Azure":::

Tento model se dá rozšířit na několik partnerských virtuálních sítí, které jsou přidružené ke stejnému privátnímu koncovému bodu. To se dá udělat [přidáním nových odkazů virtuální sítě](../dns/private-dns-virtual-network-links.md) do privátní zóny DNS pro všechny partnerské virtuální sítě.

 > [!IMPORTANT]
>  Pro tuto konfiguraci se vyžaduje jedna privátní zóna DNS. vytvořením několika zón se stejným názvem pro různé virtuální sítě by bylo potřeba ruční operace sloučení záznamů DNS.

V tomto scénáři je síť [centra & paprsků se sítěmi rozbočovače](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) , které sdílí společný privátní koncový bod a všechny virtuální sítě paprsků jsou propojené se stejnou privátní zónou DNS. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="centrum a paprsky s DNS poskytovanou službou Azure":::


## <a name="next-steps"></a>Další kroky
- [Informace o privátních koncových bodech](private-endpoint-overview.md)
