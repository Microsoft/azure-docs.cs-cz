---
title: Přehled značek služeb Azure
titlesuffix: Azure Virtual Network
description: Přečtěte si informace o značkách služeb. Značky služeb pomáhají minimalizovat složitost vytváření pravidel zabezpečení.
services: virtual-network
documentationcenter: na
author: jispar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2020
ms.author: jispar
ms.reviewer: kumud
ms.openlocfilehash: 568fc880711d42941fd9aef2ea19b8ac3123793a
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384241"
---
# <a name="virtual-network-service-tags"></a>Značky virtuálních síťových služeb
<a name="network-service-tags"></a>

Značka služby představuje skupinu předpon IP adres z dané služby Azure. Společnost Microsoft spravuje předpony adres zahrnuté v servisním štítku a automaticky aktualizuje výrobní číslo, jak se adresy mění, čímž minimalizuje složitost častých aktualizací pravidel zabezpečení sítě.

Pomocí značek služeb můžete definovat ovládací prvky přístupu k síti ve [skupinách](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) zabezpečení sítě nebo [azure firewall](https://docs.microsoft.com/azure/firewall/service-tags). Při vytváření pravidel zabezpečení používejte značky služeb místo konkrétních adres IP. Zadáním názvu značky služby (například **ApiManagement**) v příslušném *zdrojovém* nebo *cílovém* poli pravidla můžete povolit nebo odepřít provoz pro odpovídající službu.

Značky služeb můžete použít k dosažení izolace sítě a ochraně prostředků Azure před obecným internetem při přístupu ke službám Azure, které mají veřejné koncové body. Vytvořte příchozí a odchozí pravidla skupiny zabezpečení sítě, abyste odepřeli provoz na internet a z **Internetu** a povolte provoz na **azurecloud** u a z jiných [dostupných značek služeb](#available-service-tags) konkrétních služeb Azure.

## <a name="available-service-tags"></a>Dostupné značky služeb
Následující tabulka obsahuje všechny značky služeb, které jsou k dispozici pro použití v [pravidlech skupiny zabezpečení sítě.](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)

Sloupce označují, zda značka:

- Je vhodný pro pravidla, která pokrývají příchozí nebo odchozí provoz.
- Podporuje [oblastní](https://azure.microsoft.com/regions) oblast působnosti.
- Je použitelný v pravidlech [Azure Firewall.](https://docs.microsoft.com/azure/firewall/service-tags)

Ve výchozím nastavení značky služeb odrážejí rozsahy pro celý cloud. Některé značky služby také umožňují podrobnější řízení omezením odpovídajících rozsahů IP adres na zadanou oblast. Například služba **značky Storage** představuje Azure Storage pro celý cloud, ale **Storage.WestUS** zužuje rozsah pouze IP adresy úložiště rozsahy z oblasti WestUS. V následující tabulce je uvedeno, zda každá značka servisu podporuje takový regionální obor.  

| Značka | Účel | Můžete použít příchozí nebo odchozí? | Může být regionální? | Můžete použít s Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **Skupina akcí** | akční skupiny. | Příchozí | Ne | Ne |
| **ApiManagement** | Provoz správy pro nasazení vyhrazená pro správu rozhraní Azure API. <br/><br/>*Poznámka:* Tato značka představuje koncový bod služby Azure API Management pro rovinu ovládacího prvku na oblast. To umožňuje zákazníkům provádět operace správy rozhraní API, operace, zásady, pojmenované hodnoty nakonfigurované ve službě Api Management.  | Příchozí | Ano | Ano |
| **ApplicationInsightsAvailability** | Dostupnost přehledů aplikací. | Příchozí | Ne | Ne |
| **Konfigurace aplikace** | Konfigurace aplikace. | Odchozí | Ne | Ne |
| **Služba AppService**    | Azure App Service. Tato značka je doporučena pro odchozí pravidla zabezpečení na front-endy webových aplikací. | Odchozí | Ano | Ano |
| **AppServiceManagement** | Provoz správy pro nasazení vyhrazená pro prostředí služby App Service. | Obojí | Ne | Ano |
| **AzureActiveDirectory** | Azure Active Directory | Odchozí | Ne | Ano |
| **Služby AzureActiveDirectoryDomainServices** | Provoz správy pro nasazení vyhrazená pro službu Azure Active Directory Domain Services. | Obojí | Ne | Ano |
| **AzureAdvancedThreatProtection** | Azure Pokročilá ochrana před hrozbami. | Odchozí | Ne | Ne |
| **AzureBackup** |Azure Backup.<br/><br/>*Poznámka:* Tato značka je závislá na značkách **Storage** a **AzureActiveDirectory.** | Odchozí | Ne | Ano |
| **Služba AzureBotService** | Azure Bot Service. | Odchozí | Ne | Ne |
| **AzureCloud** | Všechny [veřejné IP adresy datového centra](https://www.microsoft.com/download/details.aspx?id=56519). | Odchozí | Ano | Ano |
| **AzureCognitiveSearch** | Azure kognitivní vyhledávání. <br/><br/>Tuto značku nebo IP adresy, na které se tato značka vztahuje, lze použít k udělení zabezpečeného přístupu indexerů ke zdrojům dat. Další podrobnosti naleznete v [dokumentaci k připojení indexeru.](https://docs.microsoft.com/azure/search/search-indexer-troubleshooting#connection-errors) <br/><br/> *Poznámka:* Ip adresa vyhledávací služby není zahrnuta v seznamu rozsahů IP adres pro tento servisní značku a **je také třeba ji přidat do** brány firewall IP zdrojů dat. | Příchozí | Ne | Ne |
| **Konektory Azure** | Konektory Azure Logic Apps pro připojení sondy/back-endu. | Příchozí | Ano | Ano |
| **AzureContainerRegistry** | Azure Kontejner registru. | Odchozí | Ano | Ano |
| **AzureCosmosDB** | Azure Cosmos DB. | Odchozí | Ano | Ano |
| **AzureDatabricks** | Datové cihly Azure. | Obojí | Ne | Ne |
| **AzureDataExplorerManagement** | Správa Průzkumníka dat Azure. | Příchozí | Ne | Ne |
| **AzureDataLake** | Azure Data Lake Storage Gen1. | Odchozí | Ne | Ano |
| **AzureDevSpaces** | Azure Dev Spaces. | Odchozí | Ne | Ne |
| **Síť AzureEventGrid** | Azure Event Grid. <br/><br/>*Poznámka:* Tato značka zahrnuje koncové body Azure Event Grid pouze v USA – – – střed, USA – východ, USA – východ 2, USA – západ 2 a US Central. | Obojí | Ne | Ne |
| **AzureFrontDoor.Frontend** <br/> **AzureFrontDoor.Back-end** <br/> **AzureFrontDoor.FirstParty**  | Azurové přední dveře. | Obojí | Ne | Ne |
| **AzureInformationProtection** | Azure Information Protection.<br/><br/>*Poznámka:* Tato značka má závislost na značkách **AzureActiveDirectory**, **AzureFrontDoor.Frontend** a **AzureFrontDoor.FirstParty.** | Odchozí | Ne | Ne |
| **AzureIoTHub** | Azure IoT Hub. | Odchozí | Ne | Ne |
| **AzureKeyVault** | Trezor klíčů Azure.<br/><br/>*Poznámka:* Tato značka je závislá na značce **AzureActiveDirectory.** | Odchozí | Ano | Ano |
| **AzureLoadBalancer** | Vyvažovač zatížení infrastruktury Azure. Značka se překládá na [virtuální IP adresu hostitele](security-overview.md#azure-platform-considerations) (168.63.129.16), kde pocházejí sondy stavu Azure. To nezahrnuje provoz na prostředek Azure Load Balancer. Pokud nepoužíváte Azure Balancer, můžete přepsat toto pravidlo. | Obojí | Ne | Ne |
| **AzureMachineLearning** | Azure Machine Learning. | Obojí | Ne | Ano |
| **AzureMonitor** | Log Analytics, Application Insights, AzMon a vlastní metriky (gig koncové body).<br/><br/>*Poznámka:* Pro Analýzu protokolů má tato značka závislost na značce **úložiště.** | Odchozí | Ne | Ano |
| **AzureOpenDatasets** | Otevřené datové sady Azure.<br/><br/>*Poznámka:* Tato značka má závislost na značce **AzureFrontDoor.Frontend** a **Storage.** | Odchozí | Ne | Ne |
| **AzurePlatformDNS** | Základní infrastruktura (výchozí) služba DNS.<br/><br>Pomocí této značky můžete zakázat výchozí dns. Při použití této značky buďte opatrní. Doporučujeme, abyste si [přečetli aspekty platformy Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations). Doporučujeme také provést testování před použitím této značky. | Odchozí | Ne | Ne |
| **AzureplatformIMDS** | Služba metadat instance Azure (IMDS), což je základní služba infrastruktury.<br/><br/>Pomocí této značky můžete zakázat výchozí systém IMDS. Při použití této značky buďte opatrní. Doporučujeme, abyste si [přečetli aspekty platformy Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations). Doporučujeme také provést testování před použitím této značky. | Odchozí | Ne | Ne |
| **AzurePlatformLKM** | Služba licencování nebo správy klíčů systému Windows.<br/><br/>Pomocí této značky můžete zakázat výchozí hodnoty pro licencování. Při použití této značky buďte opatrní. Doporučujeme, abyste si [přečetli aspekty platformy Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations).  Doporučujeme také provést testování před použitím této značky. | Odchozí | Ne | Ne |
| **AzureResourceManager** | Správce prostředků Azure. | Odchozí | Ne | Ne |
| **AzureSignalR** | Azure SignalR. | Odchozí | Ne | Ne |
| **AzureSiteRecovery** | Azure Site Recovery.<br/><br/>*Poznámka:* Tato značka má závislost na značkách **AzureActiveDirectory**, **AzureKeyVault**, **EventHub**,**GuestAndHybridManagement** a **Storage.** | Odchozí | Ne | Ne |
| **AzureTrafficManager** | Azure Traffic Manager sonda IP adresy.<br/><br/>Další informace o IP adresách sondy Traffic Manageru najdete v [nejčastějších dotazech k Azure Traffic Manageru](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). | Příchozí | Ne | Ano |  
| **Správa batchnode** | Provoz správy pro nasazení vyhrazená pro Azure Batch. | Obojí | Ne | Ano |
| **CognitiveServicesManagement** | Rozsahy adres pro provoz pro Azure Cognitive Services. | Odchozí | Ne | Ne |
| **DataFactory**  | Azure Data Factory | Obojí | Ne | Ne |
| **Řízení datových společností** | Provoz správy pro Azure Data Factory. | Odchozí | Ne | Ne |
| **Dynamics365ForMarketingEmail** | Rozsahy adres marketingové e-mailové služby Dynamics 365. | Odchozí | Ano | Ne |
| **ElasticAFD** | Elastické azurové přední dveře. | Obojí | Ne | Ne |
| **Centrum událostí** | Azure Event Hubs. | Odchozí | Ano | Ano |
| **GatewayManager** | Provoz správy pro nasazení vyhrazená pro Azure VPN Gateway a Aplikační brána. | Příchozí | Ne | Ne |
| **GuestAndHybridManagement** | Azure Automation a konfigurace hosta. | Odchozí | Ne | Ano |
| **HDInsight** | Azure HDInsight. | Příchozí | Ano | Ne |
| **Internetu** | IP adresní prostor, který je mimo virtuální síť a je dostupný veřejným internetem.<br/><br/>Rozsah adres zahrnuje [veřejný IP adresový prostor vlastněný Azure](https://www.microsoft.com/download/details.aspx?id=41653). | Obojí | Ne | Ne |
| **LogicApps** | Aplikace logiky. | Obojí | Ne | Ne |
| **LogicAppsManagement** | Správa provozu pro logic apps. | Příchozí | Ne | Ne |
| **MicrosoftCloudAppSecurity** | Zabezpečení aplikací Microsoft Cloud. | Odchozí | Ne | Ne |
| **Registr MicrosoftContainer** | Registr kontejnerů pro image kontejneru Microsoft. <br/><br/>*Poznámka:* Tato značka má závislost na značce **AzureFrontDoor.FirstParty.** | Odchozí | Ano | Ano |
| **PowerQueryOnline** | Power Query Online. | Obojí | Ne | Ne |
| **ServiceBus** | Provoz Azure Service Bus, který používá úroveň služby Premium. | Odchozí | Ano | Ano |
| **ServiceFabric** | Azure Service Fabric.<br/><br/>*Poznámka:* Tato značka představuje koncový bod služby Service Fabric pro rovinu ovládacího prvku na oblast. To umožňuje zákazníkům provádět operace správy pro jejich clustery Service Fabric z jejich virtuální sítě (koncový bod např. https:// westus.servicefabric.azure.com) | Obojí | Ne | Ne |
| **Sql** | Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL a Azure SQL Data Warehouse.<br/><br/>*Poznámka:* Tato značka představuje službu, ale ne konkrétní instance služby. Značka například představuje službu Azure SQL Database, ale ne konkrétní server nebo databázi SQL. Tato značka se nevztahuje na instanci spravované SQL. | Odchozí | Ano | Ano |
| **Řízení sqlmanagementu** | Provoz správy pro nasazení vyhrazená pro SQL. | Obojí | Ne | Ano |
| **Úložiště** | Azure Storage. <br/><br/>*Poznámka:* Tato značka představuje službu, ale ne konkrétní instance služby. Značka například představuje službu Azure Storage, ale ne konkrétní účet služby Azure Storage. | Odchozí | Ano | Ano |
| **Služba StorageSyncService** | Služba synchronizace úložiště. | Obojí | Ne | Ne |
| **WindowsVirtualDesktop** | Virtuální plocha systému Windows. | Obojí | Ne | Ne |
| **VirtualNetwork** | Adresní prostor virtuální sítě (všechny rozsahy IP adres definované pro virtuální síť), všechny připojené místní adresové prostory, [partnerské](virtual-network-peering-overview.md) virtuální sítě, virtuální sítě připojené k [bráně virtuální sítě](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json), virtuální IP adresa [hostitele](security-overview.md#azure-platform-considerations)a předpony adres používané na [uživatelem definovaných trasách](virtual-networks-udr-overview.md). Tato značka může také obsahovat výchozí trasy. | Obojí | Ne | Ne |

>[!NOTE]
>V klasickém modelu nasazení (před Správcem prostředků Azure) je podporována podmnožina značek uvedených v předchozí tabulce. Tyto značky jsou napsány odlišně:
>
>| Klasický pravopis | Ekvivalentní značka Správce prostředků |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Značky služeb Azure označují předpony adres z konkrétního používaného cloudu. Například základní rozsahy IP adres, které odpovídají hodnotě značky **Sql** v cloudu Azure Public, se budou lišit od základních rozsahů v cloudu Azure China.

> [!NOTE]
> Pokud implementujete [koncový bod služby virtuální sítě](virtual-network-service-endpoints-overview.md) pro službu, jako je Azure Storage nebo Azure SQL Database, Azure přidá [trasu](virtual-networks-udr-overview.md#optional-default-routes) do podsítě virtuální sítě pro službu. Předpony adres v postupu jsou stejné předpony adres nebo rozsahy CIDR jako příslušné výrobní číslo.

## <a name="service-tags-on-premises"></a>Místní značky služeb  
Můžete získat aktuální výrobní číslo a informace o rozsahu, které chcete zahrnout jako součást místní konfigurace brány firewall. Tyto informace jsou aktuálním seznamem bodů v čase rozsahů IP adres, které odpovídají jednotlivým výrobním polím. Informace můžete získat programově nebo prostřednictvím stahování souboru JSON, jak je popsáno v následujících částech.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>Použití rozhraní API pro zjišťování výrobních značek (veřejná verze Preview)
Můžete programově načíst aktuální seznam značek služeb spolu s podrobnostmi o rozsahu IP adres:

- [Odpočinku](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> I když je ve verzi Public Preview, rozhraní DISCOVERY API může vrátit informace, které jsou méně aktuální než informace vrácené stažením JSON. (Viz další část.)


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>Zjišťování značek služeb pomocí stahovatelných souborů JSON 
Můžete si stáhnout soubory JSON, které obsahují aktuální seznam značek služeb spolu s podrobnostmi o rozsahu IP adres. Tyto seznamy jsou aktualizovány a zveřejňovány každý týden. Umístění pro každý cloud jsou:

- [Azure Public](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure americká vláda](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure (Čína)](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure (Německo)](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>Podmnožina těchto informací byla publikována v souborech XML pro [Azure Public](https://www.microsoft.com/download/details.aspx?id=41653), [Azure China](https://www.microsoft.com/download/details.aspx?id=42064)a Azure [Germany](https://www.microsoft.com/download/details.aspx?id=54770). Června 2020 budou již zastaralé a po tomto datu již nebudou k dispozici. Měli byste migrovat na použití rozhraní Discovery API nebo stahování souborů JSON, jak je popsáno v předchozích částech.

### <a name="tips"></a>Tipy 
- Aktualizace z jedné publikace do druhé můžete zjistit tak, že si zaznažete zvýšené hodnoty *changeNumber* v souboru JSON. Každá podsekce (například **Storage.WestUS**) má vlastní *changeNumber,* který se při provádění změn zintáží. Nejvyšší úroveň *změny souboruNumber* se zintácí při změně některé z podsekcí.
- Příklady analýzy informací o výrobním tagu (například získat všechny rozsahy adres pro úložiště ve WestUS), naleznete v dokumentaci k prostředí [PowerShell pro zjišťování výrobních značek.](https://aka.ms/discoveryapi_powershell)

## <a name="next-steps"></a>Další kroky
- Přečtěte si, jak [vytvořit skupinu zabezpečení sítě](tutorial-filter-network-traffic.md).
