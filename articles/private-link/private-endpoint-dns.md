---
title: Konfigurace DNS privátního koncového bodu v Azure
description: Informace o konfiguraci DNS privátního koncového bodu Azure
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: allensu
ms.openlocfilehash: 1c296b157fbac1e4c8d3fefb2b8cc09ff2ccc7a8
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620592"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Konfigurace DNS privátního koncového bodu v Azure


Pokud se připojujete k prostředku privátního propojení pomocí plně kvalifikovaného názvu domény (FQDN) jako součást připojovacího řetězce, je důležité správně nakonfigurovat nastavení DNS tak, aby se přeložilo na přidělenou privátní IP adresu. Existující služby Microsoft Azure Services už můžou mít konfiguraci DNS, která se má použít při připojování přes Veřejný koncový bod. Tato konfigurace se musí přepsat, aby se mohla připojit pomocí privátního koncového bodu. 
 
Síťové rozhraní přidružené k privátnímu koncovému bodu obsahuje úplnou sadu informací potřebných ke konfiguraci DNS, včetně plně kvalifikovaného názvu domény a privátních IP adres přidělených konkrétnímu prostředku privátního propojení. 
 
Pomocí následujících možností můžete nakonfigurovat nastavení DNS privátních koncových bodů: 
- **Použijte soubor hostitele (doporučeno pouze pro testování)**. K přepsání DNS můžete použít soubor hostitele na virtuálním počítači.  
- **Použijte privátní ZÓNU DNS**. [Privátní zóny DNS](../dns/private-dns-privatednszone.md) můžete použít k přepsání překladu DNS pro konkrétní soukromý koncový bod. Privátní zóna DNS se dá propojit s vaší virtuální sítí a vyřešit konkrétní domény.
- **Použijte službu DNS pro přeposílání (volitelné)**. K přepsání překladu DNS pro konkrétní prostředek privátního propojení můžete použít server DNS pro přeposílání. Pokud je váš [Server DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) hostovaný ve virtuální síti, můžete vytvořit předávací pravidlo DNS pro použití privátní zóny DNS a zjednodušit tak konfiguraci všech prostředků privátního propojení.

> [!IMPORTANT]
> Nedoporučuje se přepsat zónu, která je aktivně používána k řešení veřejných koncových bodů. Připojení k prostředkům nebude možné správně přeložit bez předávání DNS do veřejného serveru DNS. Pokud se chcete vyhnout problémům, vytvořte jiný název domény nebo použijte navrhovaný název pro každou službu níže. 



## <a name="azure-services-dns-zone-configuration"></a>Konfigurace zóny DNS služeb Azure
Služba Azure ve veřejné službě DNS vytvoří záznam DNS kanonického názvu (CNAME), který přesměruje řešení na navrhovaný název privátní domény. Řešení můžete přepsat privátní IP adresou vašich privátních koncových bodů. 
 
Vaše aplikace nemusí měnit adresu URL připojení. Při pokusu o překlad pomocí veřejné služby DNS se teď server DNS bude překládat na vaše privátní koncové body. Proces nemá vliv na vaše stávající aplikace. 

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
| Azure Event Hubs (Microsoft. EventHub/obory názvů)/obor názvů | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Service Bus (Microsoft. ServiceBus/obory názvů)/obor názvů | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure IoT Hub (Microsoft. Devices/IotHubs)/iotHub | privatelink.azure-devices.net | azure-devices.net |
| Azure Relay (Microsoft. Relay/obory názvů)/obor názvů | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Event Grid (Microsoft. EventGrid/témata)/téma | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Event Grid (Microsoft. EventGrid/domény)/doména | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Web Apps (Microsoft. Web/weby)/lokality | privatelink.azurewebsites.net | azurewebsites.net |
| Azure Machine Learning (Microsoft. MachineLearningServices/pracovní prostory)/pracovní prostor | privatelink.api.azureml.ms | api.azureml.ms |
| IoT Hub (Microsoft. Devices/IotHubs)/IotHub | privatelink.azure-devices.net | azure-devices.net |
| Signál (Microsoft. SignalRService/Signaler)/Signal | privatelink.service.signalr.net | service.signalr.net |
| Azure Monitor (Microsoft. Insights/privateLinkScopes)/azuremonitor | privatelink.monitor.azure.com<br/> privatelink.oms.opinsights.azure.com <br/> privatelink.ods.opinsights.azure.com <br/> privatelink.agentsvc.azure-automation.net | monitor.azure.com<br/> oms.opinsights.azure.com<br/> ods.opinsights.azure.com<br/> agentsvc.azure-automation.net |
| Cognitive Services (Microsoft. Cognitiveservices Account/Accounts)/Account | privatelink.cognitiveservices.azure.com  | cognitiveservices.azure.com  |
| Azure File Sync (Microsoft. StorageSync/storageSyncServices)/AFS |  privatelink.afs.azure.net  |  afs.azure.net  |
| Azure Data Factory (Microsoft. DataFactory/Factory)/DataFactory |  privatelink.datafactory.azure.net  |  datafactory.azure.net  |
| Azure Data Factory (Microsoft. DataFactory/Factory)/portál |  privatelink.azure.com  |  azure.com  |
| Mezipaměť Azure pro Redis (Microsoft. cache/Redis)/redisCache | privatelink.redis.cache.windows.net | redis.cache.windows.net |

 
## <a name="dns-configuration-scenarios"></a>Scénáře konfigurace DNS

Plně kvalifikovaný název domény služby se automaticky přeloží na veřejnou IP adresu. Pokud chcete překládat na privátní IP adresu privátního koncového bodu, musíte odpovídajícím způsobem změnit konfiguraci DNS.

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

