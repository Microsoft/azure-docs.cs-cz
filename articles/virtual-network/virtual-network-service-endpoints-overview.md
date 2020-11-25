---
title: Koncové body služby virtuální sítě Azure
titlesuffix: Azure Virtual Network
description: Zjistěte, jak povolit přímý přístup k prostředkům Azure z virtuální sítě pomocí koncových bodů služby.
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2019
ms.author: sumi
ms.custom: ''
ms.openlocfilehash: 7d937542201792c0d1c0be69df9bd1c2b34edea3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004938"
---
# <a name="virtual-network-service-endpoints"></a>Koncové body služby pro virtuální síť

Koncový bod služby Virtual Network (VNet) poskytuje zabezpečené a přímé připojení ke službám Azure přes optimalizovanou trasu přes páteřní síť Azure. Koncové body umožňují svázat vaše důležité prostředky služeb Azure pouze s vašimi virtuálními sítěmi. Koncové body služby umožňují privátním IP adresám ve virtuální síti dosáhnout koncového bodu služby Azure bez nutnosti veřejné IP adresy ve virtuální síti.

Tato funkce je k dispozici pro následující služby a oblasti Azure. *Microsoft. \** Resource je v závorkách. Povolit tento prostředek ze strany podsítě při konfiguraci koncových bodů služby pro vaši službu:

**Obecná dostupnost**

