---
title: Přehled skupin zabezpečení Azure
titlesuffix: Azure Virtual Network
description: Přečtěte si víc o skupinách zabezpečení sítě a aplikací. Skupiny zabezpečení pomáhají filtrovat síťový provoz mezi prostředky Azure.
services: virtual-network
documentationcenter: na
author: jimdial
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2018
ms.author: jdial
ms.openlocfilehash: 7ba0cd37ea4c26485b154663fa4a99e98e3ec64e
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430127"
---
# <a name="security-groups"></a>Skupiny zabezpečení
<a name="network-security-groups"></a>

Pomocí skupiny zabezpečení sítě můžete filtrovat síťový provoz do a z prostředků Azure ve [virtuální síti](virtual-networks-overview.md) Azure. Skupina zabezpečení sítě obsahuje [pravidla zabezpečení](#security-rules) umožňující povolit nebo odepřít příchozí nebo odchozí síťový provoz několika typů prostředků Azure. Informace o prostředcích Azure, které je možné nasadit do virtuální sítě a ke kterým je možné přidružit skupiny zabezpečení sítě, najdete v tématu [Integrace virtuální sítě pro služby Azure](virtual-network-for-azure-services.md). Pro každé pravidlo můžete určit zdroj a cíl, port a protokol.

Tento článek vysvětluje koncepty skupin zabezpečení sítě, které vám pomůžou je efektivně využívat. Pokud jste ještě nikdy skupinu zabezpečení sítě nevytvářeli, můžete si projít rychlý [kurz](tutorial-filter-network-traffic.md), ve kterém se seznámíte s jejím vytvořením. Pokud už skupiny zabezpečení sítě znáte a potřebujete je spravovat, přečtěte si téma [Správa skupiny zabezpečení sítě](manage-network-security-group.md). Pokud máte problémy s komunikací a potřebujete řešit potíže se skupinami zabezpečení sítě, přečtěte si téma [Diagnostika potíží s filtrováním síťového provozu virtuálních počítačů](diagnose-network-traffic-filter-problem.md). Můžete povolit [protokoly toků skupin zabezpečení sítě](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json), abyste mohli [analyzovat síťový provoz](../network-watcher/traffic-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) směřující do a z prostředků s přidruženou skupinou zabezpečení sítě.

## <a name="security-rules"></a>Pravidla zabezpečení

Skupina zabezpečení sítě nemusí obsahovat žádná pravidla nebo může podle potřeby obsahovat libovolný počet pravidel v rámci [omezení](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) předplatného Azure. Každé pravidlo určuje následující vlastnosti:

|Vlastnost  |Vysvětlení  |
|---------|---------|
|Název|Jedinečný název v rámci skupiny zabezpečení sítě.|
|Priorita | Číslo v rozsahu od 100 do 4096. Pravidla se zpracovávají v pořadí podle priority, přičemž nižší čísla, která mají vyšší prioritu, se zpracovávají před vyššími čísly. Jakmile provoz odpovídá pravidlu, zpracování se zastaví. V důsledku toho se nezpracují žádná existující pravidla s nižší prioritou (vyšší čísla), která mají stejné atributy jako pravidla s vyšší prioritou.|
|Zdroj nebo cíl| Všechny nebo určitá IP adresa, blok CIDR (například 10.0.0.0/24), [značka služby](#service-tags) nebo [skupina zabezpečení aplikace](#application-security-groups). Pokud zadáváte adresu prostředku Azure, zadejte privátní IP adresu přiřazenou k tomuto prostředku. Skupiny zabezpečení sítě se zpracovávají poté, co Azure přeloží veřejnou IP adresu na privátní IP adresu pro příchozí provoz, a před tím, než Azure přeloží privátní IP adresu na veřejnou IP adresu pro odchozí provoz. Další informace o [IP adresách](virtual-network-ip-addresses-overview-arm.md) Azure. Zadání rozsahu, značky služby nebo skupiny zabezpečení aplikace umožňuje vytvářet méně pravidel zabezpečení. Možnost zadat v pravidlu několik jednotlivých IP adres a rozsahů (není možné zadat více značek služeb ani skupin aplikací) se označuje jako [rozšířená pravidla zabezpečení](#augmented-security-rules). Rozšířená pravidla zabezpečení je možné vytvářet pouze ve skupinách zabezpečení sítě vytvořených prostřednictvím modelu nasazení Resource Manager. Ve skupinách zabezpečení sítě vytvořených prostřednictvím modelu nasazení Classic není možné zadat více IP adres ani rozsahů IP adres. Další informace o [modelech nasazení Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
|Protocol (Protokol)     | TCP, UDP nebo všechny, což zahrnuje (ale bez omezení) TCP, UDP a ICMP. Samotný protokol ICMP nejde zadat, takže pokud potřebujete protokol ICMP, použijte možnost Všechny. |
|Směr| Určuje, jestli se pravidlo vztahuje na příchozí nebo odchozí provoz.|
|Rozsah portů     |Můžete zadat určitý port nebo rozsah portů. Můžete zadat například 80 nebo 10000-10005. Zadání rozsahů umožňuje vytvářet méně pravidel zabezpečení. Rozšířená pravidla zabezpečení je možné vytvářet pouze ve skupinách zabezpečení sítě vytvořených prostřednictvím modelu nasazení Resource Manager. Ve skupinách zabezpečení sítě vytvořených prostřednictvím modelu nasazení Classic není možné zadat několik portů ani rozsahů portů ve stejném pravidlu zabezpečení.   |
|Akce     | Povolení nebo odepření.        |

Pravidla zabezpečení skupin zabezpečení sítě se vyhodnocují podle priority s použitím informací o řazené kolekci 5 členů (zdroj, zdrojový port, cíl, cílový port a protokol) a určují, jestli se má provoz povolit nebo odepřít. Pro stávající připojení se vytvoří záznam toku. Komunikace se povoluje nebo odepírá na základě stavu připojení v záznamu toku. Záznam toku umožňuje, aby skupina zabezpečení sítě byla stavová. Pokud zadáte odchozí pravidlo zabezpečení pro všechny adresy například přes port 80, není potřeba zadávat příchozí pravidlo zabezpečení pro reakci na odchozí provoz. Příchozí pravidlo zabezpečení je potřeba zadat pouze v případě, že se komunikace zahajuje externě. Opačně to platí také. Pokud je přes port povolený příchozí provoz, není potřeba zadávat odchozí pravidlo zabezpečení pro reakci na provoz přes tento port.
Pokud odeberete pravidlo zabezpečení, které povolilo tok, nesmí se přerušit žádné stávající připojení. Toky provozu se přeruší v případě zastavení připojení a toku provozu oběma směry po dobu alespoň několika minut.

Počet pravidel zabezpečení, která můžete ve skupině zabezpečení sítě vytvořit, je omezený. Podrobnosti najdete v tématu věnovaném [omezením Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="augmented-security-rules"></a>Rozšířená pravidla zabezpečení

Rozšířená pravidla zabezpečení zjednodušují definici zabezpečení pro virtuální sítě tím, že umožňují definovat větší a složitější zásady zabezpečení sítě při použití menšího počtu pravidel. Můžete zkombinovat více portů a explicitních IP adres a rozsahů do jediného, snadno pochopitelného pravidla zabezpečení. Rozšířená pravidla používejte v polích pravidla pro zdroj, cíl a port. Pokud chcete zjednodušit údržbu definice pravidla zabezpečení, zkombinujte rozšířená pravidla zabezpečení se [značkami služeb](#service-tags) nebo [skupinami zabezpečení aplikací](#application-security-groups). Existují omezení počtu adres, rozsahy a porty, které můžete zadat v pravidlu. Podrobnosti najdete v tématu věnovaném [omezením Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="service-tags"></a>Značky služeb

 Značka služby představuje skupinu předpon IP adres a tím pomáhá minimalizovat složitost vytváření pravidla zabezpečení. Nemůžete vytvořit vlastní značku služby ani určit, které IP adresy jsou ve značce zahrnuté. Předpony adres zahrnuté ve značce služby spravuje Microsoft, a pokud se adresy změní, automaticky značku služby aktualizuje. Značky služeb můžete používat místo konkrétních IP adres při vytváření pravidel zabezpečení. 
 
 Z následujících týdenních publikací pro [veřejný](https://www.microsoft.com/download/details.aspx?id=56519) cloud Azure a cloudy Azure [US Government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) a [Germany](https://www.microsoft.com/download/details.aspx?id=57064) si můžete stáhnout seznam značek služeb s podrobnostmi o předponách a integrovat ho s místní bránou firewall.

 Pro použití v definici pravidla zabezpečení jsou k dispozici následující značky služeb. Jejich názvy se mírně liší mezi [modely nasazení Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

* **Virtuální síť** (Resource Manager) (**VIRTUAL_NETWORK** v případě klasického modelu): Tato značka zahrnuje adresní prostor virtuální sítě (všechny rozsahy CIDR definované pro virtuální síť), všechny připojené místní adresní prostory a [v partnerském vztahu](virtual-network-peering-overview.md) virtuální sítě nebo virtuální sítě připojené k [virtuální Brána sítě](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* **AzureLoadBalancer** (Resource Manager) (**AZURE_LOADBALANCER** v případě klasického modelu): Tato značka označuje nástroj pro vyrovnávání zatížení infrastruktury Azure. Značka se přeloží na [virtuální IP adresu hostitele](security-overview.md#azure-platform-considerations) (168.63.129.16), kde mají původ sondy stavu Azure. Pokud nepoužíváte nástroj pro vyrovnávání zatížení Azure, můžete toto pravidlo přepsat.
* **Internet** (Resource Manager) (**INTERNET** v případě klasického modelu): Tato značka označuje adresní prostor IP adres, který se nachází mimo virtuální síť a je dostupný prostřednictvím veřejného Internetu. Rozsah adres zahrnuje [veřejný adresní prostor IP adres vlastněný Azure](https://www.microsoft.com/download/details.aspx?id=41653).
* **AzureCloud** (pouze Resource Manager): Tato značka označuje adresní prostor IP adres pro Azure, včetně všech [veřejné IP adresy datacentra](https://www.microsoft.com/download/details.aspx?id=41653). Pokud jako hodnotu zadáte *AzureCloud*, provoz směřující na veřejné IP adresy Azure se povolí nebo zakáže. Pokud chcete povolit přístup ke cloudu Azure jenom v konkrétní [oblasti](https://azure.microsoft.com/regions), můžete zadat tuto oblast. Pokud například chcete povolit přístup ke cloudu Azure pouze v oblasti USA – východ, můžete jako značku služby zadat *AzureCloud.EastUS*. 
* **AzureTrafficManager** (pouze Resource Manager): Tato značka označuje adresní prostor IP adres pro IP adresy Azure Traffic Manageru test paměti. Další informace o IP adresách sondy pro Traffic Manager najdete v tématu [Azure Traffic Manager – nejčastější dotazy](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). 
* **Úložiště** (pouze Resource Manager): Tato značka označuje adresní prostor IP adres pro službu Azure Storage. Pokud jako hodnotu zadáte *Storage*, provoz směřující do úložiště se povolí nebo zakáže. Pokud chcete povolit přístup k úložišti pouze v konkrétní [oblasti](https://azure.microsoft.com/regions), můžete zadat tuto oblast. Pokud například chcete povolit přístup pouze ke službě Azure Storage v oblasti Východní USA, můžete jako značku služby zadat *Storage.EastUS*. Značka představuje službu, ale ne konkrétní instance služby. Značka například představuje službu Azure Storage, ale ne konkrétní účet služby Azure Storage. Všechny předpony adres reprezentované touto značkou jsou reprezentované také značkou **Internet**. 
* **SQL** (pouze Resource Manager): Tato značka označuje předpony adres služeb Azure SQL Database a Azure SQL Data Warehouse. Pokud jako hodnotu zadáte *SQL*, provoz směřující do SQL se povolí nebo zakáže. Pokud chcete povolit přístup k SQL jenom v konkrétní [oblasti](https://azure.microsoft.com/regions), můžete zadat tuto oblast. Pokud například chcete povolit přístup pouze ke službě Azure SQL Database v oblasti Východní USA, můžete jako značku služby zadat *Storage.EastUS*. Značka představuje službu, ale ne konkrétní instance služby. Značka například představuje službu Azure SQL Database, ale ne konkrétní server nebo databázi SQL. Všechny předpony adres reprezentované touto značkou jsou reprezentované také značkou **Internet**. 
* **AzureCosmosDB** (pouze Resource Manager): Tato značka označuje předpony adres služby databáze Azure Cosmos. Pokud jako hodnotu zadáte *AzureCosmosDB*, provoz směřující do služby Azure Cosmos DB se povolí nebo zakáže. Pokud chcete povolit přístup ke službě Azure Cosmos DB pouze v konkrétní [oblasti](https://azure.microsoft.com/regions), můžete oblast zadat v následujícím formátu: AzureCosmosDB.[název_oblasti]. 
* **AzureKeyVault** (pouze Resource Manager): Tato značka označuje předpony adres služby Azure Key Vaultu. Pokud jako hodnotu zadáte *AzureKeyVault*, provoz směřující do služby Azure Key Vault se povolí nebo zakáže. Pokud chcete povolit přístup ke službě Azure Key Vault pouze v konkrétní [oblasti](https://azure.microsoft.com/regions), můžete oblast zadat v následujícím formátu: AzureKeyVault.[název_oblasti]. 
* **Centrum EventHub** (pouze Resource Manager): Tato značka označuje předpony adres služeb Azure EventHub. Pokud jako hodnotu zadáte *EventHub*, provoz směřující do služby Event Hubs se povolí nebo zakáže. Pokud chcete povolit přístup ke službě Event Hubs pouze v konkrétní [oblasti](https://azure.microsoft.com/regions), můžete oblast zadat v následujícím formátu: EventHub.[název_oblasti]. 
* **Služby Service Bus** (pouze Resource Manager): Tato značka označuje předpony adres služby Azure Service Bus. Pokud jako hodnotu zadáte *ServiceBus*, provoz směřující do služby Service Bus se povolí nebo zakáže. Pokud chcete povolit přístup ke službě Service Bus pouze v konkrétní [oblasti](https://azure.microsoft.com/regions), můžete oblast zadat v následujícím formátu: ServiceBus.[název_oblasti]. 
* **MicrosoftContainerRegistry** (pouze Resource Manager): Tato značka označuje předpony adres služby Microsoft Container Registry. Pokud jako hodnotu zadáte *MicrosoftContainerRegistry*, provoz směřující do služby Microsoft Container Registry se povolí nebo zakáže. Pokud chcete povolit přístup ke službě Microsoft Container Registry pouze v konkrétní [oblasti](https://azure.microsoft.com/regions), můžete oblast zadat v následujícím formátu: MicrosoftContainerRegistry.[název_oblasti]. 
* **AzureContainerRegistry** (pouze Resource Manager): Tato značka označuje předpony adres služby Azure Container Registry. Pokud jako hodnotu zadáte *AzureContainerRegistry*, provoz směřující do služby Azure Container Registry se povolí nebo zakáže. Pokud chcete povolit přístup ke službě Azure Container Registry pouze v konkrétní [oblasti](https://azure.microsoft.com/regions), můžete oblast zadat v následujícím formátu: AzureContainerRegistry.[název_oblasti]. 
* **Služby App Service** (pouze Resource Manager): Tato značka označuje předpony adres služby Azure AppService. Pokud jako hodnotu zadáte *AppService*, provoz směřující do služby App Service se povolí nebo zakáže. Pokud chcete povolit přístup ke službě App Service pouze v konkrétní [oblasti](https://azure.microsoft.com/regions), můžete oblast zadat v následujícím formátu: AppService.[název_oblasti]. 
* **AppServiceManagement** (pouze Resource Manager): Tato značka označuje předpony adres služby Azure App Service Management. Pokud jako hodnotu zadáte *AppServiceManagement*, provoz směřující do služby pro správu služby App Service se povolí nebo zakáže. 
* **ApiManagement** (pouze Resource Manager): Tato značka označuje předpony adres služby Azure Api Management. Pokud jako hodnotu zadáte *ApiManagement*, provoz směřující do služby API Management se povolí nebo zakáže.  
* **AzureConnectors** (pouze Resource Manager): Tato značka označuje předpony adres služeb Azure konektory. Pokud jako hodnotu zadáte *AzureConnectors*, provoz směřující do konektorů Azure se povolí nebo zakáže. Pokud chcete povolit přístup ke konektorům Azure pouze v konkrétní [oblasti](https://azure.microsoft.com/regions), můžete oblast zadat v následujícím formátu: AzureConnectors.[název_oblasti]. 
* **GatewayManager** (pouze Resource Manager): Tato značka označuje předpony adres služeb Azure Správce brány. Pokud jako hodnotu zadáte *GatewayManager*, provoz směřující do služby Správce brány se povolí nebo zakáže.  
* **AzureDataLake** (pouze Resource Manager): Tato značka označuje předpony adres služby Azure Data Lake. Pokud jako hodnotu zadáte *AzureDataLake*, provoz směřující do služby Azure Data Lake se povolí nebo zakáže. 
* **AzureActiveDirectory** (pouze Resource Manager): Tato značka označuje předpony adres AzureActiveDirectory služby. Pokud jako hodnotu zadáte *AzureActiveDirectory*, provoz směřující do služby Azure Active Directory se povolí nebo zakáže.  
* **AzureMonitor** (pouze Resource Manager): Tato značka označuje předpony adres AzureMonitor služby. Pokud zadáte *AzureMonitor* pro hodnotu, je povolené nebo zakázané AzureMonitor přenosy. 
* **ServiceFabric** (pouze Resource Manager): Tato značka označuje předpony adres služby ServiceFabric. Pokud zadáte *ServiceFabric* pro hodnotu, je povolené nebo zakázané ServiceFabric přenosy. 
* **AzureMachineLearning** (pouze Resource Manager): Tato značka označuje předpony adres AzureMachineLearning služby. Pokud zadáte *AzureMachineLearning* pro hodnotu, je povolené nebo zakázané AzureMachineLearning přenosy. 

> [!NOTE]
> Značky služeb služeb Azure, označuje předpony adres z konkrétní cloudu se používají. 

> [!NOTE]
> Pokud implementujete [koncový bod služby virtuální sítě](virtual-network-service-endpoints-overview.md) pro službu, jako je Azure Storage nebo Azure SQL Database, Azure pro tuto službu přidá [trasu](virtual-networks-udr-overview.md#optional-default-routes) k podsíti virtuální sítě. Předpony adres pro trasu jsou stejné předpony adres nebo rozsahy CIDR jako u odpovídající značky služby.

## <a name="default-security-rules"></a>Výchozí pravidla zabezpečení

Azure v každé skupině zabezpečení sítě, kterou vytvoříte, vytvoří následující výchozí pravidla:

### <a name="inbound"></a>Příchozí

#### <a name="allowvnetinbound"></a>AllowVNetInBound

|Priorita|Zdroj|Zdrojové porty|Cíl|Cílové porty|Protocol (Protokol)|Access|
|---|---|---|---|---|---|---|
|65000|VirtualNetwork|0-65535|VirtualNetwork|0-65535|Vše|Povolit|

#### <a name="allowazureloadbalancerinbound"></a>AllowAzureLoadBalancerInBound

|Priorita|Zdroj|Zdrojové porty|Cíl|Cílové porty|Protocol (Protokol)|Access|
|---|---|---|---|---|---|---|
|65001|AzureLoadBalancer|0-65535|0.0.0.0/0|0-65535|Vše|Povolit|

#### <a name="denyallinbound"></a>DenyAllInbound

|Priorita|Zdroj|Zdrojové porty|Cíl|Cílové porty|Protocol (Protokol)|Access|
|---|---|---|---|---|---|---|
|65500|0.0.0.0/0|0-65535|0.0.0.0/0|0-65535|Vše|Odepřít|

### <a name="outbound"></a>Odchozí

#### <a name="allowvnetoutbound"></a>AllowVnetOutBound

|Priorita|Zdroj|Zdrojové porty| Cíl | Cílové porty | Protocol (Protokol) | Access |
|---|---|---|---|---|---|---|
| 65000 | VirtualNetwork | 0-65535 | VirtualNetwork | 0-65535 | Vše | Povolit |

#### <a name="allowinternetoutbound"></a>AllowVnetOutBound

|Priorita|Zdroj|Zdrojové porty| Cíl | Cílové porty | Protocol (Protokol) | Access |
|---|---|---|---|---|---|---|
| 65001 | 0.0.0.0/0 | 0-65535 | Internet | 0-65535 | Vše | Povolit |

#### <a name="denyalloutbound"></a>DenyAllOutBound

|Priorita|Zdroj|Zdrojové porty| Cíl | Cílové porty | Protocol (Protokol) | Access |
|---|---|---|---|---|---|---|
| 65500 | 0.0.0.0/0 | 0-65535 | 0.0.0.0/0 | 0-65535 | Vše | Odepřít |

Ve sloupcích **Zdroj** a **Cíl** jsou hodnoty *VirtualNetwork*, *AzureLoadBalancer* a *Internet* [značky služeb](#service-tags), a nikoli IP adresy. Hodnota **Všechny** ve sloupci Protokol zahrnuje protokoly TCP, UDP a ICMP. Při vytváření pravidla můžete zadat protokol TCP, UDP nebo Všechny, ale nemůžete zadat samotný protokol ICMP. Proto pokud vaše pravidlo vyžaduje protokol ICMP, vyberte jako protokol hodnotu *Všechny*. Hodnota *0.0.0.0/0* ve sloupcích **Zdroj** a **Cíl** představuje všechny adresy.
 
Výchozí pravidla nemůžete odebrat, ale můžete je přepsat vytvořením pravidel s vyšší prioritou.

## <a name="application-security-groups"></a>Skupiny zabezpečení aplikací

Skupiny zabezpečení aplikací umožňují konfigurovat zabezpečení sítě jako přirozené rozšíření struktury aplikace. Můžete seskupovat virtuální počítače a na základě těchto skupin definovat zásady zabezpečení sítě. Zásady zabezpečení můžete opakovaně používat ve velkém měřítku bez potřeby ruční údržby explicitních IP adres. O složitost explicitních IP adres a několika skupin pravidel se stará platforma a vy se tak můžete zaměřit na obchodní logiku. Pro lepší pochopení skupin zabezpečení aplikací si představte následující příklad:

![Skupiny zabezpečení aplikací](./media/security-groups/application-security-groups.png)

Na předchozím obrázku jsou *NIC1* a *NIC2* členy skupiny zabezpečení aplikace *AsgWeb*. *NIC3* je členem skupiny zabezpečení aplikace *AsgLogic*. *NIC4* je členem skupiny zabezpečení aplikace *AsgDb*. Přestože jsou všechna síťová rozhraní v tomto příkladu členy pouze jedné skupiny zabezpečení aplikace, síťové rozhraní může být členem více skupin zabezpečení aplikací, a to až do limitu daného [omezeními Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Žádné ze síťových rozhraní nemá přidruženou skupinu zabezpečení sítě. Skupina *NSG1* je přidružená k oběma podsítím a obsahuje následující pravidla:

### <a name="allow-http-inbound-internet"></a>Allow-HTTP-Inbound-Internet

Toto pravidlo je potřeba k povolení provozu směřujícího z internetu na webové servery. Vzhledem k tomu, že výchozí pravidlo zabezpečení [DenyAllInbound](#denyallinbound) odepírá příchozí provoz z internetu, není pro skupiny zabezpečení aplikací *AsgLogic* a *AsgDb* potřeba žádné další pravidlo.

|Priorita|Zdroj|Zdrojové porty| Cíl | Cílové porty | Protocol (Protokol) | Access |
|---|---|---|---|---|---|---|
| 100 | Internet | * | AsgWeb | 80 | TCP | Povolit |

### <a name="deny-database-all"></a>Deny-Database-All

Vzhledem k tomu, že výchozí pravidlo zabezpečení [AllowVNetInBound](#allowvnetinbound) povoluje veškerou komunikaci mezi prostředky ve stejné virtuální síti, je toto pravidlo potřeba k odepření provozu ze všech prostředků.

|Priorita|Zdroj|Zdrojové porty| Cíl | Cílové porty | Protocol (Protokol) | Access |
|---|---|---|---|---|---|---|
| 120 | * | * | AsgDb | 1433 | Vše | Odepřít |

### <a name="allow-database-businesslogic"></a>Allow-Database-BusinessLogic

Toto pravidlo povoluje provoz ze skupiny zabezpečení aplikace *AsgLogic* do skupiny zabezpečení aplikace *AsgDb*. Priorita tohoto pravidla je vyšší než priorita pravidla *Deny-Database-All*. Díky tomu se toto pravidlo zpracuje před pravidlem *Deny-Database-All*, takže se povolí provoz ze skupiny zabezpečení aplikace *AsgLogic*, zatímco veškerý ostatní provoz se zablokuje.

|Priorita|Zdroj|Zdrojové porty| Cíl | Cílové porty | Protocol (Protokol) | Access |
|---|---|---|---|---|---|---|
| 110 | AsgLogic | * | AsgDb | 1433 | TCP | Povolit |

Pravidla určující skupinu zabezpečení aplikace jako zdroj nebo cíl se použijí pouze na síťová rozhraní, která jsou členy příslušné skupiny zabezpečení aplikace. Pokud síťové rozhraní není členem žádné skupiny zabezpečení aplikace, pravidlo se pro něj nepoužije, ani když je k podsíti přiřazená skupina zabezpečení sítě.

Pro skupiny zabezpečení aplikací platí následující omezení:

-   Počet skupin zabezpečení aplikací, které můžete mít v předplatném, je omezený. V souvislosti se skupinami zabezpečení aplikací platí také další omezení. Podrobnosti najdete v tématu věnovaném [omezením Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Jako zdroj a cíl můžete v pravidlu zabezpečení zadat jednu skupinu zabezpečení aplikace. Více skupin zabezpečení aplikací ve zdroji ani cíli zadat nemůžete.
- Všechna síťová rozhraní přiřazená ke skupině zabezpečení aplikace musí existovat ve stejné virtuální síti jako první síťové rozhraní přiřazené ke skupině zabezpečení aplikace. Pokud se například první síťové rozhraní přiřazené ke skupině zabezpečení aplikace *AsgWeb* nachází ve virtuální síti *VNet1*, pak všechna další síťová rozhraní přiřazená ke skupině zabezpečení aplikace *ASGWeb* musí existovat ve virtuální síti *VNet1*. Do stejné skupiny zabezpečení aplikace nemůžete přidat síťová rozhraní z různých virtuálních sítí.
- Pokud zadáte skupinu zabezpečení aplikací jako zdroj a cíl v pravidle zabezpečení, síťová rozhraní v obou skupinách zabezpečení aplikací musí existovat ve stejné virtuální síti. Kdyby například skupina *AsgLogic* obsahovala síťová rozhraní z virtuální sítě *VNet1* a skupina *AsgDb* obsahovala síťová rozhraní z virtuální sítě *VNet2*, nemohli byste v pravidle přiřadit skupinu *AsgLogic* jako zdroj a skupinu *AsgDb* jako cíl. Všechna síťová rozhraní pro zdrojové i cílové skupiny zabezpečení aplikací musí existovat ve stejné virtuální síti.

> [!TIP]
> Pokud chcete minimalizovat počet potřebných pravidel zabezpečení a nutnost jejich změn, naplánujte potřebné skupiny zabezpečení aplikací a vytvářejte pravidla s použitím značek služeb nebo skupin zabezpečení aplikací, a ne jednotlivých IP adres nebo rozsahů IP adres, pokud je to možné.

## <a name="how-traffic-is-evaluated"></a>Způsob vyhodnocování provozu

Do virtuální sítě Azure můžete nasadit prostředky z několika služeb Azure. Úplný seznam najdete v tématu popisujícím [služby, které je možné nasadit do virtuální sítě](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Ke každé [podsíti](virtual-network-manage-subnet.md#change-subnet-settings) virtuální sítě a každému [síťovému rozhraní](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) na virtuálním počítači můžete přiřadit jednu nebo žádnou skupinu zabezpečení sítě. Stejnou skupinu zabezpečení sítě můžete přidružit k libovolnému počtu podsítí a síťových rozhraní.

Následující obrázek ukazuje různé scénáře nasazení skupin zabezpečení sítě pro povolení síťového provozu do a z internetu na portu TCP 80:

![Zpracování NSG](./media/security-groups/nsg-interaction.png)

Předchozí obrázek společně s následujícím textem vám pomůže porozumět způsobu, jakým Azure zpracovává příchozí a odchozí pravidla pro skupiny zabezpečení sítě:

### <a name="inbound-traffic"></a>Příchozí provoz

V případě příchozího provozu zpracuje Azure nejprve pravidla ve skupině zabezpečení sítě přidružené k příslušné podsíti, pokud taková skupina existuje, a pak pravidla ve skupině zabezpečení sítě přidružené k síťovému rozhraní, pokud taková skupina existuje.

- **VM1**: Pravidla zabezpečení v *NSG1* se zpracovávají, protože je přidružená k *Subnet1* a *VM1* probíhá *Subnet1*. Pokud jste nevytvořili pravidlo povolující příchozí provoz na portu 80, výchozí pravidlo zabezpečení [DenyAllInbound](#denyallinbound) provoz odepře a skupina *NSG2* ho nikdy nevyhodnotí, protože skupina *NSG2* je přidružená k síťovému rozhraní. Pokud skupina *NSG1* obsahuje pravidlo zabezpečení povolující port 80, provoz se pak zpracuje skupinou *NSG2*. Pokud pro virtuální počítač chcete povolit příchozí provoz přes port 80, skupina *NSG1* i *NSG2* musí obsahovat pravidlo povolující příchozí provoz přes port 80 z internetu.
- **VM2**: Pravidla v *NSG1* jsou zpracovat, protože *VM2* jsou uvedené taky v *Subnet1*. Vzhledem k tomu, že k síťovému rozhraní virtuálního počítače *VM2* není přidružená žádná skupina zabezpečení sítě, přijme toto rozhraní veškerý provoz povolený skupinou *NSG1* nebo se mu odepře veškerý provoz zakázaný skupinou *NSG1*. Pokud je skupina zabezpečení sítě přidružená k podsíti, povolí se nebo se odepře provoz do všech prostředků ve stejné podsíti.
- **VM3**: Protože neexistuje žádná skupina zabezpečení sítě přidružené k *Subnet2*, provoz je povolený do podsítě a zpracuje *NSG2*, protože *NSG2* je přidružen k síti rozhraní připojené k *VM3*.
- **VM4**: Provoz je povolený pro *VM4,* vzhledem k tomu, že skupina zabezpečení sítě není přidružená k *Subnet3*, nebo síťové rozhraní ve virtuálním počítači. Pokud k podsíti a síťovému rozhraní není přidružená žádná skupina zabezpečení sítě, povolí se přes ně průchod veškerého síťového provozu.

### <a name="outbound-traffic"></a>Odchozí provoz

V případě odchozího provozu zpracuje Azure nejprve pravidla ve skupině zabezpečení sítě přidružené k příslušnému síťovému rozhraní, pokud taková skupina existuje, a pak pravidla ve skupině zabezpečení sítě přidružené k podsíti, pokud taková skupina existuje.

- **VM1**: Pravidla zabezpečení v *NSG2* se zpracovávají. Pokud nevytvoříte pravidlo zabezpečení zakazující odchozí provoz přes port 80 do internetu, výchozí pravidlo zabezpečení [AllowInternetOutbound](#allowinternetoutbound) ve skupině *NSG1* i *NSG2* provoz povolí. Pokud skupina *NSG2* obsahuje pravidlo zabezpečení zakazující port 80, provoz se odepře a skupina *NSG1* ho nikdy nevyhodnotí. Pokud chcete na virtuálním počítači zakázat odchozí provoz přes port 80, jedna ze skupin zabezpečení sítě nebo obě musí obsahovat pravidlo zakazující odchozí provoz přes port 80 do internetu.
- **VM2**: Všechny přenosy se odesílají prostřednictvím síťové rozhraní k podsíti, protože síťové rozhraní připojené k *VM2* nemá skupinu zabezpečení sítě přidruženou k němu. Zpracují se pravidla ve skupině *NSG1*.
- **VM3**: Pokud *NSG2* pravidlo zabezpečení, která zakazuje port 80, provoz byl odepřen. Pokud skupina *NSG2* obsahuje pravidlo zabezpečení povolující port 80, povolí se odchozí provoz přes port 80 do internetu, protože k podsíti *Subnet2* není přidružená žádná skupina zabezpečení sítě.
- **VM4**: Je povolen veškerý síťový provoz z *VM4,* protože není přidružená k síťové rozhraní připojené k virtuálnímu počítači nebo na skupinu zabezpečení sítě *Subnet3*.

Agregovaná pravidla použitá na síťové rozhraní můžete snadno zobrazit v [platných pravidlech zabezpečení](virtual-network-network-interface.md#view-effective-security-rules) pro síťové rozhraní. Pomocí funkce [Ověření toku protokolu IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) v nástroji Azure Network Watcher můžete také určit, jestli je povolená komunikace směřující do síťového rozhraní nebo z něj. Ověření toku protokolu IP vám řekne, jestli je povolená nebo zakázaná komunikace, a které pravidlo zabezpečení sítě povoluje nebo odepírá provoz.

> [!NOTE]
> Skupiny zabezpečení sítě jsou přidružené k podsítím nebo virtuálním počítačům a cloudovým službám nasazeným v modelu nasazení classic, nikoli k síťovým rozhraním v modelu nasazení Resource Manager. Další informace o modelech nasazení Azure najdete v článku [Vysvětlení modelů nasazení Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!TIP]
> Pokud k tomu nemáte konkrétní důvod, doporučujeme přidružit skupinu zabezpečení sítě k podsíti nebo k síťovému rozhraní, ale ne k oběma. Vzhledem k tomu, že může docházet ke konfliktům mezi pravidly ve skupině zabezpečení sítě přidružené k podsíti a pravidly ve skupině zabezpečení sítě přidružené k síťovému rozhraní, můžou nastat neočekávané problémy s komunikací vyžadující řešení.

## <a name="azure-platform-considerations"></a>Důležité informace o platformě Azure

- **Virtuální IP adresa uzlu hostitele**: Základní infrastruktura služby, jako je DHCP, DNS, IMDS a sledování stavu, jsou poskytované prostřednictvím virtualizované hostitele IP adresy 168.63.129.16 a 169.254.169.254. Tyto IP adresy patří společnosti Microsoft a jsou jediné virtualizované IP adresy pro tento účel používá ve všech oblastech.
- **Licencování (služba správy klíčů)**: Image Windows spuštěné ve virtuálních počítačích musí mít licenci. Aby se zajistilo licencování, odesílají se žádosti o licenci na hostitelské servery Služby správy klíčů, které takové dotazy zpracovávají. Požadavek odchází přes port 1688. Pro nasazení využívající konfiguraci [výchozí trasy 0.0.0.0/0](virtual-networks-udr-overview.md#default-route) bude toto pravidlo platformy zakázané.
- **Virtuální počítače ve fondech s vyrovnáváním zatížení**: Jsou zdrojový port a rozsah adres odpovídá zdrojovému počítači, není nástroj pro vyrovnávání zatížení. Cílový port a rozsah adres odpovídá cílovému počítači, a nikoli nástroji pro vyrovnávání zatížení.
- **Instance služeb Azure**: V podsítích virtuální sítě jsou nasazené instance několika služeb Azure, jako je HDInsight, prostředí aplikačních služeb a Škálovací sady virtuálních počítačů. Úplný seznam služeb, které můžete nasadit do virtuální sítě, najdete v tématu [Virtuální síť pro služby Azure](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Před použitím skupiny zabezpečení sítě na podsíť, ve které je nasazený prostředek, se ujistěte, že znáte požadavky jednotlivých služeb na porty. Pokud odepřete porty, které služba vyžaduje, nebude správně fungovat.
- **Odesílání odchozích e-mailů**: Společnost Microsoft doporučuje k odesílání e-mailů ze služby Azure Virtual Machines využívat služby předávání přes ověřený protokol SMTP (obvykle připojené přes TCP port 587, ale často další, i). Služby pro přenos přes protokol SMTP se specializují na reputaci odesílatele, aby se minimalizovala možnost odmítnutí zpráv poskytovateli e-mailu třetích stran. Mezi takové služby pro přenos přes protokol SMTP patří mimo jiné Exchange Online Protection a SendGrid. Používání služeb pro přenos přes protokol SMTP v Azure není nijak omezeno, a to bez ohledu na typ předplatného. 

  Pokud jste své předplatné Azure vytvořili před 15. listopadem 2017, kromě možnosti používat služby pro přenos přes protokol SMTP můžete e-maily odesílat také přímo přes port TCP 25. Pokud jste své předplatné vytvořili po 15. listopadu 2017, možná nebudete moci odesílat e-maily přímo přes port 25. Chování odchozí komunikace přes port 25 závisí na typu vašeho předplatného, a to následujícím způsobem:

     - **Smlouva Enterprise Agreement**: Odchozí komunikace přes port 25 je povolená. Odchozí emaily můžete z virtuálních počítačů odesílat přímo externím poskytovatelům e-mailu bez jakýchkoli omezení platformy Azure. 
     - **Průběžné platby:** Odchozí komunikace přes port 25 ze všech prostředků je blokovaná. Pokud potřebujete odesílat e-maily z virtuálního počítače přímo externím poskytovatelům e-mailu (bez použití přenosu přes zabezpečený protokol SMTP), můžete vytvořit žádost o odebrání tohoto omezení. Žádosti se posuzují a schvalují na základě vlastního uvážení Microsoftu a vyhoví se jim pouze po provedení kontrol v souvislosti s možnými podvody. Pokud chcete vytvořit žádost, otevřete případ podpory s typem problému *Technický*, *Možnosti připojení k virtuální síti*, *Nelze odesílat e-maily (SMTP/port 25)*. Do případu podpory zahrňte podrobnosti o tom, proč vaše předplatné potřebuje odesílat e-maily přímo poskytovatelům e-mailu místo používání přenosu přes ověřený protokol SMTP. Pokud má vaše předplatné výjimku, odchozí komunikace přes port 25 jsou schopné pouze virtuální počítače vytvořené po datu udělení výjimky.
     - **Azure MSDN, Azure Pass, v otevřít, vzdělávání, BizSpark a bezplatné zkušební verze**: Odchozí komunikace přes port 25 ze všech prostředků je blokovaná. Není možné vytvořit žádost o odebrání omezení, protože takovým žádostem se nevyhovuje. Pokud z virtuálního počítače potřebujete odesílat e-maily, musíte k tomu použít službu pro přenos přes protokol SMTP.
     - **Poskytovatel cloudových služeb**: Zákazníci, kteří prostředky Azure prostřednictvím poskytovatele cloudových služeb můžete vytvořit lístek podpory s jejich poskytovatel cloudových služeb a požádat o, že poskytovatel vytvořit případ odblokování jejich jménem, pokud se nedá použít zabezpečeného serveru SMTP.

  Pokud vám Azure povolí odesílat e-maily přes port 25, Microsoft nemůže zaručit přijetí příchozích e-mailů z vašeho virtuálního počítače poskytovateli e-mailu. Pokud konkrétní poskytovatel odmítá e-maily z vašeho virtuálního počítače, spolupracujte přímo s daným poskytovatelem a vyřešte případné problémy s doručováním zpráv nebo filtrováním nevyžádané pošty, nebo použijte službu pro přenos přes ověřený protokol SMTP.

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [vytvořit skupinu zabezpečení sítě](tutorial-filter-network-traffic.md).
