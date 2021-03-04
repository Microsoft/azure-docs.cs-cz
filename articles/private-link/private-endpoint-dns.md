---
title: Konfigurace DNS privátního koncového bodu v Azure
description: Informace o konfiguraci DNS privátního koncového bodu Azure
services: private-link
author: allensu
ms.service: private-link
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: allensu
ms.openlocfilehash: edb6748009ef9660c020c77e411ce55f60b45791
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124597"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Konfigurace DNS privátního koncového bodu v Azure

Je důležité správně nakonfigurovat nastavení DNS k překladu IP adresy privátního koncového bodu na plně kvalifikovaný název domény (FQDN) připojovacího řetězce.

Existující služby Microsoft Azure Services už můžou mít konfiguraci DNS pro veřejný koncový bod. Tato konfigurace musí být přepsána, aby se mohla připojit pomocí privátního koncového bodu. 
 
Síťové rozhraní spojené s privátním koncovým bodem obsahuje informace pro konfiguraci DNS. Informace o síťovém rozhraní zahrnují plně kvalifikovaný název domény a privátní IP adresy pro prostředek privátního propojení. 
 
Pomocí následujících možností můžete nakonfigurovat nastavení DNS privátních koncových bodů: 
- **Použijte soubor hostitele (doporučeno pouze pro testování)**. K přepsání DNS můžete použít soubor hostitele na virtuálním počítači.  
- **Použijte privátní ZÓNU DNS**. Pomocí [privátních zón DNS](../dns/private-dns-privatednszone.md) můžete přepsat překlad DNS privátního koncového bodu. Privátní zóna DNS se dá propojit s vaší virtuální sítí a vyřešit konkrétní domény.
- **Použijte službu DNS pro přeposílání (volitelné)**. K přepsání překladu DNS pro prostředek privátního propojení můžete použít službu DNS pro přeposílání. Vytvořte pravidlo předávání DNS pro použití privátní zóny DNS na [serveru DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) hostovaném ve virtuální síti.

> [!IMPORTANT]
> Nedoporučuje se přepsat zónu, která je aktivně používána k řešení veřejných koncových bodů. Připojení k prostředkům nebude možné správně přeložit bez předávání DNS do veřejného serveru DNS. Pokud se chcete vyhnout problémům, vytvořte jiný název domény nebo použijte navrhovaný název pro každou službu níže. 

## <a name="azure-services-dns-zone-configuration"></a>Konfigurace zóny DNS služeb Azure
Azure na veřejném serveru DNS vytvoří záznam DNS kanonického názvu (CNAME). Záznam CNAME přesměruje rozlišení na název privátní domény. Řešení můžete přepsat privátní IP adresou vašich privátních koncových bodů. 
 
Vaše aplikace nemusí měnit adresu URL připojení. Při překladu na veřejnou službu DNS se server DNS přeloží na vaše soukromé koncové body. Proces nemá vliv na vaše stávající aplikace. 

> [!IMPORTANT]
> Privátní sítě, které už používají privátní zónu DNS pro daný typ, se můžou připojovat k veřejným prostředkům jenom v případě, že nemají žádná připojení privátního koncového bodu, jinak se v privátní zóně DNS vyžaduje odpovídající konfigurace DNS, aby bylo možné dokončit pořadí překladu DNS. 

Pro služby Azure použijte doporučené názvy zón, jak je popsáno v následující tabulce:

| Typ prostředku/podprostředek privátního propojení |Název Privátní DNS zóny | Veřejné servery DNS pro přeposílání zóny |
|---|---|---|
| Azure Automation/(Microsoft. Automation/automationAccounts)/Webhook, DSCAndHybridWorker | privatelink.azure-automation.net | azure-automation.net |
| Azure SQL Database (Microsoft. SQL/servery)/SQL Server | privatelink.database.windows.net | database.windows.net |
| Azure synapse Analytics (Microsoft. SQL/servery)/SQL Server  | privatelink.database.windows.net | database.windows.net |
| Účet úložiště (Microsoft. Storage/storageAccounts)/BLOB (BLOB, blob_secondary) | privatelink.blob.core.windows.net | blob.core.windows.net |
| Účet úložiště (Microsoft. Storage/storageAccounts)/Table (tabulka, table_secondary) | privatelink.table.core.windows.net | table.core.windows.net |
| Účet úložiště (Microsoft. Storage/storageAccounts)/Queue (fronta, queue_secondary) | privatelink.queue.core.windows.net | queue.core.windows.net |
| Účet úložiště (Microsoft. Storage/storageAccounts)/soubor (soubor, file_secondary) | privatelink.file.core.windows.net | file.core.windows.net |
| Účet úložiště (Microsoft. Storage/storageAccounts)/Web (web, web_secondary) | privatelink.web.core.windows.net | web.core.windows.net |
| Azure Data Lake systému souborů Gen2 (Microsoft. Storage/storageAccounts)/Data Lake systému souborů Gen2 (DFS, dfs_secondary) | privatelink.dfs.core.windows.net | dfs.core.windows.net |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/SQL | privatelink.documents.azure.com | documents.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/MongoDB | privatelink.mongo.cosmos.azure.com | mongo.cosmos.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/Cassandra | privatelink.cassandra.cosmos.azure.com | cassandra.cosmos.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/Gremlin | privatelink.gremlin.cosmos.azure.com | gremlin.cosmos.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/Table | privatelink.table.cosmos.azure.com | table.cosmos.azure.com |
| Azure Database for PostgreSQL – jeden server (Microsoft. DBforPostgreSQL/servery)/postgresqlServer | privatelink.postgres.database.azure.com | postgres.database.azure.com |
| Azure Database for MySQL (Microsoft. DBforMySQL/servery)/mysqlServer | privatelink.mysql.database.azure.com | mysql.database.azure.com |
| Azure Database for MariaDB (Microsoft. DBforMariaDB/servery)/mariadbServer | privatelink.mariadb.database.azure.com | mariadb.database.azure.com |
| Azure Key Vault (Microsoft. webrecovery/trezory)/trezor | privatelink.vaultcore.azure.net | vault.azure.net <br> vaultcore.azure.net |
| Azure Kubernetes Service – rozhraní Kubernetes API (Microsoft. ContainerService/managedClusters)/Správa | privatelink. {region}. azmk8s. IO | {region}. azmk8s. IO |
| Azure Search (Microsoft. Search/searchServices)/searchService | privatelink.search.windows.net | search.windows.net |
| Azure Container Registry (Microsoft. ContainerRegistry/registrys)/registr | privatelink.azurecr.io | azurecr.io |
| Konfigurace aplikace Azure (Microsoft. AppConfiguration/configurationStores)/configurationStore | privatelink.azconfig.io | azconfig.io |
| Azure Backup (Microsoft. RecoveryServices/trezory)/trezor | privatelink. {region}. Backup. windowsazure. com | {region}. Backup. windowsazure. com |
| Azure Site Recovery (Microsoft. RecoveryServices/trezory)/trezor | {region}. privatelink. siterecovery. windowsazure. com | {region}. hypervrecoverymanager. windowsazure. com |
| Azure Event Hubs (Microsoft. EventHub/obory názvů)/obor názvů | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Service Bus (Microsoft. ServiceBus/obory názvů)/obor názvů | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure IoT Hub (Microsoft. Devices/IotHubs)/iotHub | privatelink.azure-devices.net<br/>privatelink.servicebus.windows.net<sup>1</sup> | azure-devices.net<br/>servicebus.windows.net |
| Azure Relay (Microsoft. Relay/obory názvů)/obor názvů | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Event Grid (Microsoft. EventGrid/témata)/téma | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Event Grid (Microsoft. EventGrid/domény)/doména | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Web Apps (Microsoft. Web/weby)/lokality | privatelink.azurewebsites.net | azurewebsites.net |
| Azure Machine Learning (Microsoft. MachineLearningServices/pracovní prostory)/amlworkspace | privatelink.api.azureml.ms<br/>privatelink.notebooks.azure.net | api.azureml.ms<br/>notebooks.azure.net<br/>instances.azureml.ms<br/>aznbcontent.net |
| Signál (Microsoft. SignalRService/Signaler)/Signal | privatelink.service.signalr.net | service.signalr.net |
| Azure Monitor (Microsoft. Insights/privateLinkScopes)/azuremonitor | privatelink.monitor.azure.com<br/> privatelink.oms.opinsights.azure.com <br/> privatelink.ods.opinsights.azure.com <br/> privatelink.agentsvc.azure-automation.net | monitor.azure.com<br/> oms.opinsights.azure.com<br/> ods.opinsights.azure.com<br/> agentsvc.azure-automation.net |
| Cognitive Services (Microsoft. Cognitiveservices Account/Accounts)/Account | privatelink.cognitiveservices.azure.com  | cognitiveservices.azure.com  |
| Synchronizace souborů Azure (Microsoft. StorageSync/storageSyncServices)/AFS |  privatelink.afs.azure.net  |  afs.azure.net  |
| Azure Data Factory (Microsoft. DataFactory/Factory)/DataFactory |  privatelink.datafactory.azure.net  |  datafactory.azure.net  |
| Azure Data Factory (Microsoft. DataFactory/Factory)/portál |  privatelink.azure.com  |  azure.com  |
| Mezipaměť Azure pro Redis (Microsoft. cache/Redis)/redisCache | privatelink.redis.cache.windows.net | redis.cache.windows.net |