- **[Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** (*Microsoft. Storage*): všeobecně dostupné ve všech oblastech Azure.
- **[Azure SQL Database](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): všeobecně dostupné ve všech oblastech Azure.
- **[Azure synapse Analytics](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): všeobecně dostupná ve všech oblastech Azure.
- **[Azure Database for PostgreSQL Server](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): všeobecně dostupné v oblastech Azure, kde je databázová služba k dispozici.
- **[Azure Database for MySQL server](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): všeobecně dostupné v oblastech Azure, kde je databázová služba k dispozici.
- **[Azure Database for MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)** (*Microsoft. SQL*): všeobecně dostupné v oblastech Azure, kde je databázová služba k dispozici.
- **[Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. AzureCosmosDB*): všeobecně dostupné ve všech oblastech Azure.
- **[Azure Key Vault](../key-vault/general/overview-vnet-service-endpoints.md)** (*Microsoft. klíčů trezor*): všeobecně dostupné ve všech oblastech Azure.
- **[Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. ServiceBus*): všeobecně dostupné ve všech oblastech Azure.
- **[Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. EventHub*): všeobecně dostupné ve všech oblastech Azure.
- **[Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. Azureactivedirectory selhala*): všeobecně dostupná ve všech oblastech Azure, kde je adls Gen1 k dispozici.
- **[Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)** (*Microsoft. Web*): všeobecně dostupné ve všech oblastech Azure, kde je dostupná služba App Service.
- **[Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-virtual-networks?tabs=portal)** (*Microsoft. Cognitiveservices Account*): všeobecně dostupné ve všech oblastech Azure, kde jsou k dispozici služby pro rozpoznávání.

**Public Preview**

- **[Azure Container Registry](../container-registry/container-registry-vnet.md)** (*Microsoft. ContainerRegistry*): verze Preview je dostupná v omezených oblastech Azure, kde je Azure Container Registry k dispozici.

Nejaktuálnější oznámení najdete na stránce [Aktualizace služby Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="key-benefits"></a>Klíčové výhody

Koncové body služby poskytují následující výhody:

- **Vylepšené zabezpečení prostředků služeb Azure**: soukromé adresní prostory virtuální sítě se můžou překrývat. K jednoznačné identifikaci provozu, který pochází z vaší virtuální sítě, nemůžete použít překrývající se mezery. Koncové body služby poskytují možnost zabezpečit prostředky služeb Azure ve vaší virtuální síti rozšířením identity virtuální sítě na službu. Po povolení koncových bodů služby ve virtuální síti můžete přidat pravidlo virtuální sítě pro zabezpečení prostředků služby Azure do vaší virtuální sítě. Přidání pravidla nabízí lepší zabezpečení díky úplnému odebrání veřejného internetového přístupu k prostředkům a povolení provozu pouze z vaší virtuální sítě.
- **Optimální směrování provozu služeb Azure z vaší virtuální sítě**: v současné době budou všechny trasy ve vaší virtuální síti, které vynucují internetový provoz na místní nebo virtuální zařízení, taky vynutit provoz služeb Azure, aby převzal stejnou trasu jako internetový provoz. Koncové body služby poskytují optimální směrování provozu Azure. 

  Koncové body vždy směrují provoz služby přímo z vaší virtuální sítě do služby v páteřní síti Microsoft Azure. Udržování provozu na páteřní síti Azure umožňuje pokračovat v auditování a monitorování odchozího internetového provozu z vašich virtuálních sítí prostřednictvím vynuceného tunelování, aniž by to mělo vliv na provoz služby. Další informace o trasách definovaných uživatelem a vynuceném tunelování najdete v tématu [směrování provozu virtuální sítě Azure](virtual-networks-udr-overview.md).
- **Snadné nastavení a méně režie na správu:** Ve virtuálních sítích už nepotřebujete vyhrazené veřejné IP adresy pro zabezpečení prostředků Azure prostřednictvím brány firewall protokolu IP. Pro nastavení koncových bodů služby není k dispozici žádný překlad síťových adres (NAT) nebo zařízení brány. Koncové body služby můžete nakonfigurovat jediným kliknutím na podsíť. Neexistují žádné další nároky na údržbu koncových bodů.

## <a name="limitations"></a>Omezení

- Tato funkce je dostupná pouze pro virtuální sítě nasazené pomocí modelu nasazení Azure Resource Manager.
- Koncové body jsou povolené na podsítích nakonfigurovaných ve virtuálních sítích Azure. Koncové body není možné použít pro provoz z vašich místních služeb do služeb Azure. Další informace najdete v tématu [zabezpečení přístupu ke službě Azure z místního](#secure-azure-services-to-virtual-networks) prostředí.
- Pro Azure SQL se koncový bod služby vztahuje jenom na provoz služeb Azure v rámci oblasti virtuální sítě. V případě Azure Storage se koncové body také rozšíří tak, aby zahrnovaly spárované oblasti, ve kterých nasadíte virtuální síť pro podporu provozu služby Read-Access Geo-Redundant Storage (RA-GRS) a úložiště Geo-Redundant (GRS). Další informace najdete v tématu [spárované oblasti Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
- V případě Azure Data Lake Storage (ADLS) 1. generace je možnost integrace virtuální sítě dostupná jenom pro virtuální sítě v rámci stejné oblasti. Také si všimněte, že integrace virtuální sítě pro ADLS Gen1 používá ke generování dalších deklarací zabezpečení v přístupovém tokenu zabezpečení koncového bodu služby virtuální sítě mezi vaší virtuální sítí a Azure Active Directory (Azure AD). Tyto deklarace identity pak slouží k ověření vaší virtuální sítě v účtu Data Lake Storage Gen1 a povolení přístupu. Značka *Microsoft. azureactivedirectory selhala* uvedená v části služby podporující koncové body služby se používá jenom pro podporu koncových bodů služby na ADLSu Gen 1. Azure AD nepodporuje nativně koncové body služby. Další informace o Azure Data Lake Store integraci virtuální sítě 1. generace najdete [v tématu zabezpečení sítě v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="secure-azure-services-to-virtual-networks"></a>Zabezpečení služeb Azure pro virtuální sítě

- Koncový bod služby pro virtuální síť poskytuje službě Azure identitu vaší virtuální sítě. Po povolení koncových bodů služby ve virtuální síti můžete přidat pravidlo virtuální sítě pro zabezpečení prostředků služby Azure do vaší virtuální sítě.
- Provoz služby Azure z virtuální sítě v současné době používá jako zdrojové IP adresy veřejné IP adresy. S koncovými body služby přepne provoz služby při přístupu ke službě Azure z vaší virtuální sítě na používání privátních adres virtuální sítě jako zdrojových IP adres. Toto přepnutí umožňuje přistupovat ke službám bez potřeby vyhrazených veřejných IP adres, které se používají v branách firewall protokolu IP.

   >[!NOTE]
   > S koncovými body služby se zdrojové IP adresy virtuálních počítačů v podsíti pro obsluhu provozu přepnou z používání veřejných IPv4 adres na privátní IPv4 adresy. Stávající pravidla bran firewall služeb Azure používající veřejné IP adresy Azure přestanou okamžikem přepnutí fungovat. Před nastavováním koncových bodů služby se ujistěte, že pravidla bran firewall služeb Azure toto přepnutí podporují. Při konfiguraci koncových bodů služby může také dojít k dočasnému přerušení provozu služby z této podsítě. 
 
## <a name="secure-azure-service-access-from-on-premises"></a>Zabezpečení přístupu ke službě Azure z místního prostředí

  Ve výchozím nastavení nejsou prostředky služeb Azure zabezpečené pro virtuální sítě dosažitelné z místních sítí. Pokud chcete povolený provoz z místního prostředí, musíte taky z místního nebo ExpressRoute povolených IP adres veřejných (obvykle NAT). Tyto IP adresy můžete přidat prostřednictvím konfigurace brány firewall protokolu IP pro prostředky služeb Azure.

  ExpressRoute: Pokud používáte [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pro veřejné partnerské vztahy nebo partnerské vztahy Microsoftu z vašich místních prostředí, budete muset identifikovat IP adresy NAT, které používáte. U veřejných partnerských vztahů každý okruh ExpressRoute používá ve výchozím nastavení dvě IP adresy překladu adres (NAT), které se ve výchozím nastavení použijí na provoz služeb Azure, když přenos vstoupí do sítě Microsoft Azure páteřní sítě. U partnerského vztahu Microsoftu jsou IP adresy NAT buď poskytnuté zákazníkem, nebo poskytované poskytovatelem služeb. Pokud chcete povolit přístup k prostředkům služby, musíte tyto veřejné IP adresy povolit v nastavení IP adresy brány firewall prostředku. Pokud chcete zjistit IP adresy veřejného partnerského okruhu ExpressRoute, [otevřete lístek podpory pro ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) na webu Azure Portal. Další informace o překladu adres (NAT) pro veřejný partnerský vztah ExpressRoute a partnerského vztahu Microsoftu najdete v článku [požadavky na EXPRESSROUTE NAT](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

![Svázání služeb Azure s virtuálními sítěmi](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Konfigurace

- Konfigurace koncových bodů služby v podsíti ve virtuální síti. Koncové body fungují s jakýmkoli typem výpočetních instancí spuštěných v rámci této podsítě.
- V podsíti můžete nakonfigurovat několik koncových bodů služby pro všechny podporované služby Azure (například Azure Storage nebo Azure SQL Database).
- Pro Azure SQL Database musí být virtuální sítě ve stejné oblasti jako prostředek služby Azure. Pokud používáte pro Azure Storage účty GRS a RA-GRS, primární účet musí být ve stejné oblasti jako virtuální síť. Pro všechny ostatní služby můžete zabezpečit prostředky služeb Azure pro virtuální sítě v libovolné oblasti. 
- Virtuální síť, ve které je koncový bod nakonfigurovaný, může být ve stejném předplatném jako prostředek služby Azure nebo v jiném předplatném. Další informace o oprávněních požadovaných pro nastavení koncových bodů a zabezpečení služeb Azure najdete v části [Zřizování](#provisioning).
- U podporovaných služeb můžete pomocí koncových bodů služby svázat s virtuálními sítěmi nové nebo existující prostředky.

### <a name="considerations"></a>Požadavky

- Po povolení koncového bodu služby se zdrojová IP adresa přepne z použití veřejných IPv4 adres na používání jejich privátní IPv4 adresy při komunikaci se službou z této podsítě. Během tohoto přepnutí se ukončí všechna existující otevřená připojení TCP ke službě. Při povolování nebo zakazování koncového bodu služby pro podsíť se ujistěte, že nejsou spuštěné žádné důležité úlohy. Také se ujistěte, že se vaše aplikace můžou po přepnutí IP adres automaticky připojit ke službám Azure.

  Přepnutí IP adres ovlivní pouze provoz služeb z vaší virtuální sítě. Neexistuje žádný vliv na žádný jiný provoz adresovaný na veřejné IPv4 adresy přiřazené vašim virtuálním počítačům nebo z nich. Pokud máte pro služby Azure existující pravidla brány firewall používající veřejné IP adresy Azure, tato pravidla s přepnutím na privátní adresy virtuální sítě přestanou fungovat.
- S koncovými body služby zůstávají záznamy DNS pro služby Azure tak, jak jsou, a nadále se překládají na veřejné IP adresy přiřazené službě Azure.

- Skupiny zabezpečení sítě (NSG) s koncovými body služby:
  - Ve výchozím nastavení skupin zabezpečení sítě povoluje odchozí internetový provoz a také umožňuje provoz z vaší virtuální sítě do služeb Azure. Tento provoz bude i nadále pracovat s koncovými body služby, jak je. 
  - Pokud chcete zakázat veškerý odchozí internetový provoz a povolit jenom přenosy na konkrétní služby Azure, můžete to udělat pomocí [značek služeb ve službě](security-overview.md#service-tags) skupin zabezpečení sítě. V pravidlech NSG můžete zadat podporované služby Azure jako cíl a Azure taky poskytuje údržbu IP adres, které jsou základem jednotlivých značek. Další informace najdete v tématu [Značky služeb Azure pro skupiny zabezpečení sítě](security-overview.md#service-tags). 

### <a name="scenarios"></a>Scénáře

- **Partnerské, propojené nebo vícenásobné virtuální sítě:** Pokud chcete svázat služby Azure s několika podsítěmi v rámci virtuální sítě nebo mezi několika virtuálními sítěmi, můžete koncové body služby povolit v každé z podsítí nezávisle na sobě a svázat tak prostředky služeb Azure se všemi podsítěmi.
- **Filtrování odchozího provozu z virtuální sítě do služeb Azure**: Pokud chcete zkontrolovat nebo filtrovat provoz odeslaný do služby Azure z virtuální sítě, můžete v rámci virtuální sítě nasadit síťové virtuální zařízení. Potom můžete na podsíť s nasazeným síťovým virtuálním zařízením použít koncové body služby a svázat prostředky služby Azure pouze s touto podsítí. Tento scénář může být užitečný, pokud chcete pomocí filtrování síťových virtuálních zařízení omezit přístup služby Azure z vaší virtuální sítě jenom na konkrétní prostředky Azure. Další informace najdete v popisu [výchozího přenosu dat se síťovými virtuálními zařízeními](/azure/architecture/reference-architectures/dmz/nva-ha).
- **Zabezpečení prostředků Azure pro služby nasazené přímo do virtuálních sítí**: různé služby Azure můžete nasadit přímo do konkrétních podsítí ve virtuální síti. Prostředky služby Azure můžete zabezpečit pro podsítě [spravované služby](virtual-network-for-azure-services.md) nastavením koncového bodu služby v podsíti spravované služby.
- **Přenos disku z virtuálního počítače Azure**: provoz disku virtuálního počítače pro spravované a nespravované disky nemá vliv na změny směrování koncových bodů služby pro Azure Storage. Tento provoz zahrnuje diskIO a také připojení a odpojení. Můžete omezit přístup REST k objektům blob stránky a vybrat sítě prostřednictvím koncových bodů služby a [Azure Storagech síťových pravidel](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

### <a name="logging-and-troubleshooting"></a>Protokolování a řešení potíží

Po nakonfigurování koncových bodů služby na určitou službu ověřte, zda je směrování koncového bodu služby v platnosti: 
 
- Ověření zdrojové IP adresy každé žádosti o služby v diagnostice služby. U všech nových žádostí pomocí koncových bodů služby se jako zdrojová IP adresa žádosti zobrazí privátní IP adresa virtuální sítě, která je přiřazená klientovi provádějícímu žádost z vaší virtuální sítě. Bez koncového bodu je tato adresa veřejnou IP adresou Azure.
- Zobrazení efektivních tras na všech síťových rozhraních v podsíti. Trasa ke službě:
  - Ukazuje konkrétnější výchozí trasu k rozsahu předpon adresy každé služby.
  - Má jako typ dalšího segmentu směrování *VirtualNetworkServiceEndpoint*.
  - Indikuje, že se v porovnání s trasami vynuceného tunelování používá přímější připojení ke službě.

>[!NOTE]
> Trasy koncového bodu služby přepíší všechny trasy BGP nebo UDR pro shodu předpon adresy služby Azure. Další informace najdete v tématu [řešení potíží s efektivními trasami](diagnose-network-routing-problem.md).

## <a name="provisioning"></a>Zřizování

Koncové body služby je možné konfigurovat na virtuálních sítích nezávisle na uživateli s oprávněním k zápisu do virtuální sítě. Aby bylo možné zabezpečit prostředky služeb Azure na virtuální síť, musí mít uživatel oprávnění k *Microsoft. Network/virtualNetworks/subnets/joinViaServiceEndpoint/Action* pro přidané podsítě. Ve výchozím nastavení zahrnují předdefinované role správce služby toto oprávnění. Oprávnění můžete upravit vytvořením vlastních rolí.

Další informace o předdefinovaných rolích najdete v tématu [předdefinované role Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Další informace o přiřazení konkrétních oprávnění k vlastním rolím najdete v tématu [vlastní role Azure](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Virtuální sítě a prostředky služeb Azure můžou být ve stejném předplatném nebo v různých předplatných. Pokud jsou virtuální síť a prostředky služeb Azure v různých předplatných, musí být prostředky ve stejném tenantovi Active Directory (AD). 

## <a name="pricing-and-limits"></a>Ceny a omezení

Za použití koncových bodů služby se neúčtují žádné další poplatky. Aktuální cenový model pro služby Azure (Azure Storage, Azure SQL Database atd.) se používá v dnešní době.

Celkový počet koncových bodů služby ve virtuální síti není nijak omezený.

Některé služby Azure, například účty Azure Storage, můžou vymáhat omezení počtu podsítí používaných k zabezpečení prostředku. Podrobnosti najdete v dokumentaci k různým službám v části [Další kroky](#next-steps) .

## <a name="vnet-service-endpoint-policies"></a>Zásady koncového bodu služby virtuální sítě 

Zásady koncového bodu služby virtuální sítě umožňují filtrovat provoz virtuální sítě do služeb Azure. Tento filtr povoluje jenom určité prostředky služeb Azure nad koncovými body služby. Zásady koncového bodu služby poskytují podrobné řízení přístupu k provozu virtuální sítě do služeb Azure. Další informace najdete v tématu [Virtual Network zásady koncového bodu služby](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview).

## <a name="faqs"></a>Nejčastější dotazy

Nejčastější dotazy najdete v tématu [Virtual Network časté otázky týkající se koncového bodu služby](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints).

## <a name="next-steps"></a>Další kroky

- [Konfigurace koncových bodů služby pro virtuální síť](tutorial-restrict-network-access-to-resources.md)
- [Zabezpečení účtu Azure Storage k virtuální síti](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Zabezpečení Azure SQL Database k virtuální síti](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Zabezpečení služby Azure synapse Analytics na virtuální síť](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Integrace služeb Azure ve virtuálních sítích](virtual-network-for-azure-services.md)
- [Virtual Network zásady koncového bodu služby](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)
- [Šablona Azure Resource Manageru](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)
