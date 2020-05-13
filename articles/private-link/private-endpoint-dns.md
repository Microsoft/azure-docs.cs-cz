---
title: Konfigurace DNS privátního koncového bodu Azure
description: Informace o konfiguraci DNS privátního koncového bodu Azure
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: allensu
ms.openlocfilehash: 7db02546b562f1b542080efdbda8968940655e95
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121273"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Konfigurace DNS privátního koncového bodu Azure


Při připojování k prostředku privátního propojení pomocí plně kvalifikovaného názvu domény (FQDN) jako součásti připojovacího řetězce je důležité správně nakonfigurovat nastavení DNS tak, aby se přeložilo na přidělenou privátní IP adresu. Existující služby Azure už můžou mít konfiguraci DNS, která se má použít při připojování přes Veřejný koncový bod. Tato konfigurace se musí přepsat, aby se mohla připojit pomocí privátního koncového bodu. 
 
Síťové rozhraní přidružené k privátnímu koncovému bodu obsahuje úplnou sadu informací potřebných ke konfiguraci DNS, včetně plně kvalifikovaného názvu domény a privátních IP adres přidělených pro daný prostředek privátního propojení. 
 
Pomocí následujících možností můžete nakonfigurovat nastavení DNS privátních koncových bodů: 
- **Použijte soubor hostitele (doporučeno pouze pro testování)**. K přepsání DNS můžete použít soubor hostitele na virtuálním počítači.  
- **Použijte privátní ZÓNU DNS**. K přepsání překladu DNS pro daný privátní koncový bod můžete použít [soukromé zóny DNS](../dns/private-dns-privatednszone.md) . Privátní zóna DNS se dá propojit s vaší virtuální sítí a vyřešit konkrétní domény.
- **Použijte službu DNS pro přeposílání (volitelné)**. K přepsání překladu DNS pro daný prostředek privátního propojení můžete použít server DNS pro přeposílání. Pokud je váš [Server DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) hostovaný ve virtuální síti, můžete vytvořit předávací pravidlo DNS pro použití privátní zóny DNS a zjednodušit tak konfiguraci všech prostředků privátního propojení.
 
> [!IMPORTANT]
> Nedoporučujeme potlačit zónu, která je aktivně používána k řešení veřejných koncových bodů. Připojení k prostředkům nebude možné správně přeložit bez předávání DNS do veřejného serveru DNS. Pokud se chcete vyhnout problémům, vytvořte jiný název domény nebo použijte navrhovaný název pro každou službu níže. 

## <a name="azure-services-dns-zone-configuration"></a>Konfigurace zóny DNS služeb Azure
Služba Azure vytvoří záznam DNS (CNAME) kanonického názvu (CNAME) na veřejném serveru DNS pro přesměrování řešení na navrhovaný název privátní domény. Řešení můžete přepsat privátní IP adresou vašich privátních koncových bodů. 
 
Vaše aplikace nemusí měnit adresu URL připojení. Při pokusu o překlad pomocí veřejné služby DNS se server DNS teď přeloží na vaše soukromé koncové body. Proces nemá vliv na vaše stávající aplikace. 

Pro služby Azure použijte doporučené názvy zón, jak je popsáno v následující tabulce:

| Typ prostředku/podprostředek privátního propojení |Název Privátní DNS zóny | Název veřejné zóny DNS |
|---|---|---|---|
| SQL DB (Microsoft. SQL/servery)/SQL Server | privatelink.database.windows.net | database.windows.net |
| Azure synapse Analytics (Microsoft. SQL/servery)/SQL Server  | privatelink.database.windows.net | database.windows.net |
| Účet úložiště (Microsoft. Storage/storageAccounts)/BLOB (BLOB, blob_secondary) | privatelink.blob.core.windows.net | blob.core.windows.net |
| Účet úložiště (Microsoft. Storage/storageAccounts)/Table (tabulka, table_secondary) | privatelink.table.core.windows.net | table.core.windows.net |
| Účet úložiště (Microsoft. Storage/storageAccounts)/Queue (fronta, queue_secondary) | privatelink.queue.core.windows.net | queue.core.windows.net |
| Účet úložiště (Microsoft. Storage/storageAccounts)/soubor (soubor, file_secondary) | privatelink.file.core.windows.net | file.core.windows.net |
| Účet úložiště (Microsoft. Storage/storageAccounts)/Web (web, web_secondary) | privatelink.web.core.windows.net | web.core.windows.net |
| Data Lake systému souborů Gen2 (Microsoft. Storage/storageAccounts)/Data Lake systému souborů Gen2 (DFS, dfs_secondary) | privatelink.dfs.core.windows.net | dfs.core.windows.net |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/SQL | privatelink.documents.azure.com | documents.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/MongoDB | privatelink.mongo.cosmos.azure.com | mongo.cosmos.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/Cassandra | privatelink.cassandra.cosmos.azure.com | cassandra.cosmos.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/Gremlin | privatelink.gremlin.cosmos.azure.com | gremlin.cosmos.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/Table | privatelink.table.cosmos.azure.com | table.cosmos.azure.com |
| Azure Database for PostgreSQL – jeden server (Microsoft. DBforPostgreSQL/servery)/postgresqlServer | privatelink.postgres.database.azure.com | postgres.database.azure.com |
| Azure Database for MySQL (Microsoft. DBforMySQL/servery)/mysqlServer | privatelink.mysql.database.azure.com | mysql.database.azure.com |
| Azure Database for MariaDB (Microsoft. DBforMariaDB/servery)/mariadbServer | privatelink.mariadb.database.azure.com | mariadb.database.azure.com |
| Azure Key Vault (Microsoft. webrecovery/trezory)/trezor | privatelink.vaultcore.azure.net | vault.azure.net |
| Azure Kubernetes Service – rozhraní Kubernetes API (Microsoft. ContainerService/managedClusters)/managedCluster | privatelink. {region}. azmk8s. IO | {region}. azmk8s. IO |
| Azure Search (Microsoft. Search/searchServices)/searchService | privatelink.search.windows.net | search.windows.net |
| Azure Container Registry (Microsoft. ContainerRegistry/registrys)/registr | privatelink.azurecr.io | azurecr.io |
| Konfigurace aplikace Azure (Microsoft. AppConfiguration/configurationStores)/configurationStore | privatelink.azconfig.io | azconfig.io |
| Azure Backup (Microsoft. RecoveryServices/trezory)/trezor | privatelink. {region}. Backup. windowsazure. com | {region}. Backup. windowsazure. com |
| Centrum událostí Azure (Microsoft. EventHub/obory názvů)/obor názvů | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Service Bus (Microsoft. ServiceBus/obory názvů)/obor názvů | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Relay (Microsoft. Relay/obory názvů)/obor názvů | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Event Grid (Microsoft. EventGrid/témata)/téma | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Event Grid (Microsoft. EventGrid/domény)/doména | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure WebApps (Microsoft. Web/weby)/Web | privatelink.azurewebsites.net | azurewebsites.net |
| Azure Machine Learning (Microsoft. MachineLearningServices/pracovní prostory)/pracovní prostor | privatelink.api.azureml.ms | api.azureml.ms |

 


## <a name="dns-configuration-scenarios"></a>Scénáře konfigurace DNS

Plně kvalifikovaný název domény služby se automaticky přeloží na veřejnou IP adresu, takže aby bylo možné přeložit na privátní IP adresu privátního koncového bodu, musíte odpovídajícím způsobem změnit konfiguraci DNS.

Služba DNS je důležitou součástí, která aplikaci správně funguje, a to tak, že ji převedete správným způsobem na IP adresu privátního koncového bodu.

V závislosti na vašich preferencích jsou k dispozici následující scénáře pro integraci překladu DNS:

- [Úlohy virtuální sítě bez vlastního serveru DNS](#virtual-network-workloads-without-custom-dns-server)
- [Místní úlohy využívající službu DNS pro přeposílání](#on-premises-workloads-using-a-dns-forwarder)

## <a name="virtual-network-workloads-without-custom-dns-server"></a>Úlohy virtuální sítě bez vlastního serveru DNS

Tato konfigurace je vhodná pro úlohy virtuální sítě bez vlastního serveru DNS. V tomto scénáři se klient dotazuje na IP adresu privátního koncového bodu do Azure poskytnuté DNS [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md). Azure DNS bude zodpovědná za překlad DNS privátních zón DNS.


> [!NOTE]
> Tento scénář používá doporučenou zónu Privátní DNS služby Azure SQL Database. Pro ostatní služby můžete model upravit pomocí následujícího odkazu na [konfiguraci zóny DNS služeb Azure](#azure-services-dns-zone-configuration).

Aby bylo možné správně nakonfigurovat, budete potřebovat následující prostředky:

- Klientská virtuální síť

- Privátní DNS [privatelink.Database.Windows.NET](../dns/private-dns-privatednszone.md) zóny s [typem záznamu](../dns/dns-zones-records.md#record-types)

- Informace o privátním koncovém bodu (název záznamu a privátní IP adresa)

Následující diagram znázorňuje sekvenci překladu názvů DNS z úloh virtuální sítě pomocí privátní zóny DNS.

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="Jedna virtuální síť a DNS poskytovaná službou Azure":::

Tento model se dá rozšířit na několik partnerských virtuálních sítí, které jsou přidružené ke stejnému privátnímu koncovému bodu. To se dá udělat [přidáním nových odkazů virtuální sítě](../dns/private-dns-virtual-network-links.md) do privátní zóny DNS pro všechny partnerské virtuální sítě.

> [!IMPORTANT]
>  Pro tuto konfiguraci se vyžaduje jedna privátní zóna DNS. vytvořením několika zón se stejným názvem pro různé virtuální sítě by bylo potřeba ruční operace sloučení záznamů DNS.

V tomto scénáři je propojená topologie sítě [rozbočovače &](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) s paprsky se sítěmi, které sdílí společný privátní koncový bod a všechny virtuální sítě paprsků jsou propojené se stejnou privátní zónou DNS. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="Centrum a Paprskový s DNS poskytovanou službou Azure":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>Místní úlohy využívající službu DNS pro přeposílání
 
Aby bylo možné v místních úlohách přeložit plně kvalifikovaný název domény privátního koncového bodu na privátní IP adresu, je nutné použít službu DNS pro překládání, aby se zajistilo řešení [veřejné zóny DNS](#azure-services-dns-zone-configuration) služby Azure nasazené v Azure.


Následující scénář je vhodný pro místní síť, která má službu DNS pro přeposílání v Azure, která je pak odpovědná za řešení všech dotazů DNS prostřednictvím služby pro překládání na úrovni serveru Azure poskytnuté DNS [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) 

> [!NOTE]
> Tento scénář používá doporučenou zónu Privátní DNS služby Azure SQL Database.Pro ostatní služby můžete model upravit pomocí následujícího odkazu na [konfiguraci zóny DNS služeb Azure](#azure-services-dns-zone-configuration).

Aby bylo možné správně nakonfigurovat, budete potřebovat následující prostředky:

- Místní síť
- Virtuální síť [připojená k místnímu](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) prostředí
- Služba přeposílání DNS nasazená v Azure 
- Zóny Privátní DNS [privatelink.Database.Windows.NET](../dns/private-dns-privatednszone.md)   s [typem záznamu](../dns/dns-zones-records.md#record-types)
- Informace o privátním koncovém bodu (název záznamu a privátní IP adresa)

Následující diagram znázorňuje sekvenci překladu DNS z místní sítě, která používá službu DNS pro přeposílání v Azure, kde se řešení provádí pomocí privátní zóny DNS propojené s virtuální sítí.

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="Místně pomocí Azure DNS":::

Tato konfigurace se dá prodloužit pro místní síť, která už je na svém místě řešení DNS. 
Místní řešení DNS je potřeba nakonfigurovat tak, aby přesměrovalo provoz DNS do Azure DNS prostřednictvím [podmíněného](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) dosazování, které se odkazuje na službu DNS pro přeposílání v Azure.

> [!NOTE]
> Tento scénář používá doporučenou zónu Privátní DNS služby Azure SQL Database.Pro ostatní služby můžete model upravit pomocí následujícího odkazu na [konfiguraci zóny DNS služeb Azure](#azure-services-dns-zone-configuration).

Aby bylo možné správně nakonfigurovat, budete potřebovat následující prostředky:


- Místní síť s vlastním řešením DNS 
- Virtuální síť [připojená k místnímu](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) prostředí
- Služba přeposílání DNS nasazená v Azure
- Zóny Privátní DNS [privatelink.Database.Windows.NET](../dns/private-dns-privatednszone.md)    s [typem záznamu](../dns/dns-zones-records.md#record-types)
- Informace o privátním koncovém bodu (název záznamu a privátní IP adresa)

Následující diagram znázorňuje sekvenci překladu názvů DNS z místní sítě, která podmíněně přechází do Azure provoz DNS, kde se řešení provádí v privátní zóně DNS propojené s virtuální sítí.

> [!IMPORTANT]
> Podmíněné přesměrování je nutné provést v rámci [veřejné zóny DNS](#azure-services-dns-zone-configuration)   ex:  `database.windows.net`   místo **privatelink**. Database.Windows.NET.

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="Místní přesměrování na Azure DNS":::


## <a name="next-steps"></a>Další kroky
- [Informace o privátních koncových bodech](private-endpoint-overview.md)