Tento model se dá rozšířit na několik partnerských virtuálních sítí, které jsou přidružené ke stejnému privátnímu koncovému bodu. To se dá udělat [přidáním nových odkazů virtuální sítě](../dns/private-dns-virtual-network-links.md) do privátní zóny DNS pro všechny partnerské virtuální sítě.

> [!IMPORTANT]
> Pro tuto konfiguraci se vyžaduje jedna privátní zóna DNS. Vytvoření více zón se stejným názvem pro různé virtuální sítě by vyžadovalo ruční operace sloučení záznamů DNS.

> [!IMPORTANT]
> Pokud používáte privátní koncový bod v modelu hvězdicové lokality z jiného předplatného, znovu použijte stejnou privátní zónu DNS v centru.

V tomto scénáři je k dispozici topologie sítě [rozbočovače a paprsku](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) se sítěmi paprsků, které sdílejí společný privátní koncový bod, a všechny virtuální sítě paprsků jsou propojeny se stejnou privátní zónou DNS. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="Centrum a Paprskový s DNS poskytovanou službou Azure":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>Místní úlohy využívající službu DNS pro přeposílání

Aby bylo možné místní úlohy přeložit plně kvalifikovaný název domény privátního koncového bodu na privátní IP adresu, je nutné použít službu DNS pro překládání překladu [veřejné zóny DNS](#azure-services-dns-zone-configuration) služby Azure v Azure.

Následující scénář je vhodný pro místní síť, která má službu DNS pro přeposílání v Azure, což je zodpovědný za řešení všech dotazů DNS prostřednictvím služby pro překládání na úrovni serveru poskytované službou Azure [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md). 

> [!NOTE]
> V tomto scénáři se používá privátní zóna DNS, kterou doporučuje Azure SQL Database. Pro jiné služby můžete model upravit pomocí následujícího odkazu: [Konfigurace zóny DNS služeb Azure](#azure-services-dns-zone-configuration).

Ke správné konfiguraci potřebujete tyto prostředky:

- Místní síť
- Virtuální síť [připojená k místnímu](/azure/architecture/reference-architectures/hybrid-networking/) prostředí
- Služba přeposílání DNS nasazená v Azure 
- Zóny Privátní DNS [privatelink.Database.Windows.NET](../dns/private-dns-privatednszone.md) s [typem záznamu](../dns/dns-zones-records.md#record-types)
- Informace o privátním koncovém bodu (název záznamu a privátní IP adresa)

Následující diagram znázorňuje sekvenci překladu DNS z místní sítě, která používá službu DNS pro přeposílání v Azure, kde se řešení provádí v privátní zóně DNS [propojené s virtuální sítí](../dns/private-dns-virtual-network-links.md):

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="Místně pomocí Azure DNS":::

Tato konfigurace se dá prodloužit na místní síť, která už má řešení DNS. Místní řešení DNS je potřeba nakonfigurovat tak, aby přesměrovalo provoz DNS na Azure DNS prostřednictvím [podmíněného dodávaného](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) serveru, který odkazuje na službu pro překládání DNS nasazenou v Azure.

> [!NOTE]
> V tomto scénáři se používá privátní zóna DNS, kterou doporučuje Azure SQL Database. Pro jiné služby můžete model upravit pomocí následujícího odkazu: [Konfigurace zóny DNS služeb Azure](#azure-services-dns-zone-configuration) .

Ke správné konfiguraci potřebujete tyto prostředky:

- Místní síť s vlastním řešením DNS 
- Virtuální síť [připojená k místnímu](/azure/architecture/reference-architectures/hybrid-networking/) prostředí
- Služba přeposílání DNS nasazená v Azure
- Zóny Privátní DNS [privatelink.Database.Windows.NET](../dns/private-dns-privatednszone.md)  s [typem záznamu](../dns/dns-zones-records.md#record-types)
- Informace o privátním koncovém bodu (název záznamu a privátní IP adresa)

Následující diagram znázorňuje sekvenci překladu názvů DNS z místní sítě, která podmíněně přechází do Azure provoz DNS, kde se řešení provádí v privátní zóně DNS [propojené s virtuální sítí](../dns/private-dns-virtual-network-links.md).

> [!IMPORTANT]
> Podmíněné přesměrování je nutné provést v doporučené [službě pro předávání veřejné zóny DNS](#azure-services-dns-zone-configuration). Například: `database.windows.net` místo **privatelink**. Database.Windows.NET.

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="Místní přesměrování na Azure DNS":::

## <a name="virtual-network-and-on-premises-workloads-using-a-dns-forwarder"></a>Virtuální síť a místní úlohy pomocí služby DNS pro přeposílání

Pro obecný přístup, který je vhodný pro úlohy, které potřebují přístup k privátnímu koncovému bodu z virtuálních i místních sítí, musíte použít sdílenou službu DNS pro překládání, aby se zajistilo řešení [veřejné zóny DNS](#azure-services-dns-zone-configuration) služby Azure nasazené v Azure.

Následující scénář je vhodný pro místní síť, která má službu DNS pro přeposílání v Azure, a virtuální sítě, které potřebují přístup k privátnímu koncovému bodu umístěnému ve sdílené síti rozbočovače.  

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

Následující obrázek znázorňuje sekvenci překladu názvů DNS z místní a virtuální sítě, která používá službu DNS resolutioner nasazenou v Azure, kde je řešení prováděné privátní zónou DNS [propojenou s virtuální sítí](../dns/private-dns-virtual-network-links.md):

:::image type="content" source="media/private-endpoint-dns/hybrid-scenario.png" alt-text="Hybridní scénář":::

## <a name="next-steps"></a>Další kroky
- [Informace o privátních koncových bodech](private-endpoint-overview.md)