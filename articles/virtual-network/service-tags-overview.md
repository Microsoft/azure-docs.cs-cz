---
title: Přehled značek služeb Azure
titlesuffix: Azure Virtual Network
description: Přečtěte si o značkách služby. Značky služeb pomůžou minimalizovat složitost vytváření pravidel zabezpečení.
services: virtual-network
documentationcenter: na
author: allegradomel
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 41db671e4ab76dc56dc2c01f4852640acfe3fd83
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389735"
---
# <a name="virtual-network-service-tags"></a>Značky služby virtuální sítě
<a name="network-service-tags"></a>

Značka služby představuje skupinu předpon IP adres z dané služby Azure. Společnost Microsoft spravuje předpony adres, které jsou zahrnuté ve značce služby, a automaticky aktualizuje značku služby, protože se mění adresy. tím se minimalizuje složitost častých aktualizací pravidel zabezpečení sítě.

Pomocí značek služeb můžete definovat řízení přístupu k síti pro [skupiny zabezpečení sítě](./network-security-groups-overview.md#security-rules) nebo [Azure firewall](../firewall/service-tags.md). Při vytváření pravidel zabezpečení používejte značky služby místo konkrétních IP adres. Zadáním názvu značky služby, jako je například **ApiManagement**, v příslušném *zdrojovém* nebo *cílovém* poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu.

Pomocí značek služeb můžete zajistit izolaci sítě a chránit prostředky Azure před obecným internetem při přístupu ke službám Azure, které mají veřejné koncové body. Vytvořte pravidla skupiny zabezpečení příchozích a odchozích sítí pro zamítnutí provozu do a z **Internetu** a umožněte přenos do/z **AzureCloud** nebo jiných [dostupných značek služeb](#available-service-tags) pro konkrétní služby Azure.

![Izolace sítě služeb Azure pomocí značek služeb](./media/service-tags-overview/service_tags.png)

## <a name="available-service-tags"></a>Dostupné značky služeb
Následující tabulka obsahuje všechny značky služeb, které jsou k dispozici pro použití v pravidlech [skupiny zabezpečení sítě](./network-security-groups-overview.md#security-rules) .

Sloupce označují, zda značka:

- Je vhodný pro pravidla, která se vztahují na příchozí nebo odchozí provoz.
- Podporuje [regionální](https://azure.microsoft.com/regions) rozsah.
- Je použitelný v pravidlech [Azure firewall](../firewall/service-tags.md) .

Ve výchozím nastavení značky služby odráží rozsahy celého cloudu. Některé značky služby také umožňují podrobnější kontrolu tím, že omezují odpovídající rozsahy IP adres na určitou oblast. Například **úložiště** značek služby představuje Azure Storage pro celý Cloud, ale **Storage. WestUS** zúží rozsah jenom na rozsahy IP adres úložiště z oblasti WestUS. V následující tabulce je uvedeno, zda každá značka služby podporuje tento regionální rozsah.  

| Značka | Účel | Dá se použít příchozí nebo odchozí? | Je možné je rozregionovat? | Lze použít s Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|
| **ActionGroup** | Skupina akcí. | Příchozí | No | No |
| **Správa rozhraní API** | Provoz správy pro nasazení ve službě Azure API Management vyhrazena. <br/><br/>*Poznámka:* Tato značka představuje koncový bod služby Azure API Management pro rovinu ovládacího prvku na oblast. To zákazníkům umožňuje provádět operace správy na rozhraních API, operacích, zásadách a NamedValues nakonfigurovaných ve službě API Management.  | Příchozí | Yes | Yes |
| **ApplicationInsightsAvailability** | Application Insights dostupnost. | Příchozí | No | No |
| **AppConfiguration** | Konfigurace aplikace | Odchozí | No | No |
| **AppService**    | Azure App Service Tato značka se doporučuje pro odchozí pravidla zabezpečení pro webové aplikace a aplikace Function App.  | Odchozí | Yes | Yes |
| **AppServiceManagement** | Provoz správy pro nasazení vyhrazená pro App Service Environment. | Obojí | No | Yes |
| **Azureactivedirectory selhala** | Azure Active Directory | Odchozí | No | Yes |
| **AzureActiveDirectoryDomainServices** | Provoz správy pro nasazení vyhrazená pro Azure Active Directory Domain Services. | Obojí | No | Yes |
| **AzureAdvancedThreatProtection** | Rozšířená ochrana před internetovými útoky Azure. | Odchozí | No | No |
| **AzureArcInfrastructure** | Servery s podporou ARC Azure, Kubernetes a konfigurace hosta s povoleným přenosem.<br/><br/>*Poznámka:* Tato značka má závislost na značkách **azureactivedirectory selhala**,**AzureTrafficManager** a **AzureResourceManager** . *Tato značka se v tuto chvíli nedá nakonfigurovat přes Azure Portal*.| Odchozí | No | Yes |
| **AzureBackup** |Azure Backup.<br/><br/>*Poznámka:* Tato značka má závislost na značkách **úložiště** a **azureactivedirectory selhala** . | Odchozí | No | Yes |
| **AzureBotService** | Azure Bot Service. | Odchozí | No | No |
| **AzureCloud** | Všechny [veřejné IP adresy Datacenter](https://www.microsoft.com/download/details.aspx?id=56519) | Odchozí | Yes | Yes |
| **AzureCognitiveSearch** | Kognitivní hledání Azure. <br/><br/>Tato značka nebo IP adresy, na které se vztahuje tato značka, se dají použít k udělení zabezpečeného přístupu indexerům k datovým zdrojům. Další podrobnosti najdete v [dokumentaci k připojení indexeru](../search/search-indexer-troubleshooting.md#connection-errors) . <br/><br/> *Poznámka*: IP adresa vyhledávací služby není zahrnutá v seznamu ROZSAHŮ IP adres pro tuto značku služby a musí se **taky přidat** do brány firewall IP zdrojů dat. | Příchozí | No | No |
| **AzureConnectors** | Tato značka představuje IP adresy používané pro spravované konektory, které umožňují příchozí zpětná volání Webhooku službě Azure Logic Apps a odchozí volání jejich příslušných služeb, například Azure Storage nebo Azure Event Hubs. | Příchozí/odchozí | Yes | Yes |
| **AzureContainerRegistry** | Azure Container Registry. | Odchozí | Yes | Yes |
| **AzureCosmosDB** | Azure Cosmos DB. | Odchozí | Yes | Yes |
| **AzureDatabricks** | Azure Databricks. | Obojí | No | No |
| **AzureDataExplorerManagement** | Správa Azure Průzkumník dat. | Příchozí | No | No |
| **AzureDataLake** | Azure Data Lake Storage Gen1. | Odchozí | No | Yes |
| **AzureDevSpaces** | Azure Dev Spaces. | Odchozí | No | No |
| **AzureDevOps** | Operace vývoje Azure<br/><br/>*Poznámka: Tato značka se v tuto chvíli nedá nakonfigurovat přes Azure Portal.*| Příchozí | No | Yes |
| **AzureDigitalTwins** | Digitální vlákna Azure.<br/><br/>*Poznámka:* Tato značka nebo IP adresy, na které se vztahuje tato značka, se dají použít k omezení přístupu k koncovým bodům nakonfigurovaným pro trasy událostí. *Tato značka se v tuto chvíli nedá nakonfigurovat přes Azure Portal.* | Příchozí | No | Yes |
| **AzureEventGrid** | Azure Event Grid. | Obojí | No | No |
| **AzureFrontDoor. front-end** <br/> **AzureFrontDoor. back-end** <br/> **AzureFrontDoor.FirstParty**  | Přední dveře Azure. | Obojí | No | No |
| **AzureInformationProtection** | Azure Information Protection.<br/><br/>*Poznámka:* Tato značka má závislost na značkách **azureactivedirectory selhala**, **AzureFrontDoor. front.** a **AzureFrontDoor. FirstParty** . | Odchozí | No | No |
| **AzureIoTHub** | Azure IoT Hub | Odchozí | No | No |
| **AzureKeyVault** | Azure Key Vault<br/><br/>*Poznámka:* Tato značka má závislost na značce **azureactivedirectory selhala** . | Odchozí | Yes | Yes |
| **AzureLoadBalancer** | Nástroj pro vyrovnávání zatížení infrastruktury Azure. Značka se přeloží na [virtuální IP adresu hostitele](./network-security-groups-overview.md#azure-platform-considerations) (168.63.129.16), ve které vznikly sondy stavu Azure. To zahrnuje jenom přenos dat sondy, ne skutečný provoz do back-endu prostředku. Pokud nepoužíváte Azure Load Balancer, můžete toto pravidlo přepsat. | Obojí | No | No |
| **AzureMachineLearning** | Azure Machine Learning. | Obojí | No | Yes |
| **AzureMonitor** | Log Analytics, Application Insights, AzMon a vlastní metriky (koncové body 4Gigový).<br/><br/>*Poznámka:* U Log Analytics má tato značka závislost na značce **úložiště** . | Odchozí | No | Yes |
| **AzureOpenDatasets** | Otevřené datové sady Azure<br/><br/>*Poznámka:* Tato značka má závislost na značce **AzureFrontDoor. front** a **Storage** . | Odchozí | No | No |
| **AzurePlatformDNS** | Služba DNS základní infrastruktury (výchozí).<br/><br>Pomocí této značky můžete zakázat výchozí DNS. Při použití této značky buďte opatrní. Doporučujeme, abyste si přečetli [informace o platformě Azure](./network-security-groups-overview.md#azure-platform-considerations). Doporučujeme také, abyste před použitím této značky provedli testování. | Odchozí | No | No |
| **AzurePlatformIMDS** | Azure Instance Metadata Service (IMDS), což je základní služba infrastruktury.<br/><br/>Pomocí této značky můžete zakázat výchozí IMDS. Při použití této značky buďte opatrní. Doporučujeme, abyste si přečetli [informace o platformě Azure](./network-security-groups-overview.md#azure-platform-considerations). Doporučujeme také, abyste před použitím této značky provedli testování. | Odchozí | No | No |
| **AzurePlatformLKM** | Licencování Windows nebo služba správy klíčů.<br/><br/>Pomocí této značky můžete zakázat výchozí hodnoty licencování. Při použití této značky buďte opatrní. Doporučujeme, abyste si přečetli [informace o platformě Azure](./network-security-groups-overview.md#azure-platform-considerations).  Doporučujeme také, abyste před použitím této značky provedli testování. | Odchozí | No | No |
| **AzureResourceManager** | V Azure Resource Manageru | Odchozí | No | No |
| **AzureSignalR** | Signál Azure. | Odchozí | No | No |
| **AzureSiteRecovery** | Azure Site Recovery.<br/><br/>*Poznámka:* Tato značka má závislost na značkách **azureactivedirectory selhala**, **AzureKeyVault**, **EventHub**,**GuestAndHybridManagement** a **Storage** . | Odchozí | No | No |
| **AzureTrafficManager** | IP adresy služby Azure Traffic Manager PROBE.<br/><br/>Další informace o Traffic Manager IP adres sondy najdete v tématu [Nejčastější dotazy k Azure Traffic Manager](../traffic-manager/traffic-manager-faqs.md). | Příchozí | No | Yes |  
| **BatchNodeManagement** | Provoz správy pro nasazení vyhrazená pro Azure Batch. | Obojí | No | Yes |
| **CognitiveServicesManagement** | Rozsah adres pro přenos pro Azure Cognitive Services. | Obojí | No | No |
| **DataFactory**  | Azure Data Factory | Obojí | No | No |
| **DataFactoryManagement** | Provoz správy pro Azure Data Factory. | Odchozí | No | No |
| **Dynamics365ForMarketingEmail** | Rozsah adres pro marketingovou e-mailovou službu Dynamics 365. | Odchozí | Yes | No |
| **Centrum událostí** | Event Hubs Azure. | Odchozí | Yes | Yes |
| **GatewayManager** | Provoz správy pro nasazení vyhrazená pro Azure VPN Gateway a Application Gateway. | Příchozí | No | No |
| **GuestAndHybridManagement** | Azure Automation a konfigurace hostů. | Odchozí | No | Yes |
| **HDInsight** | Azure HDInsight. | Příchozí | Yes | No |
| **Internet** | Adresní prostor IP adres, který je mimo virtuální síť a dosažitelný veřejným internetem.<br/><br/>Rozsah adres zahrnuje [veřejný adresní prostor IP adres ve vlastnictví Azure](https://www.microsoft.com/download/details.aspx?id=41653). | Obojí | No | No |
| **LogicApps** | Logic Apps. | Obojí | No | No |
| **LogicAppsManagement** | Provoz správy pro Logic Apps. | Příchozí | No | No |
| **MicrosoftCloudAppSecurity** | Microsoft Cloud App Security. | Odchozí | No | No |
| **MicrosoftContainerRegistry** | Registr kontejnerů pro Image Microsoft Container. <br/><br/>*Poznámka:* Tato značka má závislost na značce **AzureFrontDoor. FirstParty** . | Odchozí | Yes | Yes |
| **PowerBI** | PowerBi. *Poznámka: Tato značka se v tuto chvíli nedá nakonfigurovat přes Azure Portal.* | Obojí | No | No|
| **PowerQueryOnline** | Power Query online. | Obojí | No | No |
| **ServiceBus** | Azure Service Bus provoz, který využívá úroveň služby Premium. | Odchozí | Yes | Yes |
| **ServiceFabric** | Service Fabric Azure.<br/><br/>*Poznámka:* Tato značka představuje koncový bod služby Service Fabric pro rovinu ovládacího prvku na oblast. To zákazníkům umožňuje provádět operace správy pro své Service Fabric clustery ze své virtuální sítě (koncové body). https://westus.servicefabric.azure.com) | Obojí | No | No |
| **SQL** | Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL a Azure synapse Analytics.<br/><br/>*Poznámka:* Tato značka představuje službu, ale ne konkrétní instance služby. Značka například představuje službu Azure SQL Database, ale ne konkrétní server nebo databázi SQL. Tato značka se nevztahuje na spravovanou instanci SQL. | Odchozí | Yes | Yes |
| **SqlManagement** | Provoz správy pro nasazení vyhrazená SQL. | Obojí | No | Yes |
| **Storage** | Azure Storage <br/><br/>*Poznámka:* Tato značka představuje službu, ale ne konkrétní instance služby. Značka například představuje službu Azure Storage, ale ne konkrétní účet služby Azure Storage. | Odchozí | Yes | Yes |
| **StorageSyncService** | Služba synchronizace úložiště. | Obojí | No | No |
| **WindowsVirtualDesktop** | Virtuální klient Windows. | Obojí | No | Yes |
| **VirtualNetwork** | Adresní prostor virtuální sítě (všechny rozsahy IP adres definované pro virtuální síť), všechny připojené místní adresní prostory, [partnerské](virtual-network-peering-overview.md) virtuální sítě, virtuální sítě připojené k [bráně virtuální sítě](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json), [virtuální IP adresu hostitele](./network-security-groups-overview.md#azure-platform-considerations)a předpony adres používané na [trasách definovaných uživatelem](virtual-networks-udr-overview.md). Tato značka může obsahovat také výchozí trasy. | Obojí | No | No |

>[!NOTE]
>V modelu nasazení Classic (před Azure Resource Manager) je podporována podmnožina značek uvedená v předchozí tabulce. Tyto značky jsou napsané jinak:
>
>| Klasický pravopis | Ekvivalentní značka Správce prostředků |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Značky služeb Azure označují předpony adres z konkrétního cloudu, který se používá. Například základní rozsahy IP adres, které odpovídají hodnotě značky **SQL** ve veřejném cloudu Azure, se budou lišit od základních rozsahů v cloudu Azure Čína.

> [!NOTE]
> Pokud implementujete [koncový bod služby virtuální sítě](virtual-network-service-endpoints-overview.md) pro službu, například Azure Storage nebo Azure SQL Database, Azure přidá [trasu](virtual-networks-udr-overview.md#optional-default-routes) k podsíti virtuální sítě pro danou službu. Předpony adresy v trase jsou stejné předpony adres nebo rozsahy CIDR jako u odpovídající značky služby.

## <a name="service-tags-on-premises"></a>Místní značky služby  
Aktuální informace o značce služby a rozsahu, které se mají zahrnout, můžete získat jako součást místních konfigurací brány firewall. Tyto informace představují aktuální časový Seznam rozsahů IP adres, které odpovídají jednotlivým značkám služby. Informace můžete získat prostřednictvím kódu programu nebo stažením souboru JSON, jak je popsáno v následujících částech.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>Použití rozhraní API zjišťování značek služeb (Public Preview)
Můžete programově načíst aktuální seznam značek služeb společně s podrobnostmi o rozsahu IP adres:

- [REST](/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag?viewFallbackFrom=azps-2.3.2)
- [Azure CLI](/cli/azure/network#az-network-list-service-tags)

> [!NOTE]
> I když je ve verzi Public Preview, rozhraní API pro zjišťování může vracet informace, které jsou méně aktuální než informace vrácené stahováním JSON. (Další informace najdete v další části.)


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>Zjišťování značek služeb pomocí souborů JSON ke stažení 
Můžete stáhnout soubory JSON, které obsahují aktuální seznam značek služeb společně s podrobnostmi o rozsahu IP adres. Tyto seznamy se aktualizují a publikují týdně. Umístění pro každý Cloud:

- [Veřejné Azure](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure pro vládu USA](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure (Čína)](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure (Německo)](https://www.microsoft.com/download/details.aspx?id=57064)   

Rozsahy IP adres v těchto souborech jsou v zápisu CIDR. 

> [!NOTE]
>Podmnožina těchto informací byla publikována v souborech XML pro [Azure Public](https://www.microsoft.com/download/details.aspx?id=41653), [Azure Čína](https://www.microsoft.com/download/details.aspx?id=42064)a [Azure Německo](https://www.microsoft.com/download/details.aspx?id=54770). Tyto soubory XML budou zastaralé od 30. června 2020 a po tomto datu již nebudou k dispozici. Měli byste migrovat na použití rozhraní Discovery API nebo souboru JSON ke stažení, jak je popsáno v předchozích částech.

### <a name="tips"></a>Tipy 
- Můžete zjišťovat aktualizace z jedné publikace na další tím, že zaznamenáte zvýšené hodnoty *changeNumber* v souboru JSON. Každý pododdíl (například **Storage. WestUS**) má svůj vlastní *changeNumber* , který se zvýší, protože dojde ke změnám. Nejvyšší úroveň *changeNumber* souboru se zvýší, když se změní některá z dílčích sekcí.
- Příklady, jak analyzovat informace o značce služby (například získat všechny rozsahy adres pro úložiště v WestUS), najdete v dokumentaci k [rozhraní API zjišťování značek služby](/powershell/module/az.network/Get-AzNetworkServiceTag?viewFallbackFrom=azps-2.3.2) .
- Když se do značek služeb přidají nové IP adresy, nepoužijí se v Azure aspoň jeden týden. Získáte tak čas aktualizovat všechny systémy, které by mohly potřebovat sledovat IP adresy přidružené k značkám služby.

## <a name="next-steps"></a>Další kroky
- Naučte se [vytvořit skupinu zabezpečení sítě](tutorial-filter-network-traffic.md).
