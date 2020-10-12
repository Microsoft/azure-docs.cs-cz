---
title: Co je privátní koncový bod Azure?
description: Seznamte se s privátním koncovým bodem Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: allensu
ms.openlocfilehash: 7456402605328592d4f5677767bcd985941173ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88639830"
---
# <a name="what-is-azure-private-endpoint"></a>Co je privátní koncový bod Azure?

Privátní koncový bod Azure je síťové rozhraní, které vás privátně a zabezpečeně připojí ke službám využívajícím službu Azure Private Link. Privátní koncový bod používá privátní IP adresu vaší virtuální sítě a tím vlastně přináší službu do vaší virtuální sítě. Služba může být služba Azure, například Azure Storage, Azure Cosmos DB, SQL atd. nebo vaše vlastní [Služba privátních odkazů](private-link-service-overview.md).
  
## <a name="private-endpoint-properties"></a>Vlastnosti privátního koncového bodu 
 Soukromý koncový bod určuje následující vlastnosti: 


|Vlastnost  |Popis |
|---------|---------|
|Název    |    Jedinečný název v rámci skupiny prostředků.      |
|Podsíť    |  Podsíť pro nasazení a přidělování privátních IP adres z virtuální sítě. Požadavky na podsíť najdete v části omezení v tomto článku.         |
|Prostředek privátního propojení    |   Prostředek privátního propojení pro připojení pomocí ID prostředku nebo aliasu ze seznamu dostupných typů. Pro veškerý provoz odeslaný do tohoto prostředku se vygeneruje jedinečný identifikátor sítě.       |
|Cílový podprostředek   |      Prostředek, který se má připojit. Každý typ prostředku privátního propojení má různé možnosti pro výběr na základě předvolby.    |
|Metoda schválení připojení    |  Automatické nebo ruční. V závislosti na oprávněních řízení přístupu na základě role (RBAC) může být váš soukromý koncový bod schválen automaticky. Pokud se pokusíte připojit k prostředku privátního propojení bez RBAC, použijte ruční metodu, která vlastníkovi prostředku povolí schválení připojení.        |
|Zpráva požadavku     |  Můžete zadat zprávu, aby požadovaná připojení byla schválena ručně. Tato zpráva se dá použít k identifikaci konkrétního požadavku.        |
|Stav připojení   |   Vlastnost jen pro čtení, která určuje, zda je privátní koncový bod aktivní. K odeslání provozu lze použít pouze privátní koncové body ve schváleném stavu. Další stavy k dispozici: <br>-**Schváleno**: připojení bylo automaticky nebo schváleno a je připraveno k použití.</br><br>-**Čeká na vyřízení**: připojení bylo vytvořeno ručně a uživatel čeká na schválení vlastníkem prostředku privátního odkazu.</br><br>-**Odmítnuto**: připojení bylo odmítnuto vlastníkem prostředku privátního odkazu.</br><br>-**Odpojeno**: připojení bylo odebráno vlastníkem prostředku privátního odkazu. Soukromý koncový bod se bude jednat o informativní a měl by se odstranit pro vyčištění. </br>|

