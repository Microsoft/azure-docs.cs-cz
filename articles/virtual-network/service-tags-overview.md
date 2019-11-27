---
title: Přehled značek služeb Azure
titlesuffix: Azure Virtual Network
description: Přečtěte si o značkách služby. Značky služeb pomůžou minimalizovat složitost vytváření pravidel zabezpečení.
services: virtual-network
documentationcenter: na
author: jispar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2019
ms.author: jispar
ms.reviewer: kumud
ms.openlocfilehash: 33ee7351e547ee5ef57ef07f67ba6f5f4410b57f
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384142"
---
# <a name="virtual-network-service-tags"></a>Značky služby virtuální sítě 
<a name="network-service-tags"></a>

Značka služby představuje skupinu předpon IP adres z dané služby Azure. Pomáhá minimalizovat složitost častých aktualizací pravidel zabezpečení sítě. Pomocí značek služeb můžete definovat řízení přístupu k síti pro [skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) nebo [Azure firewall](https://docs.microsoft.com/azure/firewall/service-tags). Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. Zadáním názvu značky služby (např. **ApiManagement**) v příslušném *zdrojovém* nebo *cílovém* poli pravidla můžete povolit nebo odepřít provoz pro příslušnou službu. Společnost Microsoft spravuje předpony adres, které jsou součástí značky služby, a automaticky aktualizuje označení služby jako adresy změny. 

## <a name="available-service-tags"></a>Dostupné značky služeb
Následující tabulka obsahuje všechny značky služeb, které jsou k dispozici pro použití v pravidlech [skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) .

Sloupce označují, zda je značka:

- Vhodné pro pravidla zahrnující příchozí nebo odchozí provoz
- [Místní](https://azure.microsoft.com/regions) obor podpory 
- Použitelné v pravidlech [Azure firewall](https://docs.microsoft.com/azure/firewall/service-tags)

Ve výchozím nastavení značky služby odráží rozsahy celého cloudu.  Některé značky služeb také umožňují podrobnější kontrolu nad více podmnožinami omezením odpovídajících rozsahů IP adres na určitou oblast.  Například když **úložiště** značek služby představuje Azure Storage pro celý Cloud, **Storage. WestUS** zužuje jenom na rozsahy IP adres úložiště z oblasti WestUS.  Popisy jednotlivých značek služeb označují, jestli podporují takový obor.  



| Značka | Účel | Dá se použít příchozí nebo odchozí? | Je možné je rozregionovat? | Lze použít s Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ApiManagement** | Provoz správy pro vyhrazená nasazení APIM. | Obojí | Ne | Ano |
| **AppService**    | Služba App Service. Tato značka se doporučuje pro odchozí pravidla zabezpečení pro WebApp front-endu. | Odchozí | Ano | Ano |
| **AppServiceManagement** | Provoz správy pro App Service Environment vyhrazená nasazení. | Obojí | Ne | Ano |
| **Azureactivedirectory selhala** | Služba Azure Active Directory. | Odchozí | Ne | Ano |
| **AzureActiveDirectoryDomainServices** | Provoz správy pro Azure Active Directory Domain Services vyhrazená nasazení. | Obojí | Ne | Ano |
| **AzureBackup** |Služba Azure Backup.<br/><br/>*Poznámka:* Tato značka má závislost na značkách **úložiště** a **azureactivedirectory selhala** . | Odchozí | Ne | Ano |
| **AzureCloud** | Všechny [veřejné IP adresy Datacenter](https://www.microsoft.com/download/details.aspx?id=41653) | Odchozí | Ano | Ano |
| **AzureConnectors** | Konektory Logic Apps pro připojení sondy a back-endu. | Příchozí | Ano | Ano |
| **AzureContainerRegistry** | Služba Azure Container Registry. | Odchozí | Ano | Ano |
| **AzureCosmosDB** | Databázová služba Azure Cosmos | Odchozí | Ano | Ano |
| **AzureDataLake** | Služba Azure Data Lake. | Odchozí | Ne | Ano |
| **AzureIoTHub** | Služba Azure IoT Hub. | Odchozí | Ne | Ne |
| **AzureKeyVault** | Služba úložiště klíčů Azure.<br/><br/>*Poznámka:* Tato značka má závislost na značce **azureactivedirectory selhala** . | Odchozí | Ano | Ano |
| **AzureLoadBalancer** | Nástroj pro vyrovnávání zatížení infrastruktury Azure. Značka se přeloží na [virtuální IP adresu hostitele](security-overview.md#azure-platform-considerations) (168.63.129.16), kde mají původ sondy stavu Azure. Pokud nepoužíváte nástroj pro vyrovnávání zatížení Azure, můžete toto pravidlo přepsat. | Obojí | Ne | Ne |
| **AzureMachineLearning** | Služba Azure Machine Learning. | Odchozí | Ne | Ano |
| **AzureMonitor** | Log Analytics, App Insights, AzMon a vlastní metriky (koncové body 4Gigový).<br/><br/>*Poznámka:* U Log Analytics má tato značka závislost na značce **úložiště** . | Odchozí | Ne | Ano |
| **AzurePlatformDNS** | Služba DNS základní infrastruktury (výchozí).<br/><br>Pomocí této značky můžete zakázat výchozí DNS. Při používání této značky se prosím obraťte na upozornění. Přečtěte si doporučení pro [platformu Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) . Před použitím této značky se doporučuje testování. | Odchozí | Ne | Ne |
| **AzurePlatformIMDS** | IMDS, což je základní služba infrastruktury.<br/><br/>Pomocí této značky můžete zakázat výchozí IMDS.  Při používání této značky se prosím obraťte na upozornění. Přečtěte si doporučení pro [platformu Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) . Před použitím této značky se doporučuje testování. | Odchozí | Ne | Ne |
| **AzurePlatformLKM** | Licencování Windows nebo služba správy klíčů.<br/><br/>Pomocí této značky můžete zakázat výchozí hodnoty licencování. Při používání této značky se prosím obraťte na upozornění.  Přečtěte si doporučení pro [platformu Azure](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) . Před použitím této značky se doporučuje testování. | Odchozí | Ne | Ne |
| **AzureTrafficManaged** | IP adresy služby Azure Traffic Manager PROBE.<br/><br/>Další informace o IP adresách sondy pro Traffic Manager najdete v tématu [Azure Traffic Manager – nejčastější dotazy](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). | Příchozí | Ne | Ano |  
| **BatchNodeManagement** | Provoz správy pro Azure Batch vyhrazená nasazení. | Obojí | Ne | Ano |
| **CognitiveServicesManagement** | Rozsah adres pro provoz Cognitive Services | Odchozí | Ne | Ne |
| **Dynamics365ForMarketingEmail** | Rozsah adres pro marketingovou e-mailovou službu Dynamics 365. | Odchozí | Ano | Ne |
| **EventHub** | Služba EventHub Azure | Odchozí | Ano | Ano |
| **GatewayManager** | Provoz správy pro VPN/brány aplikací vyhrazené nasazení. | Příchozí | Ne | Ne |
| **Internet** | Adresní prostor IP adres, který je mimo virtuální síť a dosažitelný veřejným internetem.<br/><br/>Rozsah adres zahrnuje [veřejný adresní prostor IP adres vlastněný Azure](https://www.microsoft.com/download/details.aspx?id=41653). | Obojí | Ne | Ne |
| **MicrosoftContainerRegistry** | Služba Microsoft Container Registry. | Odchozí | Ano | Ano |
| **ServiceBus** | Služba Azure Service Bus s využitím úrovně Premium Service. | Odchozí | Ano | Ano |
| **ServiceFabric** | Služba Service Fabric. | Odchozí | Ne | Ne |
| **SQL** | Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL a Azure SQL Data Warehouse služby.<br/><br/>*Poznámka:* Tato značka představuje službu, ale ne konkrétní instance služby. Značka například představuje službu Azure SQL Database, ale ne konkrétní server nebo databázi SQL. | Odchozí | Ano | Ano |
| **SqlManagement** | Provoz správy pro vyhrazená nasazení SQL. | Obojí | Ne | Ano |
| **Storage** | Služba Azure Storage. <br/><br/>*Poznámka:* Značka představuje službu, ale ne konkrétní instance služby. Značka například představuje službu Azure Storage, ale ne konkrétní účet služby Azure Storage. | Odchozí | Ano | Ano |
| **VirtualNetwork** | Adresní prostor virtuální sítě (všechny rozsahy IP adres definované pro virtuální síť), všechny připojené místní adresní prostory, [partnerské](virtual-network-peering-overview.md) virtuální sítě nebo virtuální sítě připojené k [bráně virtuální sítě](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json), [virtuální IP adresa Adresa hostitele](security-overview.md#azure-platform-considerations) a předpony adresy používané na [trasách definovaných uživatelem](virtual-networks-udr-overview.md). Upozorňujeme, že tato značka může obsahovat také výchozí trasy. | Obojí | Ne | Ne |

>[!NOTE]
>Při práci v *klasickém* prostředí (pre-Azure Resource Manager) se podporuje vybraná sada značek výše.  Používají alternativní pravopis:

| Klasický pravopis | Ekvivalentní značka Správce prostředků |
|---|---|
| AZURE_LOADBALANCER | AzureLoadBalancer |
| INTERNET | Internet |
| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Značky služeb Azure označují předpony adres z konkrétního cloudu, který se používá. např. základní rozsahy IP adres, které odpovídají hodnotě značky **SQL** , se budou lišit mezi veřejným cloudem Azure a cloudem Azure Čína.

> [!NOTE]
> Pokud implementujete [koncový bod služby pro virtuální síť](virtual-network-service-endpoints-overview.md) pro službu, jako je Azure Storage nebo Azure SQL Database, Azure pro tuto službu přidá [trasu](virtual-networks-udr-overview.md#optional-default-routes) k podsíti virtuální sítě. Předpony adres pro trasu jsou stejné předpony adres nebo rozsahy CIDR jako u odpovídající značky služby.



## <a name="service-tags-in-on-premises"></a>Značky služeb v místním prostředí  
Aktuální informace o značce služby a rozsahu, které se mají zahrnout, můžete získat jako součást místních konfigurací brány firewall.  Tyto informace jsou aktuálním časovým seznamem rozsahů IP adres, které odpovídají jednotlivým značkám služby.  Informace lze získat prostřednictvím kódu programu nebo pomocí souboru JSON ke stažení následujícím způsobem.

### <a name="service-tag-discovery-api-public-preview"></a>Rozhraní API zjišťování značek služeb (Public Preview)
Můžete programově načíst aktuální seznam značek služeb s podrobnostmi o rozsahu IP adres:

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> V Public Preview může rozhraní API zjišťování vracet informace, které nejsou tak aktuální jako soubory JSON ke stažení (níže).


### <a name="discover-service-tags-using-downloadable-json-files"></a>Zjišťování značek služeb pomocí souborů JSON ke stažení 
Můžete stáhnout soubory JSON obsahující aktuální seznam značek služeb s podrobnostmi o rozsahu IP adres. Ty se aktualizují a publikují týdně.  Umístění pro každý Cloud:

- [Veřejné Azure](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure (Čína)](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Německo](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>V souborech XML pro [Azure Public](https://www.microsoft.com/download/details.aspx?id=41653), [Azure Čína](https://www.microsoft.com/download/details.aspx?id=42064) a [Azure Německo](https://www.microsoft.com/download/details.aspx?id=54770)byly některé podmnožiny těchto informací dřív publikované. Tyto soubory XML budou zastaralé od 30. června 2020 a po tomto datu již nebudou k dispozici. Migrujte prosím na použití rozhraní Discovery API nebo souboru JSON ke stažení, jak je popsáno výše.

### <a name="tips"></a>Tipy 
- Můžete zjišťovat aktualizace od jednoho publikování na další prostřednictvím zvýšené hodnoty *changeNumber* v souboru JSON. Jednotlivé pododdíly (např. **Storage. WestUS**) mají vlastní *changeNumber* , které se zvýší, když dojde ke změnám.  Nejvyšší úroveň *changeNumber* souboru se zvýší, když se změní kterákoli z dílčích sekcí.
- Příklady, jak analyzovat informace o značce služby (například získat všechny rozsahy adres pro úložiště v WestUS), najdete v dokumentaci k [rozhraní API zjišťování značek služby](https://aka.ms/discoveryapi_powershell) .

## <a name="next-steps"></a>Další kroky
- Zjistěte, jak [vytvořit skupinu zabezpečení sítě](tutorial-filter-network-traffic.md).

