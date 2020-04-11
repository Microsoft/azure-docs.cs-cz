---
title: Co je azure privátní koncový bod?
description: Další informace o Azure Private Endpoint
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: d10b6c52310da3d799a7fe78c83284960318f82e
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115250"
---
# <a name="what-is-azure-private-endpoint"></a>Co je Azure Private Endpoint?

Privátní koncový bod Azure je síťové rozhraní, které vás privátně a zabezpečeně připojí ke službám využívajícím službu Azure Private Link. Privátní Koncový bod používá privátní IP adresu z vaší virtuální sítě, efektivně uvedení služby do virtuální sítě. Služba může být služba Azure, jako je Azure Storage, Azure Cosmos [Private Link Service](private-link-service-overview.md)DB, SQL atd.
  
## <a name="private-endpoint-properties"></a>Vlastnosti soukromého koncového bodu 
 Privátní koncový bod určuje následující vlastnosti: 


|Vlastnost  |Popis |
|---------|---------|
|Name (Název)    |    Jedinečný název v rámci skupiny prostředků.      |
|Podsíť    |  Podsíť pro nasazení a přidělení privátních IP adres z virtuální sítě. Požadavky na podsíť naleznete v části Omezení v tomto článku.         |
|Soukromý prostředek propojení    |   Prostředek privátního propojení pro připojení pomocí ID prostředku nebo aliasu ze seznamu dostupných typů. Pro veškerý provoz odeslaný do tohoto prostředku bude vygenerován jedinečný identifikátor sítě.       |
|Cílový dílčí zdroj   |      Dílčí prostředek pro připojení. Každý typ prostředku soukromého propojení má různé možnosti pro výběr na základě předvoleb.    |
|Metoda schvalování připojení    |  Automatické nebo manuální. Na základě oprávnění řízení přístupu na základě rolí (RBAC) může být váš soukromý koncový bod automaticky schválen. Pokud se pokusíte připojit k prostředku privátní ho propojení bez RBAC, použijte ruční metodu, aby vlastník prostředku schválit připojení.        |
|Požádat o zprávu     |  Můžete zadat zprávu pro požadovaná připojení, která mají být schválena ručně. Tuto zprávu lze použít k identifikaci konkrétní požadavek.        |
|Stav připojení   |   Vlastnost jen pro čtení, která určuje, zda je aktivní soukromý koncový bod. K odesílání přenosů lze použít pouze soukromé koncové body ve schváleném stavu. K dispozici jsou další stavy: <br>-**Schváleno**: Připojení bylo automaticky nebo ručně schváleno a je připraveno k použití.</br><br>-**Čeká na vyřízení**: Připojení bylo vytvořeno ručně a čeká na schválení vlastníkem prostředku soukromého propojení.</br><br>-**Odmítnuto**: Připojení bylo odmítnuto vlastníkem prostředku privátního propojení.</br><br>-**Odpojeno**: Připojení bylo odebráno vlastníkem prostředku privátního propojení. Soukromý koncový bod se stane informativní a by měly být odstraněny pro vyčištění. </br>|

