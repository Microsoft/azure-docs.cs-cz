---
title: Koncové body virtuální síťové služby Azure
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
ms.openlocfilehash: b75e0c1e53f1e00579de73897197cdd2f14d79af
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455406"
---
# <a name="virtual-network-service-endpoints"></a>Koncové body služby pro virtuální síť

Koncové body služby Virtuální síť (Virtuální síť) rozšiřují privátní adresní prostor virtuální sítě. Koncové body také rozšířit identitu vaší virtuální sítě na služby Azure přes přímé připojení. Koncové body umožňují svázat vaše důležité prostředky služeb Azure pouze s vašimi virtuálními sítěmi. Provoz z vaší virtuální sítě do služby Azure vždy zůstává v páteřní síti Microsoft Azure.

Tato funkce je dostupná pro následující služby a oblasti Azure. Prostředek *společnosti\* Microsoft* je v závorce. Povolte tento prostředek ze strany podsítě při konfiguraci koncových bodů služby pro vaši službu:

**Obecná dostupnost**

- **[Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** (*Microsoft.Storage):* Obecně dostupné ve všech oblastech Azure.
- **[Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql):* Obecně dostupné ve všech oblastech Azure.
- **[Azure SQL Data Warehouse](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *(Microsoft.Sql):* Obecně dostupné ve všech oblastech Azure.
- **[Azure Database for PostgreSQL server](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Obecně dostupné v oblastech Azure, kde je dostupná databázová služba.
- **[Azure Database for MySQL server](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.Sql*): Obecně dostupné v oblastech Azure, kde je dostupná databázová služba.
- **[Azure Database for MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)** (*Microsoft.Sql*): Obecně dostupné v oblastech Azure, kde je dostupná databázová služba.
- **[Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *(Microsoft.AzureCosmosDB):* Obecně dostupné ve všech oblastech Azure.
- **[Azure Key Vault](../key-vault/general/overview-vnet-service-endpoints.md)** (*Microsoft.KeyVault):* Obecně dostupné ve všech oblastech Azure.
- **[Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.ServiceBus):* Obecně dostupné ve všech oblastech Azure.
- **[Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *(Microsoft.EventHub):* Obecně dostupné ve všech oblastech Azure.
- **[Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *(Microsoft.AzureActiveDirectory):* Obecně dostupné ve všech oblastech Azure, kde je k dispozici ADLS Gen1.
- **[Azure App Service:](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)** Obecně dostupné ve všech oblastech Azure, kde je služba App service dostupná.

**Veřejná verze Preview**

- **[Azure Container Registry](../container-registry/container-registry-vnet.md)** *(Microsoft.ContainerRegistry):* Preview je k dispozici ve všech oblastech Azure, kde je k dispozici Azure Container Registry.

Nejaktuálnější oznámení najdete na stránce [Aktualizace služby Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="key-benefits"></a>Klíčové výhody

Koncové body služby poskytují následující výhody:

- **Vylepšené zabezpečení prostředků služeb Azure:** Privátní adresní prostory virtuální sítě se můžou překrývat. Překrývající se mezery nelze použít k jednoznačné identifikaci provozu, který pochází z vaší virtuální sítě. Koncové body služby poskytují možnost zabezpečit prostředky služeb Azure do vaší virtuální sítě rozšířením identity virtuální sítě na službu. Jakmile povolíte koncové body služby ve vaší virtuální síti, můžete přidat pravidlo virtuální sítě pro zabezpečení prostředků služby Azure do vaší virtuální sítě. Přidání pravidla poskytuje lepší zabezpečení tím, že plně odstraní veřejný přístup k internetu k prostředkům a povolí provoz pouze z vaší virtuální sítě.
- **Optimální směrování pro provoz služeb Azure z vaší virtuální sítě**: Všechny trasy ve vaší virtuální síti, které vynucují internetový provoz do místního a/nebo virtuálního zařízení, dnes také nutí provoz služeb Azure směrovat stejnou trasu jako internetový provoz. Koncové body služby poskytují optimální směrování provozu Azure. 

  Koncové body vždy směrují provoz služby přímo z vaší virtuální sítě do služby v páteřní síti Microsoft Azure. Udržování provozu na páteřní síti Azure umožňuje pokračovat v auditování a monitorování odchozího internetového provozu z vašich virtuálních sítí prostřednictvím vynuceného tunelování, aniž by to mělo vliv na provoz služby. Další informace o uživatelem definovaných trasách a vynuceném tunelovém propojení najdete v tématu [Směrování provozu virtuální sítě Azure](virtual-networks-udr-overview.md).
- **Snadné nastavení a méně režie na správu:** Ve virtuálních sítích už nepotřebujete vyhrazené veřejné IP adresy pro zabezpečení prostředků Azure prostřednictvím brány firewall protokolu IP. K nastavení koncových bodů služby nejsou potřeba žádná zařízení pro překlad síťových adres (NAT) ani zařízení brány. Koncové body služby můžete nakonfigurovat jednoduchým kliknutím na podsíť. Neexistuje žádná další režie na údržbu koncových bodů.

## <a name="limitations"></a>Omezení

- Tato funkce je dostupná pouze pro virtuální sítě nasazené pomocí modelu nasazení Azure Resource Manager.
- Koncové body jsou povolené na podsítích nakonfigurovaných ve virtuálních sítích Azure. Koncové body nelze použít pro provoz z vašich prostor do služeb Azure. Další informace najdete [v tématu Zabezpečený přístup ke službám Azure z místního prostředí.](#secure-azure-services-to-virtual-networks)
- Pro Azure SQL se koncový bod služby vztahuje jenom na provoz služeb Azure v rámci oblasti virtuální sítě. Pro Azure Storage se koncové body také rozšiřují tak, aby zahrnovaly spárované oblasti, kde nasadíte virtuální síť pro podporu geograficky redundantního úložiště pro čtení (RA-GRS) a geograficky redundantního úložiště (GRS). Další informace najdete v tématu [Azure spárované oblasti](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
- Pro Azure Data Lake Storage (ADLS) Gen 1, možnost integrace virtuální sítě je k dispozici pouze pro virtuální sítě ve stejné oblasti. Všimněte si také, že integrace virtuální sítě pro ADLS Gen1 používá zabezpečení koncového bodu služby virtuální sítě mezi vaší virtuální sítí a Azure Active Directory (Azure AD) ke generování dalších deklarací zabezpečení v přístupovém tokenu. Tyto deklarace identity pak slouží k ověření vaší virtuální sítě v účtu Data Lake Storage Gen1 a povolení přístupu. Značka *Microsoft.AzureActiveDirectory* uvedená v části služby podporující koncové body služby se používá pouze pro podporu koncových bodů služby na ADLS Gen 1. Azure AD nepodporuje koncové body služby nativně. Další informace o integraci virtuální sítě Virtuální sítě Virtuální sítě Azure Data Lake Store 1 najdete [v tématu Zabezpečení sítě v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="secure-azure-services-to-virtual-networks"></a>Zabezpečení služeb Azure do virtuálních sítí

- Koncový bod služby pro virtuální síť poskytuje službě Azure identitu vaší virtuální sítě. Jakmile povolíte koncové body služby ve vaší virtuální síti, můžete přidat pravidlo virtuální sítě pro zabezpečení prostředků služby Azure do vaší virtuální sítě.
- Provoz služby Azure z virtuální sítě v současné době používá jako zdrojové IP adresy veřejné IP adresy. S koncovými body služby přepne provoz služby při přístupu ke službě Azure z vaší virtuální sítě na používání privátních adres virtuální sítě jako zdrojových IP adres. Toto přepnutí umožňuje přistupovat ke službám bez potřeby vyhrazených veřejných IP adres, které se používají v branách firewall protokolu IP.

   >[!NOTE]
   > S koncovými body služby se zdrojové IP adresy virtuálních počítačů v podsíti pro obsluhu provozu přepnou z používání veřejných IPv4 adres na privátní IPv4 adresy. Stávající pravidla bran firewall služeb Azure používající veřejné IP adresy Azure přestanou okamžikem přepnutí fungovat. Před nastavováním koncových bodů služby se ujistěte, že pravidla bran firewall služeb Azure toto přepnutí podporují. Při konfiguraci koncových bodů služby může také dojít k dočasnému přerušení provozu služby z této podsítě. 
 
## <a name="secure-azure-service-access-from-on-premises"></a>Zabezpečený přístup ke službám Azure z místního prostředí

  Ve výchozím nastavení nejsou prostředky služeb Azure zabezpečené k virtuálním sítím dostupné z místních sítí. Pokud chcete povolit provoz z místního prostředí, musíte také povolit veřejné (obvykle NAT) IP adresy z vašeho místního prostředí nebo ExpressRoute. Tyto IP adresy můžete přidat prostřednictvím konfigurace brány firewall IP pro prostředky služeb Azure.

  ExpressRoute: Pokud používáte [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) pro veřejný partnerský vztah nebo partnerský vztah Microsoftu z vašich prostor, budete muset identifikovat IP adresy NAT, které používáte. Pro veřejný partnerský vztah používá každý okruh ExpressRoute ve výchozím nastavení dvě IP adresy NAT, které se ve výchozím nastavení používají pro provoz služeb Azure, když provoz přejde do páteřní sítě Microsoft Azure. V případě partnerského vztahu microsoftu jsou IP adresy NAT poskytovány nebo poskytovány poskytovatelem služeb.Pokud chcete povolit přístup k prostředkům služby, musíte tyto veřejné IP adresy povolit v nastavení IP adresy brány firewall prostředku.Pokud chcete zjistit IP adresy veřejného partnerského okruhu ExpressRoute, [otevřete lístek podpory pro ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) na webu Azure Portal. Další informace o nat pro ExpressRoute veřejné a Microsoft peering, naleznete v [tématu ExpressRoute NAT požadavky](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

![Svázání služeb Azure s virtuálními sítěmi](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Konfigurace

- Konfigurace koncových bodů služby v podsíti ve virtuální síti. Koncové body fungují s jakýmkoli typem výpočetních instancí spuštěných v rámci této podsítě.
- Můžete nakonfigurovat více koncových bodů služby pro všechny podporované služby Azure (Azure Storage nebo Azure SQL Database, například) v podsíti.
- Pro Azure SQL Database musí být virtuální sítě ve stejné oblasti jako prostředek služby Azure. Pokud používáte pro Azure Storage účty GRS a RA-GRS, primární účet musí být ve stejné oblasti jako virtuální síť. Pro všechny ostatní služby můžete zabezpečit prostředky služeb Azure do virtuálních sítí v libovolné oblasti. 
- Virtuální síť, ve které je koncový bod nakonfigurovaný, může být ve stejném předplatném jako prostředek služby Azure nebo v jiném předplatném. Další informace o oprávněních požadovaných pro nastavení koncových bodů a zabezpečení služeb Azure najdete v části [Zřizování](#provisioning).
- U podporovaných služeb můžete pomocí koncových bodů služby svázat s virtuálními sítěmi nové nebo existující prostředky.

### <a name="considerations"></a>Požadavky

- Po povolení koncového bodu služby, zdrojové IP adresy virtuálních počítačů v přepínači podsítě. Zdrojové adresy IP přecházejí z používání veřejných adres IPv4 na použití své privátní adresy IPv4 při komunikaci se službou z této podsítě. Během tohoto přepnutí se ukončí všechna existující otevřená připojení TCP ke službě. Při povolování nebo zakazování koncového bodu služby pro podsíť se ujistěte, že nejsou spuštěné žádné důležité úlohy. Také se ujistěte, že se vaše aplikace můžou po přepnutí IP adres automaticky připojit ke službám Azure.

  Přepnutí IP adres ovlivní pouze provoz služeb z vaší virtuální sítě. Neexistuje žádný vliv na jakýkoli jiný provoz adresovaný na nebo z veřejných adres IPv4 přiřazených k vašim virtuálním počítačům. Pokud máte pro služby Azure existující pravidla brány firewall používající veřejné IP adresy Azure, tato pravidla s přepnutím na privátní adresy virtuální sítě přestanou fungovat.
- S koncovými body služby položky DNS pro služby Azure zůstávají jako dnes a nadále řešit na veřejné IP adresy přiřazené ke službě Azure.

- Skupiny zabezpečení sítě (NSG) s koncovými body služby:
  - Ve výchozím nastavení povolují sítě zabezpečení sítě odchozí internetový provoz a také umožňují provoz z vaší virtuální sítě do služeb Azure. Tento provoz nadále pracovat s koncovými body služby, jak je. 
  - Pokud chcete odepřít veškerý odchozí internetový provoz a povolit jenom provoz na konkrétní služby Azure, můžete tak učinit pomocí [značek služeb](security-overview.md#service-tags) ve svých souborech zabezpečení sítě. V pravidlech souboru zabezpečení zabezpečení zabezpečení dat můžete jako cíl zadat podporované služby Azure a Azure také poskytuje údržbu IP adres, které jsou základem jednotlivých značek. Další informace najdete v tématu [Značky služeb Azure pro skupiny zabezpečení sítě](security-overview.md#service-tags). 

### <a name="scenarios"></a>Scénáře

- **Partnerské, propojené nebo vícenásobné virtuální sítě:** Pokud chcete svázat služby Azure s několika podsítěmi v rámci virtuální sítě nebo mezi několika virtuálními sítěmi, můžete koncové body služby povolit v každé z podsítí nezávisle na sobě a svázat tak prostředky služeb Azure se všemi podsítěmi.
- **Filtrování odchozích přenosů z virtuální sítě do služeb Azure**: Pokud chcete zkontrolovat nebo filtrovat provoz odeslaný do služby Azure z virtuální sítě, můžete v rámci virtuální sítě nasadit síťové virtuální zařízení. Potom můžete na podsíť s nasazeným síťovým virtuálním zařízením použít koncové body služby a svázat prostředky služby Azure pouze s touto podsítí. Tento scénář může být užitečné, pokud chcete použít filtrování virtuálních síťových zařízení omezit přístup ke službám Azure z vaší virtuální sítě jenom na konkrétní prostředky Azure. Další informace najdete v popisu [výchozího přenosu dat se síťovými virtuálními zařízeními](/azure/architecture/reference-architectures/dmz/nva-ha).
- **Zabezpečení prostředků Azure na služby nasazené přímo do virtuálních sítí**: Můžete přímo nasadit různé služby Azure do konkrétních podsítí ve virtuální síti. Prostředky služeb Azure můžete zabezpečit pro podsítě [spravovaných služeb](virtual-network-for-azure-services.md) nastavením koncového bodu služby v podsíti spravované služby.
- **Provoz disku z virtuálního počítače Azure**: Provoz na disku virtuálního počítače pro spravované a nespravované disky nejsou ovlivněny změnami směrování koncových bodů služby pro Azure Storage. Tento provoz zahrnuje diskIO, stejně jako připojit a odpojit. Přístup REST můžete omezit na objekty BLOB stránky a vybrat sítě prostřednictvím koncových bodů služby a [pravidel sítě Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

### <a name="logging-and-troubleshooting"></a>Protokolování a řešení potíží

Po konfiguraci koncových bodů služby na konkrétní službu ověřte, zda je trasa koncového bodu služby platná: 
 
- Ověření zdrojové IP adresy každé žádosti o služby v diagnostice služby. U všech nových žádostí pomocí koncových bodů služby se jako zdrojová IP adresa žádosti zobrazí privátní IP adresa virtuální sítě, která je přiřazená klientovi provádějícímu žádost z vaší virtuální sítě. Bez koncového bodu je tato adresa veřejnou IP adresou Azure.
- Zobrazení efektivních tras na všech síťových rozhraních v podsíti. Trasa ke službě:
  - Ukazuje konkrétnější výchozí trasu k rozsahu předpon adresy každé služby.
  - Má jako typ dalšího segmentu směrování *VirtualNetworkServiceEndpoint*.
  - Označuje, že ve srovnání s libovolnými trasami vynuceného tunelování je v platnosti přímější připojení ke službě.

>[!NOTE]
> Trasy koncového bodu služby přepíší všechny trasy BGP nebo UDR pro shodu předpon adresy služby Azure. Další informace naleznete v [tématu řešení potíží s efektivními trasami](diagnose-network-routing-problem.md).

## <a name="provisioning"></a>Zřizování

Koncové body služby lze konfigurovat ve virtuálních sítích nezávisle uživatelem s přístupem pro zápis do virtuální sítě. Chcete-li zabezpečit prostředky služby Azure do virtuální sítě, musí mít uživatel oprávnění k *Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action* pro přidané podsítě. Předdefinované role správce služby zahrnují toto oprávnění ve výchozím nastavení. Oprávnění můžete upravit vytvořením vlastních rolí.

Další informace o předdefinovaných rolích najdete [v tématu Předdefinované role pro prostředky Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Další informace o přiřazení konkrétních oprávnění k vlastním rolím najdete [v tématu Vlastní role pro prostředky Azure](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Virtuální sítě a prostředky služeb Azure můžou být ve stejném předplatném nebo v různých předplatných. Pokud jsou virtuální síť a prostředky služeb Azure v různých předplatných, musí být prostředky ve stejném tenantovi Active Directory (AD). 

## <a name="pricing-and-limits"></a>Ceny a omezení

Za použití koncových bodů služby se neplatí žádné další poplatky. Aktuální cenový model pro služby Azure (Azure Storage, Azure SQL Database atd.) platí jako dnes.

Neexistuje žádné omezení na celkový počet koncových bodů služby ve virtuální síti.

Některé služby Azure, jako jsou účty úložiště Azure, mohou vynutit omezení počtu podsítí používaných k zabezpečení prostředku. Podrobnosti naleznete v dokumentaci k různým službám v části [Další kroky.](#next-steps)

## <a name="vnet-service-endpoint-policies"></a>Zásady koncového bodu služby virtuální sítě 

Zásady koncového bodu služby Virtuální sítě umožňují filtrovat provoz virtuální sítě do služeb Azure. Tento filtr umožňuje pouze konkrétní prostředky služby Azure přes koncové body služby. Zásady koncového bodu služby poskytují podrobné řízení přístupu pro provoz virtuální chod sítě do služeb Azure. Další informace naleznete v tématu [Zásady koncového bodu služby Virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview).

## <a name="faqs"></a>Nejčastější dotazy

Nejčastější dotazy naleznete v tématu [Nejčastější dotazy ke koncovým bodům služby virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints).

## <a name="next-steps"></a>Další kroky

- [Konfigurace koncových bodů služby pro virtuální síť](tutorial-restrict-network-access-to-resources.md)
- [Zabezpečení účtu Azure Storage do virtuální sítě](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Zabezpečení azure sql databáze do virtuální sítě](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Zabezpečení datového skladu Azure SQL do virtuální sítě](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Integrace služeb Azure ve virtuálních sítích](virtual-network-for-azure-services.md)
- [Zásady koncového bodu služby Virtuální síť](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)
- [Šablona Azure Resource Manageru](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)