Tady jsou některé klíčové podrobnosti o privátních koncových bodech: 
- Privátní koncový bod umožňuje připojení mezi spotřebiteli ze stejné virtuální sítě, v oblasti partnerských virtuální sítě, globálně vázaných virtuální sítě a místně pomocí [VPN](https://azure.microsoft.com/services/vpn-gateway/) nebo [Express Route](https://azure.microsoft.com/services/expressroute/) and Services využívajících soukromé odkazy.
 
- Připojení k síti můžou inicializovat jenom klienti připojující se k privátnímu koncovému bodu, poskytovatelé služeb nemají žádnou konfiguraci směrování, aby mohli iniciovat připojení k příjemcům služeb. Připojení se dají vytvořit jenom v jednom směru.

- Při vytváření privátního koncového bodu se pro životní cyklus prostředku vytvoří také síťové rozhraní jen pro čtení. Rozhraní jsou přiřazovány dynamicky privátních IP adres z podsítě, která je mapována na prostředek privátního propojení. Hodnota privátní IP adresy zůstane beze změny pro celý životní cyklus privátního koncového bodu.
 
- Privátní koncový bod musí být nasazený ve stejné oblasti jako virtuální síť. 
 
- Prostředek privátního propojení se dá nasadit v jiné oblasti, než je virtuální síť a soukromý koncový bod.
 
- Pomocí stejného prostředku privátního propojení lze vytvořit více privátních koncových bodů. V případě jedné sítě, která používá běžnou konfiguraci serveru DNS, doporučujeme použít jeden privátní koncový bod pro daný prostředek privátního propojení, aby nedocházelo k duplicitním položkám nebo konfliktům v překladu názvů DNS. 
 
- Ve stejné virtuální síti je možné vytvořit více privátních koncových bodů v rámci jedné nebo více podsítí. Existují omezení počtu privátních koncových bodů, které můžete v rámci předplatného vytvořit. Podrobnosti najdete v tématu [omezení Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).

- Odběr z prostředku privátního propojení musí být také zaregistrován pomocí poskytovatele prostředků obraťte. Network. Podrobnosti najdete v tématu [poskytovatelé prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

 
## <a name="private-link-resource"></a>Prostředek privátního propojení 
Prostředek privátního propojení je cílovým cílem daného privátního koncového bodu. Následuje seznam dostupných typů prostředků privátního propojení: 
 
|Název prostředku privátního propojení  |Typ prostředku   |Dílčí prostředky  |
|---------|---------|---------|
|**Služba privátního propojení** (vaše vlastní služba)   |  Microsoft. Network/privateLinkServices       | empty |
|**Azure Automation** |  Microsoft. Automation/automationAccounts | Webhook, DSCAndHybridWorker |
|**Azure SQL Database** | Microsoft. SQL/servery    |  SQL Server (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft. SQL/servery    |  SQL Server (sqlServer)        | 
|**Azure Storage**  | Microsoft. Storage/storageAccounts    |  BLOB (objekt blob, blob_secondary)<BR> Tabulka (tabulka, table_secondary)<BR> Queue (Queue, queue_secondary)<BR> Soubor (soubor, file_secondary)<BR> Web (web, web_secondary)        |
|**Azure Data Lake Storage Gen2**  | Microsoft. Storage/storageAccounts    |  BLOB (objekt blob, blob_secondary)<BR> Data Lake Gen2 systému souborů (DFS, dfs_secondary)       |
|**Azure Cosmos DB** | Microsoft. AzureCosmosDB/databaseAccounts    | SQL, MongoDB, Cassandra, Gremlin, tabulka|
|**Azure Database for PostgreSQL – jeden server** | Microsoft. DBforPostgreSQL/servery    | postgresqlServer |
|**Azure Database for MySQL** | Microsoft. DBforMySQL/servery    | mysqlServer |
|**Azure Database for MariaDB** | Microsoft. DBforMariaDB/servery    | mariadbServer |
|**Azure IoT Hub** | Microsoft. Devices/IotHubs    | iotHub |
|**Azure Key Vault** | Trezory a trezory Microsoft.    | Trezor |
|**Azure Kubernetes Service – rozhraní API pro Kubernetes** | Microsoft. ContainerService/managedClusters    | správa |
|**Azure Search** | Microsoft. Search/searchService| searchService|  
|**Azure Container Registry** | Microsoft. ContainerRegistry/Registry    | registry |
|**Azure App Configuration** | Microsoft. Appconfiguration/configurationStores    | configurationStores |
|**Azure Backup** | Microsoft. RecoveryServices/trezory    | Trezor |
|**Azure Event Hub** | Microsoft. EventHub/obory názvů    | namespace |
|**Azure Service Bus** | Microsoft. ServiceBus/obory názvů | namespace |
|**Azure Relay** | Microsoft. Relay/obory názvů | namespace |
|**Azure Event Grid** | Microsoft. EventGrid/témata    | téma |
|**Azure Event Grid** | Microsoft. EventGrid/domény    | doména |
|**WebApps Azure** | Microsoft. Web/weby    | místa |
|**Azure Machine Learning** | Microsoft. MachineLearningServices/pracovní prostory    | pracovní prostor |
|**SignalR** | Microsoft. SignalRService/Signaler    | signalR |
|**Azure Monitor** | Microsoft. Insights/privateLinkScopes    | azuremonitor |
|**Cognitive Services** | (Microsoft. Cognitiveservices Account/účty    | account |
|**Synchronizace souborů Azure** | Microsoft. StorageSync/storageSyncServices    | Buňk |
    
  

  
 
## <a name="network-security-of-private-endpoints"></a>Zabezpečení sítě privátních koncových bodů 
Při používání privátních koncových bodů pro služby Azure je provoz zabezpečený pro konkrétní prostředek privátního propojení. Platforma provede řízení přístupu, aby se ověřilo, že síťová připojení dosáhnou pouze zadaného prostředku privátního propojení. Pokud chcete získat přístup k dalším prostředkům v rámci stejné služby Azure, vyžadují se další privátní koncové body. 
 
Pro připojení k podporované službě Azure můžete úlohy zcela uzamknout z přístupu k veřejným koncovým bodům. Tento ovládací prvek zajišťuje další vrstvu zabezpečení sítě pro vaše prostředky tím, že poskytuje vestavěnou exfiltrace ochranu, která brání přístupu k dalším prostředkům hostovaným na stejné službě Azure. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Přístup k prostředku privátního propojení pomocí pracovního postupu schválení 
K prostředku privátního propojení se můžete připojit pomocí následujících metod schvalování připojení:
- **Automaticky** schváleno, pokud vlastníte nebo máte oprávnění ke konkrétnímu prostředku privátního propojení. Požadovaná oprávnění jsou založená na typu prostředku privátního propojení v následujícím formátu: Microsoft. \<Provider> /<resource_type>/privateEndpointConnectionApproval/action
- **Ruční** požadavek, pokud nemáte požadovaná oprávnění a přejete si požádat o přístup. Zahájí se schvalovací pracovní postup. Privátní koncový bod a další připojení privátního koncového bodu se vytvoří ve stavu Čeká na vyřízení. Za schválení připojení je zodpovědný vlastník prostředku služby Private Link. Po schválení je povolený privátní koncový bod pro normální odesílání provozu, jak je znázorněno v následujícím diagramu pracovního postupu schválení.  

![schválení pracovního postupu](media/private-endpoint-overview/private-link-paas-workflow.png)
 
Vlastník prostředku privátního propojení může provést následující akce přes připojení privátního koncového bodu: 
- Zkontrolujte podrobnosti o všech připojeních privátního koncového bodu. 
- Schvalte připojení privátního koncového bodu. Odpovídající privátní koncový bod bude povolen k odeslání provozu do prostředku privátního propojení. 
- Odmítne připojení privátního koncového bodu. Odpovídající soukromý koncový bod bude aktualizován tak, aby odrážel stav.
- Odstraňte připojení privátního koncového bodu v libovolném stavu. Odpovídající privátní koncový bod bude aktualizován pomocí odpojeného stavu, aby odrážel akci, vlastník privátního koncového bodu může v tomto okamžiku odstranit pouze prostředek. 
 
> [!NOTE]
> Pouze privátní koncový bod ve schváleném stavu může odesílat provoz do daného prostředku privátního propojení. 

### <a name="connecting-using-alias"></a>Připojování pomocí aliasu
Alias je jedinečný moniker, který se generuje, když vlastník služby vytvoří službu privátního propojení za standardním nástrojem pro vyrovnávání zatížení. Vlastník služby může tento alias sdílet se svými spotřebiteli offline. Příjemci můžou požádat o připojení ke službě privátního propojení buď pomocí identifikátoru URI prostředku, nebo aliasu. Pokud se chcete připojit pomocí aliasu, musíte vytvořit privátní koncový bod pomocí metody ručního schválení připojení. Pro použití metody schvalování ručního připojení nastavte parametr ruční požadavek na hodnotu true během vytváření toku privátního koncového bodu. Podrobnosti najdete v [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0) a [AZ Network Private-Endpoint Create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) . 

## <a name="dns-configuration"></a>Konfigurace DNS 
Při připojování k prostředku privátního propojení pomocí plně kvalifikovaného názvu domény (FQDN) jako součásti připojovacího řetězce je důležité správně nakonfigurovat nastavení DNS tak, aby se přeložilo na přidělenou privátní IP adresu. Existující služby Azure už můžou mít konfiguraci DNS, která se má použít při připojování přes Veřejný koncový bod. To je nutné přepsat pro připojení pomocí privátního koncového bodu. 
 
Síťové rozhraní přidružené k privátnímu koncovému bodu obsahuje úplnou sadu informací potřebných ke konfiguraci DNS, včetně plně kvalifikovaného názvu domény a privátních IP adres přidělených pro daný prostředek privátního propojení. 

Podrobné informace o osvědčených postupech a doporučeních pro konfiguraci DNS pro privátní koncové body najdete v [článku Konfigurace DNS privátního koncového bodu](private-endpoint-dns.md).



 
## <a name="limitations"></a>Omezení
 
Následující tabulka obsahuje seznam známých omezení při použití privátních koncových bodů: 


|Omezení |Popis |Omezení rizik  |
|---------|---------|---------|
|Pravidla skupiny zabezpečení sítě (NSG) a uživatelsky definované trasy se nevztahují na soukromý koncový bod.    |NSG se nepodporuje u privátních koncových bodů. V případě, že k podsítím obsahujícím soukromý koncový bod může být přidruženo NSG, pravidla nebudou platná pro přenosy zpracovávané privátním koncovým bodem. K nasazení privátních koncových bodů v podsíti je nutné, aby bylo [vynucování zásad sítě zakázané](disable-private-endpoint-network-policy.md) . NSG se pořád vynutil na jiných úlohách hostovaných ve stejné podsíti. Při směrování v každé podsíti klienta bude použita předpona/32, změna výchozího chování směrování vyžaduje podobný UDR  | Řízení provozu pomocí pravidel NSG pro odchozí přenosy na zdrojových klientech. Nasaďte jednotlivé trasy s předponou/32, abyste mohli přepsat trasy privátních koncových bodů. Protokoly toku NSG a informace o monitorování pro odchozí připojení se pořád podporují a dají se použít.        |


## <a name="next-steps"></a>Další kroky
- [Vytvoření privátního koncového bodu pro SQL Database pomocí portálu ](create-private-endpoint-portal.md)
- [Vytvoření privátního koncového bodu pro SQL Database pomocí prostředí PowerShell ](create-private-endpoint-powershell.md)
- [Vytvoření privátního koncového bodu pro SQL Database pomocí rozhraní příkazového řádku ](create-private-endpoint-cli.md)
- [Vytvoření privátního koncového bodu pro účet úložiště pomocí portálu ](create-private-endpoint-storage-portal.md)
- [Vytvoření privátního koncového bodu pro účet Azure Cosmos pomocí portálu ](../cosmos-db/how-to-configure-private-endpoints.md)
- [Vytvoření vlastní služby privátního propojení pomocí Azure PowerShell](create-private-link-service-powershell.md)
- [Vytvoření vlastního privátního odkazu pro Azure Database for PostgreSQL s jedním serverem pomocí portálu](../postgresql/howto-configure-privatelink-portal.md)
- [Vytvoření vlastního privátního odkazu pro Azure Database for PostgreSQL s jedním serverem pomocí rozhraní příkazového řádku](../postgresql/howto-configure-privatelink-cli.md)
- [Vytvoření vlastního privátního odkazu pro Azure Database for MySQL pomocí portálu](../mysql/howto-configure-privatelink-portal.md)
- [Vytvoření vlastního privátního odkazu pro Azure Database for MySQL pomocí rozhraní příkazového řádku](../mysql/howto-configure-privatelink-cli.md)
- [Vytvoření vlastního privátního odkazu pro Azure Database for MariaDB pomocí portálu](../mariadb/howto-configure-privatelink-portal.md)
- [Vytvoření vlastního privátního odkazu pro Azure Database for MariaDB pomocí rozhraní příkazového řádku](../mariadb/howto-configure-privatelink-cli.md)