Tady jsou některé klíčové podrobnosti o soukromých koncových bodech: 
- Privátní koncový bod umožňuje připojení mezi příjemci ze stejné virtuální sítě, místně partnerských virtuálních sítí, globálně partnerských virtuálních sítí a v prostorách pomocí [VPN](https://azure.microsoft.com/services/vpn-gateway/) nebo [expresní trasy](https://azure.microsoft.com/services/expressroute/) a služeb využívajících private link.
 
- Při vytváření privátní koncový bod síťové rozhraní je také vytvořen pro životní cyklus prostředku. Rozhraní je přiřazena privátní IP adresa z podsítě, která se mapuje na službu Private Link Service.
 
- Soukromý koncový bod musí být nasazený ve stejné oblasti jako virtuální síť. 
 
- Prostředek privátní ho propojení lze nasadit v jiné oblasti než virtuální síť a soukromý koncový bod.
 
- Více soukromých koncových bodů lze vytvořit pomocí stejného prostředku privátní propojení. Pro jednu síť používající společnou konfiguraci serveru DNS je doporučeno použít pro daný prostředek privátního propojení jeden soukromý koncový bod, aby se zabránilo duplicitním položkám nebo konfliktům v překladu DNS. 
 
- Více soukromých koncových bodů lze vytvořit ve stejné nebo různé podsítě v rámci stejné virtuální sítě. Počet soukromých koncových bodů, které můžete vytvořit v předplatném, je limitován. Podrobnosti najdete v [tématu Limity Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


 
## <a name="private-link-resource"></a>Prostředek privátního propojení 
Prostředek privátního propojení je cílový cíl daného soukromého koncového bodu. Následuje seznam dostupných typů prostředků privátního propojení: 
 
|Název prostředku privátního propojení  |Typ prostředku   |Dílčí zdroje  |
|---------|---------|---------|
|**Private Link Service** (Vaše vlastní služba)   |  Microsoft.Network/privateLinkServices       | empty |
|**Azure SQL Database** | Microsoft.SQL/servery    |  Sql Server (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft.SQL/servery    |  Sql Server (sqlServer)        | 
|**Azure Storage**  | Microsoft.Storage/storageAccounts    |  Blob (blob, blob_secondary)<BR> Tabulka (tabulka, table_secondary)<BR> Fronta (fronta, queue_secondary)<BR> Soubor (soubor, file_secondary)<BR> Web (web, web_secondary)        |
|**Azure Data Lake Storage Gen2**  | Microsoft.Storage/storageAccounts    |  Blob (blob, blob_secondary)<BR> Souborový systém Datové ho jezera Gen2 (dfs, dfs_secondary)       |
|**Azure Cosmos DB** | Microsoft.AzureCosmosDB/databaseAccounts | Sql, MongoDB, Cassandra, Gremlin, Tabulka|
|**Databáze Azure pro postgreSQL –jeden server** | Microsoft.DBforPostgreSQL/servery   | postgresqlServer |
|**Azure Database for MySQL** | Microsoft.DBforMySQL/servery    | mysqlServer |
|**Azure Database for MariaDB** | Microsoft.DBforMariaDB/servery    | mariadbServer |
|**Azure Key Vault** | Úložiště Microsoft.KeyVault/trezory    | Trezor |
|**Služba Azure Kubernetes – rozhraní API Kubernetes** | Microsoft.ContainerService/spravované clustery | managedCluster |
|**Azure Search** | Microsoft.Search/searchService| služba searchService|  
|**Registr kontejnerů Azure** | Microsoft.ContainerRegistry/registry  | registry |
|**Azure App Configuration** | Microsoft.Appconfiguration/configurationStores   | configurationStore |
|**Azure Backup** | Microsoft.RecoveryServices/trezory   | Trezor |
|**Azure Event Hub** | Obory názvů Microsoft.EventHub/Namespaces    | namespace |
|**Azure Service Bus** | Obory názvů Microsoft.ServiceBus | namespace |
|**Azure Relay** | Obory názvů Microsoft.Relay/Namespaces | namespace |
|**Azure Event Grid** | Microsoft.EventGrid/témata  | téma |
|**Azure Event Grid** | Microsoft.EventGrid/domény | doména |
|**Azure WebApps** | Web/weby společnosti Microsoft    | Stránky |
|**Azure Machine Learning** | Microsoft.MachineLearningServices/pracovní prostory  | Pracovní prostor |
  
 
## <a name="network-security-of-private-endpoints"></a>Zabezpečení sítě privátních koncových bodů 
Při použití privátní koncové body pro služby Azure, provoz je zabezpečený na konkrétní prostředek privátní propojení. Platforma provádí řízení přístupu k ověření síťových připojení, která se dostanou pouze k určenému prostředku privátního propojení. Pro přístup k dalším prostředkům v rámci stejné služby Azure jsou vyžadovány další soukromé koncové body. 
 
Můžete úplně uzamknout vaše úlohy z přístupu k veřejným koncovým bodům pro připojení k podporované službě Azure. Tento ovládací prvek poskytuje další vrstvu zabezpečení sítě pro vaše prostředky tím, že poskytuje vestavěnou ochranu proti exfiltraci, která zabraňuje přístupu k jiným prostředkům hostovaným ve stejné službě Azure. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Přístup k prostředku privátního propojení pomocí pracovního postupu schválení 
K prostředku privátního propojení se můžete připojit pomocí následujících metod schvalování připojení:
- **Automaticky** schváleno, pokud vlastníte nebo máte oprávnění k určitému prostředku privátního propojení. Požadované oprávnění je založeno na typu prostředku privátního odkazu v následujícím formátu: Microsoft. \<Zprostředkovatel>/<resource_type>/privateEndpointConnectionApproval/action
- **Ruční** požadavek, pokud nemáte požadované oprávnění a chcete požádat o přístup. Bude zahájen pracovní postup schválení. Privátní koncový bod a další připojení privátního koncového bodu se vytvoří ve stavu Čeká na vyřízení. Za schválení připojení je zodpovědný vlastník prostředku služby Private Link. Po schválení je soukromý koncový bod povolen pro normální odesílání provozu, jak je znázorněno v následujícím diagramu pracovního postupu schválení.  

![schválení pracovního postupu](media/private-endpoint-overview/private-link-paas-workflow.png)
 
Vlastník prostředku privátního propojení může provádět následující akce přes připojení soukromého koncového bodu: 
- Zkontrolujte podrobnosti o všech soukromých koncových bodů. 
- Schválení připojení privátního koncového bodu. Odpovídající soukromý koncový bod bude povoleno odesílat přenosy do prostředku privátního propojení. 
- Odmítnout připojení soukromého koncového bodu. Odpovídající soukromý koncový bod bude aktualizován tak, aby odrážel stav.
- Odstraňte privátní koncové připojení v libovolném stavu. Odpovídající soukromý koncový bod bude aktualizován odpojený stav, aby odrážely akce, vlastník soukromého koncového bodu můžete odstranit pouze prostředek v tomto okamžiku. 
 
> [!NOTE]
> Pouze soukromý koncový bod ve schváleném stavu může odesílat provoz na daný prostředek soukromého propojení. 

### <a name="connecting-using-alias"></a>Připojení pomocí aliasu
Alias je jedinečný zástupný název, který je generován při vlastníka služby vytvoří službu soukromé propojení za standardní vyrovnávání zatížení. Vlastník služby může sdílet tento alias se svými spotřebiteli offline. Spotřebitelé mohou požádat o připojení ke službě privátního propojení pomocí identifikátoru URI prostředku nebo aliasu. Pokud se chcete připojit pomocí aliasu, musíte vytvořit soukromý koncový bod pomocí metody ručního schválení připojení. Pro použití metody ručního schvalování připojení nastavte parametr ručního požadavku na hodnotu true během toku vytvoření soukromého koncového bodu. Podívejte se na [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0) a [az sítě privátní koncový bod vytvořit](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) podrobnosti. 

## <a name="dns-configuration"></a>Konfigurace DNS 
Při připojování k prostředku privátního propojení pomocí plně kvalifikovaného názvu domény (FQDN) jako součásti připojovacího řetězce je důležité správně nakonfigurovat nastavení DNS tak, aby se vyřešilo na přidělenou privátní IP adresu. Existující služby Azure už možná mají konfiguraci DNS, kterou se dá použít při připojování přes veřejný koncový bod. To musí být přepsáno pro připojení pomocí privátní koncový bod. 
 
Síťové rozhraní přidružené k privátnímu koncovému bodu obsahuje úplnou sadu informací potřebných ke konfiguraci služby DNS, včetně úplných adres Domény a privátních IP adres přidělených pro daný prostředek soukromého propojení. 
 
Ke konfiguraci nastavení DNS pro soukromé koncové body můžete použít následující možnosti: 
- **Použijte soubor hostitele (doporučeno pouze pro testování).** Hostitelský soubor ve virtuálním počítači můžete použít k přepsání DNS.  
- **Použijte soukromou zónu DNS**. Privátní zóny DNS můžete přepsat rozlišení DNS pro daný soukromý koncový bod. Privátní zónu DNS lze propojit s vaší virtuální sítí a vyřešit tak konkrétní domény.
- **Použijte vlastní dns server**. Vlastní server DNS můžete použít k přepsání překladu DNS pro daný prostředek privátního propojení. Pokud je váš [server DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) hostován ve virtuální síti, můžete vytvořit pravidlo pro předávání DNS, které pomocí privátní zóny DNS zjednoduší konfiguraci pro všechny prostředky privátního propojení.
 
> [!IMPORTANT]
> Není doporučeno přepsat zónu, která se aktivně používá k řešení veřejných koncových bodů. Připojení k prostředkům nebude možné správně vyřešit bez dns předávání veřejné DNS. Chcete-li se vyhnout problémům, vytvořte jiný název domény nebo postupujte podle navrhovaného názvu pro každou službu níže. 
 
Pro služby Azure použijte názvy zón, jak je popsáno v následující tabulce:

|Typ prostředku privátního propojení   |Dílčí zdroj  |Název zóny  |
|---------|---------|---------|
|SQL DB (Microsoft.SQL/servery)    |  Sql Server (sqlServer)        |   privatelink.database.windows.net       |
|Azure Synapse Analytics (Microsoft.Sql/servery)    |  Sql Server (sqlServer)        | privatelink.database.windows.net |
|Účet úložiště (Microsoft.Storage/storageAccounts)    |  Blob (blob, blob_secondary)        |    privatelink.blob.core.windows.net      |
|Účet úložiště (Microsoft.Storage/storageAccounts)    |    Tabulka (tabulka, table_secondary)      |   privatelink.table.core.windows.net       |
|Účet úložiště (Microsoft.Storage/storageAccounts)    |    Fronta (fronta, queue_secondary)     |   privatelink.queue.core.windows.net       |
|Účet úložiště (Microsoft.Storage/storageAccounts)   |    Soubor (soubor, file_secondary)      |    privatelink.file.core.windows.net      |
|Účet úložiště (Microsoft.Storage/storageAccounts)     |  Web (web, web_secondary)        |    privatelink.web.core.windows.net      |
|Souborový systém datového jezera Gen2 (Microsoft.Storage/storageAccounts)  |  Souborový systém Datové ho jezera Gen2 (dfs, dfs_secondary)        |     privatelink.dfs.core.windows.net     |
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|SQL |privatelink.documents.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|MongoDB |privatelink.mongo.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Cassandra|privatelink.cassandra.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Gremlin |privatelink.gremlin.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.AzureCosmosDB/databaseAccounts)|Table|privatelink.table.cosmos.azure.com|
|Databáze Azure pro PostgreSQL – jeden server (Microsoft.DBforPostgreSQL/servery)|postgresqlServer|privatelink.postgres.database.azure.com|
|Databáze Azure pro MySQL (Microsoft.DBforMySQL/servery)|mysqlServer|privatelink.mysql.database.azure.com|
|Databáze Azure pro MariaDB (Microsoft.DBforMariaDB/servery)|mariadbServer|privatelink.mariadb.database.azure.com|
|Azure Key Vault (Microsoft.KeyVault/trezory)|Trezor|privatelink.vaultcore.azure.net|
|Služba Azure Kubernetes – rozhraní API Kubernetes (Microsoft.ContainerService/managedClusters) | managedCluster | {guid}.privatelink. <region>.azmk8s.io|
|Azure Search (Microsoft.Search/searchServices)|služba searchService|privatelink.search.windows.net|   
|Azure Container Registry (Microsoft.ContainerRegistry/registry) | registry | privatelink.azurecr.io |
|Konfigurace aplikace Azure (Microsoft.Appconfiguration/configurationStores)| configurationStore | privatelink.azconfig.io|
|Zálohování Azure (Microsoft.RecoveryServices/trezory)| Trezor |privatelink. {region}.backup.windowsazure.com|
|Azure Event Hub (Microsoft.EventHub/obory názvů)| namespace |privatelink.servicebus.windows.net|
|Azure Service Bus (Microsoft.ServiceBus/obory názvů) | namespace |privatelink.servicebus.windows.net|
|Azure Relay (Microsoft.Relay/obory názvů) | namespace |privatelink.servicebus.windows.net|
|Azure Event Grid (Microsoft.EventGrid/témata)   | téma | Téma. {region}.privatelink.eventgrid.azure.net|
|Azure Event Grid (Microsoft.EventGrid/domény) | doména | Domény. {region}.privatelink.eventgrid.azure.net |
|Azure WebApps (Microsoft.Web/weby)    | Stránky | privatelink.azurewebsites.net |
|Azure Machine Learning (Microsoft.MachineLearningServices/pracovní prostory)   | Pracovní prostor | privatelink.api.azureml.ms |
 
Azure vytvoří kanonický název DNS záznam (CNAME) na veřejné DNS přesměrovat rozlišení na navrhované názvy domén. Budete moct přepsat rozlišení s privátní IP adresu vašich soukromých koncových bodů. 
 
Vaše aplikace nemusí měnit adresu URL připojení. Při pokusu o vyřešení pomocí veřejnédns, DNS server bude nyní vyřešit na vaše soukromé koncové body. Tento proces nemá vliv na vaše aplikace. 
 
## <a name="limitations"></a>Omezení
 
Následující tabulka obsahuje seznam známých omezení při použití privátních koncových bodů: 


|Omezení |Popis |Omezení rizik  |
|---------|---------|---------|
|Pravidla skupiny zabezpečení sítě (NSG) a uživatelem definované trasy se nevztahují na soukromý koncový bod    |NSG není podporována na soukromé koncové body. Zatímco podsítě obsahující soukromý koncový bod může mít nsg přidružené k němu, pravidla nebudou účinná na provoz zpracovaný privátní koncový bod. Chcete-li nasadit soukromé koncové body v podsíti, musíte mít [zakázáno vynucení zásad sítě.](disable-private-endpoint-network-policy.md) Skupina síťových dno je stále vynucena v jiných úlohách hostovaných ve stejné podsíti. Trasy v libovolné podsíti klienta budou používat předponu /32, změna výchozího chování směrování vyžaduje podobné UDR  | Řízení provozu pomocí pravidel sítě sítě pro odchozí provoz na zdrojových klientech. Nasazení jednotlivých tras s předponou /32 k přepsání privátních tras koncového bodu. Protokoly toku nsg a informace o monitorování odchozích připojení jsou stále podporovány a lze je použít.        |


## <a name="next-steps"></a>Další kroky
- [Vytvoření privátního koncového bodu pro databázový server SQL pomocí portálu](create-private-endpoint-portal.md)
- [Vytvoření privátního koncového bodu pro databázový server SQL pomocí prostředí PowerShell](create-private-endpoint-powershell.md)
- [Vytvoření privátního koncového bodu pro databázový server SQL pomocí příkazového příkazu](create-private-endpoint-cli.md)
- [Vytvoření účtu privátního koncového bodu pro úložiště pomocí portálu](create-private-endpoint-storage-portal.md)
- [Vytvoření privátního koncového bodu pro účet Azure Cosmos pomocí portálu](../cosmos-db/how-to-configure-private-endpoints.md)
- [Vytvoření vlastní služby Privátního propojení pomocí Azure PowerShellu](create-private-link-service-powershell.md)
- [Vytvoření vlastního privátního propojení pro databázi Azure pro PostgreSQL – jeden server pomocí portálu](../postgresql/howto-configure-privatelink-portal.md)
- [Vytvoření vlastního privátního propojení pro azure databázi pro PostgreSQL – jeden server pomocí příkazového příkazového příkazu](../postgresql/howto-configure-privatelink-cli.md)
- [Vytvoření vlastního privátního odkazu pro azure databázi pro MySQL pomocí portálu](../mysql/howto-configure-privatelink-portal.md)
- [Vytvoření vlastního privátního odkazu pro azure databázi pro MySQL pomocí příkazového příkazového příkazu](../mysql/howto-configure-privatelink-cli.md)
- [Vytvoření vlastního privátního odkazu pro databázi Azure pro MariaDB pomocí portálu](../mariadb/howto-configure-privatelink-portal.md)
- [Vytvoření vlastního privátního propojení pro azure databázi pro MariaDB pomocí příkazového příkazového příkazu](../mariadb/howto-configure-privatelink-cli.md)