<sup>1</sup> Pro použití s integrovaným koncovým bodem centra událostí s IoT Hub Další informace najdete v tématu [Podpora privátních odkazů pro integrovaný koncový bod IoT Hub](../iot-hub/virtual-network-support.md#built-in-event-hub-compatible-endpoint) .

### <a name="china"></a>Čína

| Typ prostředku/podprostředek privátního propojení |Název Privátní DNS zóny | Veřejné servery DNS pro přeposílání zóny |
|---|---|---|
| Azure SQL Database (Microsoft. SQL/servery)/SQL Server | privatelink.database.chinacloudapi.cn | database.chinacloudapi.cn |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/SQL | privatelink.documents.azure.cn | documents.azure.cn |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/MongoDB | privatelink.mongo.cosmos.azure.cn | mongo.cosmos.azure.cn |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/Cassandra | privatelink.cassandra.cosmos.azure.cn | cassandra.cosmos.azure.cn |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/Gremlin | privatelink.gremlin.cosmos.azure.cn | gremlin.cosmos.azure.cn |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/Table | privatelink.table.cosmos.azure.cn | table.cosmos.azure.cn |
| Azure Database for PostgreSQL – jeden server (Microsoft. DBforPostgreSQL/servery)/postgresqlServer | privatelink.postgres.database.chinacloudapi.cn | postgres.database.chinacloudapi.cn |
| Azure Database for MySQL (Microsoft. DBforMySQL/servery)/mysqlServer | privatelink.mysql.database.chinacloudapi.cn  | mysql.database.chinacloudapi.cn  |
| Azure Database for MariaDB (Microsoft. DBforMariaDB/servery)/mariadbServer | privatelink.mariadb.database.chinacloudapi.cn | mariadb.database.chinacloudapi.cn |


## <a name="dns-configuration-scenarios"></a>Scénáře konfigurace DNS

Plně kvalifikovaný název domény služby se automaticky přeloží na veřejnou IP adresu. Pokud chcete překládat na soukromou IP adresu privátního koncového bodu, změňte konfiguraci DNS.

Služba DNS je kritická součást pro správné fungování aplikace tím, že úspěšně vyřešila IP adresu privátního koncového bodu.

V závislosti na vašich preferencích jsou k dispozici následující scénáře s integrovaným překladem názvů DNS:

- [Úlohy virtuální sítě bez vlastního serveru DNS](#virtual-network-workloads-without-custom-dns-server)
- [Místní úlohy využívající službu DNS pro přeposílání](#on-premises-workloads-using-a-dns-forwarder)
- [Virtuální síť a místní úlohy pomocí služby DNS pro přeposílání](#virtual-network-and-on-premises-workloads-using-a-dns-forwarder)

> [!NOTE]
> [Azure firewall DNS proxy serveru](../firewall/dns-settings.md#dns-proxy) se dá použít jako služba DNS pro překládání [v místních úlohách](#on-premises-workloads-using-a-dns-forwarder) a [ÚLOHÁCH virtuální sítě pomocí služby DNS pro přeposílání](#virtual-network-and-on-premises-workloads-using-a-dns-forwarder).

## <a name="virtual-network-workloads-without-custom-dns-server"></a>Úlohy virtuální sítě bez vlastního serveru DNS

Tato konfigurace je vhodná pro úlohy virtuální sítě bez vlastního serveru DNS. V tomto scénáři se klient dotazuje na IP adresu privátního koncového bodu na službu DNS poskytovanou službou Azure [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md). Azure DNS bude zodpovědná za překlad DNS privátních zón DNS.

> [!NOTE]
> V tomto scénáři se používá privátní zóna DNS, kterou doporučuje Azure SQL Database. Pro jiné služby můžete model upravit pomocí následujícího odkazu: [Konfigurace zóny DNS služeb Azure](#azure-services-dns-zone-configuration).

Ke správné konfiguraci potřebujete tyto prostředky:

- Klientská virtuální síť

- Privátní DNS [privatelink.Database.Windows.NET](../dns/private-dns-privatednszone.md)  zóny s [typem záznamu](../dns/dns-zones-records.md#record-types)

- Informace o privátním koncovém bodu (název záznamu a privátní IP adresa)

Následující snímek obrazovky znázorňuje sekvenci překladu názvů DNS z úloh virtuální sítě pomocí privátní zóny DNS:

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="Jedna virtuální síť a DNS poskytovaná službou Azure":::

Tento model můžete roztáhnout do partnerských virtuálních sítí přidružených ke stejnému privátnímu koncovému bodu. [Přidejte nová propojení virtuálních sítí](../dns/private-dns-virtual-network-links.md) do privátní zóny DNS pro všechny partnerské virtuální sítě.

> [!IMPORTANT]
> Pro tuto konfiguraci se vyžaduje jedna privátní zóna DNS. Vytvoření více zón se stejným názvem pro různé virtuální sítě by vyžadovalo ruční operace sloučení záznamů DNS.

> [!IMPORTANT]
> Pokud používáte privátní koncový bod v modelu hvězdicové lokality z jiného předplatného, znovu použijte stejnou privátní zónu DNS v centru.

V tomto scénáři je dostupná síťová topologie [centra a paprsků](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) . Sítě paprsků sdílejí soukromý koncový bod. Virtuální sítě paprsků jsou propojené se stejnou privátní zónou DNS. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="Centrum a Paprskový s DNS poskytovanou službou Azure":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>Místní úlohy využívající službu DNS pro přeposílání

V případě místních úloh za účelem vyřešení plně kvalifikovaného názvu domény privátního koncového bodu použijte službu pro překládání DNS k překladu [veřejné zóny DNS](#azure-services-dns-zone-configuration) služby Azure v Azure.

Následující scénář je určen pro místní síť s přesměrováním DNS v Azure. Tento server pro překládá dotazy DNS prostřednictvím služby pro přeposílání na úrovni serveru na [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md)DNS Azure. 

> [!NOTE]
> V tomto scénáři se používá privátní zóna DNS, kterou doporučuje Azure SQL Database. Pro jiné služby můžete model upravit pomocí následujícího odkazu: [Konfigurace zóny DNS služeb Azure](#azure-services-dns-zone-configuration).

Ke správné konfiguraci potřebujete tyto prostředky:

- Místní síť
- Virtuální síť [připojená k místnímu](/azure/architecture/reference-architectures/hybrid-networking/) prostředí
- Služba přeposílání DNS nasazená v Azure 
- Zóny Privátní DNS [privatelink.Database.Windows.NET](../dns/private-dns-privatednszone.md) s [typem záznamu](../dns/dns-zones-records.md#record-types)
- Informace o privátním koncovém bodu (název záznamu a privátní IP adresa)

Následující diagram znázorňuje sekvenci překladu názvů DNS z místní sítě. Konfigurace používá službu DNS pro přeposílání v Azure. Řešení je provedeno soukromou zónou DNS [propojenou s virtuální sítí](../dns/private-dns-virtual-network-links.md):

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="Místně pomocí Azure DNS":::

Tato konfigurace se dá prodloužit na místní síť, která už má řešení DNS. Místní řešení DNS je nakonfigurované tak, aby přesměrovalo provoz DNS na Azure DNS přes [podmíněný Server pro dopředné](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)služby. Podmíněný předávací server odkazuje na server DNS, který je nasazený v Azure.

> [!NOTE]
> V tomto scénáři se používá privátní zóna DNS, kterou doporučuje Azure SQL Database. Pro jiné služby můžete model upravit pomocí následujícího odkazu: [Konfigurace zóny DNS služeb Azure](#azure-services-dns-zone-configuration) .

Ke správné konfiguraci potřebujete tyto prostředky:

- Místní síť s vlastním řešením DNS 
- Virtuální síť [připojená k místnímu](/azure/architecture/reference-architectures/hybrid-networking/) prostředí
- Služba přeposílání DNS nasazená v Azure
- Zóny Privátní DNS [privatelink.Database.Windows.NET](../dns/private-dns-privatednszone.md)  s [typem záznamu](../dns/dns-zones-records.md#record-types)
- Informace o privátním koncovém bodu (název záznamu a privátní IP adresa)

Následující diagram znázorňuje překlad DNS z místní sítě. Překlad DNS je podmíněně předaný do Azure. Řešení je provedeno soukromou zónou DNS [propojenou s virtuální sítí](../dns/private-dns-virtual-network-links.md).

> [!IMPORTANT]
> Podmíněné přesměrování je nutné provést v doporučené [službě pro předávání veřejné zóny DNS](#azure-services-dns-zone-configuration). Například: `database.windows.net` místo **privatelink**. Database.Windows.NET.

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="Místní přesměrování na Azure DNS":::

## <a name="virtual-network-and-on-premises-workloads-using-a-dns-forwarder"></a>Virtuální síť a místní úlohy pomocí služby DNS pro přeposílání

Pro úlohy, které přistupují k privátnímu koncovému bodu z virtuálních i místních sítí, použijte službu DNS pro překládání k překladu [veřejné zóny DNS](#azure-services-dns-zone-configuration) služby Azure nasazené v Azure.

Následující scénář je určen pro místní síť s virtuálními sítěmi v Azure. Obě sítě mají přístup k privátnímu koncovému bodu umístěnému ve sdílené síti rozbočovače.

Tento server DNS zodpovídá za překlad všech dotazů DNS prostřednictvím služby pro přeposílání na úrovni serveru na službu DNS zadanou v Azure [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md).

> [!IMPORTANT]
> Pro tuto konfiguraci se vyžaduje jedna privátní zóna DNS. Všechna připojení klientů vytvořená z místní sítě a [virtuálních sítí s partnerským vztahem](../virtual-network/virtual-network-peering-overview.md) musí používat také stejnou privátní zónu DNS.

> [!NOTE]
> V tomto scénáři se používá privátní zóna DNS, kterou doporučuje Azure SQL Database. Pro jiné služby můžete model upravit pomocí následujícího odkazu: [Konfigurace zóny DNS služeb Azure](#azure-services-dns-zone-configuration).

Ke správné konfiguraci potřebujete tyto prostředky:

- Místní síť
- Virtuální síť [připojená k místnímu](/azure/architecture/reference-architectures/hybrid-networking/) prostředí
- [Virtuální síť s partnerským vztahem](../virtual-network/virtual-network-peering-overview.md) 
- Služba přeposílání DNS nasazená v Azure
- Zóny Privátní DNS [privatelink.Database.Windows.NET](../dns/private-dns-privatednszone.md)  s [typem záznamu](../dns/dns-zones-records.md#record-types)
- Informace o privátním koncovém bodu (název záznamu a privátní IP adresa)

Následující diagram znázorňuje překlad DNS pro sítě, místní i virtuální sítě. Řešení používá službu DNS pro překládání. Řešení je provedeno soukromou zónou DNS [propojenou s virtuální sítí](../dns/private-dns-virtual-network-links.md):

:::image type="content" source="media/private-endpoint-dns/hybrid-scenario.png" alt-text="Hybridní scénář":::

## <a name="next-steps"></a>Další kroky
- [Informace o privátních koncových bodech](private-endpoint-overview.md)
