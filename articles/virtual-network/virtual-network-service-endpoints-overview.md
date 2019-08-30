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
ms.date: 08/15/2018
ms.author: sumi
ms.custom: ''
ms.openlocfilehash: 8420142e67fe4af12045a2b6fe7f7461ef384f81
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70164477"
---
# <a name="virtual-network-service-endpoints"></a>Koncové body služby virtuální sítě

Koncové body služby virtuální sítě rozšiřují privátní adresní prostor vaší virtuální sítě a její identitu do služeb Azure přes přímé připojení. Koncové body umožňují svázat vaše důležité prostředky služeb Azure pouze s vašimi virtuálními sítěmi. Provoz z vaší virtuální sítě do služby Azure vždy zůstává v páteřní síti Microsoft Azure.

Tato funkce je dostupná pro následující služby a oblasti Azure a v závorkách najdete taky prostředek Microsoft. *, který musí být povolený ze strany podsítě při konfiguraci koncových bodů služby pro vaši službu:

**Obecná dostupnost**

- **[Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** (Microsoft. Storage): Všeobecně dostupná ve všech oblastech Azure.
- **[Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (Microsoft. SQL): Všeobecně dostupná ve všech oblastech Azure.
- **[Azure SQL Data Warehouse](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (Microsoft. SQL): Všeobecně dostupná ve všech oblastech Azure.
- **[Server Azure Database for PostgreSQL](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (Microsoft. SQL): Obecně dostupné v oblastech Azure, kde je databázová služba k dispozici.
- **[Server Azure Database for MySQL](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (Microsoft. SQL): Obecně dostupné v oblastech Azure, kde je databázová služba k dispozici.
- **[Azure Database for MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)** (Microsoft. SQL): Obecně dostupné v oblastech Azure, kde je databázová služba k dispozici.
- **[Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (Microsoft. AzureCosmosDB): Všeobecně dostupná ve všech oblastech Azure.
- **[Azure Key Vault](../key-vault/key-vault-overview-vnet-service-endpoints.md)** (Trezor Microsoft. klíčů): Všeobecně dostupná ve všech oblastech Azure.
- **[Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (Microsoft. ServiceBus): Všeobecně dostupná ve všech oblastech Azure.
- **[Event Hubs Azure](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (Microsoft. EventHub): Všeobecně dostupná ve všech oblastech Azure.
- **[Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (Microsoft. Azureactivedirectory selhala): Všeobecně dostupná ve všech oblastech Azure, kde je ADLS Gen1 k dispozici.
- **[Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)** : Obecně dostupné ve všech oblastech Azure, kde je dostupná služba App Service

**Public Preview**

- **[Azure Container Registry](../container-registry/container-registry-vnet.md)** (Microsoft. ContainerRegistry): Verze Preview je dostupná ve všech oblastech Azure, kde je Azure Container Registry k dispozici.

Nejaktuálnější oznámení najdete na stránce [Aktualizace služby Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="key-benefits"></a>Klíčové výhody

Koncové body služby poskytují následující výhody:

- **Vylepšené zabezpečení prostředků služeb Azure**: Privátní adresní prostor virtuální sítě se může překrývat a takže ho nejde použít k jednoznačné identifikaci provozu, který pochází z vaší virtuální sítě. Koncové body služeb umožňují zabezpečit prostředky služeb Azure ve vaší virtuální síti tím, že rozšíří identitu virtuální sítě na službu. Jakmile budou ve vaší virtuální síti povolené koncové body služby, můžete svázat prostředky služeb Azure s virtuální sítí tím, že do prostředků přidáte pravidlo virtuální sítě. Získáte tak lepší zabezpečení díky úplnému odebrání veřejného internetového přístupu k prostředkům a povolení provozu jenom z vaší virtuální sítě.
- **Optimální směrování provozu služeb Azure z vaší virtuální sítě**: V současné době všechny trasy ve vaší virtuální síti, které vynucují internetový provoz do vašich místních nebo virtuálních zařízení, označované jako vynucené tunelování, vynutí, aby provoz služeb Azure převzal stejnou trasu jako internetový provoz. Koncové body služby poskytují optimální směrování provozu Azure. 

  Koncové body vždy směrují provoz služby přímo z vaší virtuální sítě do služby v páteřní síti Microsoft Azure. Udržování provozu na páteřní síti Azure umožňuje pokračovat v auditování a monitorování odchozího internetového provozu z vašich virtuálních sítí prostřednictvím vynuceného tunelování, aniž by to mělo vliv na provoz služby. Další informace o [trasách definovaných uživatelem a vynuceném tunelování](virtual-networks-udr-overview.md)
- **Jednoduché nastavení s méně nároky na správu**: Ve virtuálních sítích už nepotřebujete vyhrazené veřejné IP adresy pro zabezpečení prostředků Azure prostřednictvím brány firewall protokolu IP. K nastavení koncových bodů služby se nevyžaduje překlad adres ani zařízení brány. Koncové body služby se konfigurují pouhým kliknutím na podsíť. S údržbou koncových bodů není spojena žádná další režie.

## <a name="limitations"></a>Omezení

- Tato funkce je dostupná pouze pro virtuální sítě nasazené pomocí modelu nasazení Azure Resource Manager.
- Koncové body jsou povolené na podsítích nakonfigurovaných ve virtuálních sítích Azure. Koncové body není možné použít pro provoz z místního prostředí do služeb Azure. Další informace najdete v části [Zabezpečení přístupu ke službám Azure z místního prostředí](#securing-azure-services-to-virtual-networks).
- Pro Azure SQL se koncový bod služby vztahuje jenom na provoz služeb Azure v rámci oblasti virtuální sítě. V případě služby Azure Storage je pro zajištění podpory provozu služby RA-GRS a úložiště GRS zahrnuta také spárovaná oblast, ve které je virtuální síť nasazená. Přečtete si další informace o [spárovaných oblastech Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
- V případě ADLS pro obecné 1 je funkce integrace virtuální sítě dostupná jenom pro virtuální sítě v rámci stejné oblasti. Všimněte si také, že integrace virtuální sítě pro Azure Data Lake Storage Gen1 využívá zabezpečení koncového bodu služby virtuální sítě mezi vaší virtuální sítí a Azure Active Directory (Azure AD) ke generování dalších deklarací zabezpečení v přístupovém tokenu. Tyto deklarace identity pak slouží k ověření vaší virtuální sítě v účtu Data Lake Storage Gen1 a povolení přístupu. Značka "Microsoft. Azureactivedirectory selhala" uvedená v části služby podporující koncové body služby se používá pouze pro podporu koncových bodů služby na ADLSu Gen 1. Azure Active Directory (Azure AD) nepodporují nativní koncové body služby. Přečtěte si další informace o [Azure Data Lake Store integraci virtuální sítě pro obecné 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="securing-azure-services-to-virtual-networks"></a>Svázání služeb Azure s virtuálními sítěmi

- Koncový bod služby virtuální sítě poskytuje službě Azure identitu vaší virtuální sítě. Jakmile budou ve vaší virtuální síti povolené koncové body služby, můžete svázat prostředky služeb Azure s virtuální sítí tím, že do prostředků přidáte pravidlo virtuální sítě.
- Provoz služby Azure z virtuální sítě v současné době používá jako zdrojové IP adresy veřejné IP adresy. S koncovými body služby přepne provoz služby při přístupu ke službě Azure z vaší virtuální sítě na používání privátních adres virtuální sítě jako zdrojových IP adres. Toto přepnutí umožňuje přistupovat ke službám bez potřeby vyhrazených veřejných IP adres, které se používají v branách firewall protokolu IP.

>[!NOTE]
> S koncovými body služby se zdrojové IP adresy virtuálních počítačů v podsíti pro obsluhu provozu přepnou z používání veřejných IPv4 adres na privátní IPv4 adresy. Stávající pravidla bran firewall služeb Azure používající veřejné IP adresy Azure přestanou okamžikem přepnutí fungovat. Před nastavováním koncových bodů služby se ujistěte, že pravidla bran firewall služeb Azure toto přepnutí podporují. Při konfiguraci koncových bodů služby může také dojít k dočasnému přerušení provozu služby z této podsítě. 
 
- __Zabezpečení přístupu ke službám Azure z místního prostředí__:

  Prostředky služeb Azure svázané s virtuálními sítěmi ve výchozím nastavení nejsou přístupné z místních sítí. Pokud chcete povolit provoz z místního prostředí, musíte také povolit veřejné IP adresy (obvykle pro překlad adres) z místních okruhů nebo okruhů ExpressRoute. Tyto IP adresy je možné přidat prostřednictvím konfigurace brány firewall protokolu IP pro prostředky služeb Azure.

  ExpressRoute: Pokud používáte [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) z vašich místních partnerských vztahů nebo partnerských vztahů Microsoftu, budete muset určit IP adresy NAT, které se používají. Ve veřejných partnerských vztazích každý okruh ExpressRoute automaticky využívá dvě IP adresy pro překlad adres (NAT), které se používají k provozu služeb Azure při vstupu do páteřní sítě Microsoft Azure. IP adresy pro překlad adres (NAT) používané v partnerských vztazích s Microsoftem poskytuje zákazník nebo poskytovatel služby. Pokud chcete povolit přístup k prostředkům služby, musíte tyto veřejné IP adresy povolit v nastavení IP adresy brány firewall prostředku. K vyhledání IP adresy veřejného partnerského okruhu ExpressRoute [otevřete lístek podpory pro ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) prostřednictvím webu Azure portal. Další informace o [překladu adres (NAT) pro veřejné partnerské vztahy a partnerské vztahy s Microsoftem v ExpressRoute.](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)

![Svázání služeb Azure s virtuálními sítěmi](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Konfigurace

- Koncové body služby se konfigurují v podsíti ve virtuální síti. Koncové body fungují s jakýmkoli typem výpočetních instancí spuštěných v rámci této podsítě.
- V podsíti můžete nakonfigurovat několik koncových bodů služby pro všechny podporované služby Azure (například Azure Storage nebo Azure SQL Database).
- Pro Azure SQL Database musí být virtuální sítě ve stejné oblasti jako prostředek služby Azure. Pokud používáte pro Azure Storage účty GRS a RA-GRS, primární účet musí být ve stejné oblasti jako virtuální síť. Pro všechny ostatní služby můžou být prostředky služby Azure zabezpečené ve virtuálních sítích v libovolné oblasti. 
- Virtuální síť, ve které je koncový bod nakonfigurovaný, může být ve stejném předplatném jako prostředek služby Azure nebo v jiném předplatném. Další informace o oprávněních požadovaných pro nastavení koncových bodů a zabezpečení služeb Azure najdete v části [Zřizování](#provisioning).
- U podporovaných služeb můžete pomocí koncových bodů služby svázat s virtuálními sítěmi nové nebo existující prostředky.

### <a name="considerations"></a>Požadavky

- Po povolení koncového bodu služby zdrojové IP adresy virtuálních počítačů v podsíti při komunikaci se službou z dané podsítě přepnou od používání veřejných IPv4 adres na používání vlastních privátních IPv4 adres. Během tohoto přepnutí se ukončí všechna existující otevřená připojení TCP ke službě. Při povolování nebo zakazování koncového bodu služby pro podsíť se ujistěte, že nejsou spuštěné žádné důležité úlohy. Také se ujistěte, že se vaše aplikace můžou po přepnutí IP adres automaticky připojit ke službám Azure.

  Přepnutí IP adres ovlivní pouze provoz služeb z vaší virtuální sítě. Veškerý ostatní provoz adresovaný na veřejné IPv4 adresy přiřazené vašim virtuálním počítačům nebo z nich nebude ovlivněn. Pokud máte pro služby Azure existující pravidla brány firewall používající veřejné IP adresy Azure, tato pravidla s přepnutím na privátní adresy virtuální sítě přestanou fungovat.
- S koncovými body služby zůstávají záznamy DNS pro služby Azure tak, jak jsou, a nadále se překládají na veřejné IP adresy přiřazené příslušné službě Azure.

- Skupiny zabezpečení sítě (NSG) s koncovými body služby:
  - Skupiny zabezpečení sítě ve výchozím nastavení povolují odchozí internetový provoz a tedy povolují i provoz z virtuální sítě do služeb Azure. S koncovými body služby to nadále funguje stejným způsobem. 
  - Pokud chcete odepřít veškerý odchozí internetový provoz a povolit pouze provoz do konkrétních služeb Azure, můžete to provést použitím [značek služeb](security-overview.md#service-tags) ve svých skupinách zabezpečení sítě. V pravidlech NSG můžete zadat podporované služby Azure jako cíl a Azure zajistí údržbu IP adres, na které jednotlivé značky odkazují. Další informace najdete v tématu [Značky služeb Azure pro skupiny zabezpečení sítě](security-overview.md#service-tags). 

### <a name="scenarios"></a>Scénáře

- **Partnerský vztah, připojená nebo více virtuálních sítí**: Pokud chcete zabezpečit služby Azure na více podsítí v rámci virtuální sítě nebo napříč více virtuálními sítěmi, můžete koncové body služby na každé z těchto podsítí povolit nezávisle a zabezpečit prostředky služeb Azure pro všechny podsítě.
- **Filtrování odchozího provozu z virtuální sítě do služeb Azure**: Pokud chcete prozkoumat nebo filtrovat provoz směřující do služby Azure z virtuální sítě, můžete v rámci virtuální sítě nasadit síťové virtuální zařízení. Potom můžete na podsíť s nasazeným síťovým virtuálním zařízením použít koncové body služby a svázat prostředky služby Azure pouze s touto podsítí. Tento scénář může být užitečný v případě, že chcete omezit přístup služby Azure z vaší virtuální sítě pouze ke konkrétním prostředkům Azure pomocí filtrování síťového virtuálního zařízení. Další informace najdete v popisu [výchozího přenosu dat se síťovými virtuálními zařízeními](/azure/architecture/reference-architectures/dmz/nva-ha).
- **Zabezpečení prostředků Azure pro služby nasazené přímo do virtuálních sítí**: Různé služby Azure je možné přímo nasadit do konkrétních podsítí ve virtuální síti. Prostředky služby Azure můžete svázat s podsítěmi [spravované služby](virtual-network-for-azure-services.md) nastavením koncového bodu služby v podsíti spravované služby.
- **Přenos z disku z virtuálního počítače Azure**: Změny směrování koncových bodů služby pro Azure Storage neovlivňují přenosy disků virtuálních počítačů (včetně připojení a odpojení, diskIO) pro spravované nebo nespravované disky. Přístup REST k objektům blob stránky můžete prostřednictvím koncových bodů služby a [pravidel sítě služby Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json) omezit na vybrané sítě. 

### <a name="logging-and-troubleshooting"></a>Protokolování a řešení potíží

Jakmile jsou koncové body služby pro konkrétní službu nakonfigurované, ověřte následujícím způsobem fungování trasy koncového bodu služby: 
 
- Ověření zdrojové IP adresy každé žádosti o služby v diagnostice služby. U všech nových žádostí pomocí koncových bodů služby se jako zdrojová IP adresa žádosti zobrazí privátní IP adresa virtuální sítě, která je přiřazená klientovi provádějícímu žádost z vaší virtuální sítě. Bez koncového bodu je tato adresa veřejnou IP adresou Azure.
- Zobrazení efektivních tras na všech síťových rozhraních v podsíti. Trasa ke službě:
  - Ukazuje konkrétnější výchozí trasu k rozsahu předpon adresy každé služby.
  - Má jako typ dalšího segmentu směrování *VirtualNetworkServiceEndpoint*.
  - Označuje, že se v porovnání s trasami vynuceného tunelování používá přímější připojení ke službě.

>[!NOTE]
> Trasy koncového bodu služby přepíší všechny trasy BGP nebo UDR pro shodu předpon adresy služby Azure. Další informace o [řešení potíží pomocí efektivních tras](diagnose-network-routing-problem.md)

## <a name="provisioning"></a>Zřizování

Koncové body služby může ve virtuálních sítích nezávisle na sobě konfigurovat uživatel s oprávněním k zápisu do virtuální sítě. Aby bylo možné zabezpečit prostředky služeb Azure pro virtuální síť, musí mít uživatel oprávnění k *Microsoft. Network/virtualNetworks/subnets/joinViaServiceEndpoint/Action* pro přidávané podsítě. Toto oprávnění je ve výchozím nastavení součástí předdefinovaných rolí správců služeb a může se upravit vytvořením vlastních rolí.

Další informace o [předdefinovaných rolích](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a přiřazení konkrétních oprávnění k [vlastním rolím](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Virtuální sítě a prostředky služeb Azure můžou být ve stejném předplatném nebo v různých předplatných. Pokud jsou virtuální síť a prostředky služeb Azure v různých předplatných, musí být prostředky ve stejném tenantovi Active Directory (AD). 

## <a name="pricing-and-limits"></a>Ceny a omezení

Používání koncových bodů služby je bez dalších poplatků. Používá se cenový model pro služby Azure (Azure Storage, Azure SQL Database apod.) v současné podobě.

Celkový počet koncových bodů služby ve virtuální síti není nijak omezený.

Některé služby Azure, například účty Azure Storage, můžou vymáhat omezení počtu podsítí používaných k zabezpečení prostředku. Podrobnosti najdete v dokumentaci k různým službám v části [Další kroky](#next-steps).

## <a name="virtual-network-service-endpoint-policies"></a>Zásady koncového bodu služby virtuální sítě 

Zásady koncových bodů služeb virtuální sítě umožňují filtrovat provoz do služeb Azure, umožňuje prostředky pouze konkrétních služeb Azure prostřednictvím koncových bodů služby virtuální sítě. Zásady koncového bodu služby poskytují podrobné řízení přístupu pro přenosy virtuální sítě do služeb Azure. Další informace: [Virtual Network zásady koncového bodu služby](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)

## <a name="faqs"></a>Nejčastější dotazy

Pro nejčastější dotazy, podívejte se na [nejčastější dotazy koncových bodů služby virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints)

## <a name="next-steps"></a>Další postup

- Naučte se [konfigurovat koncové body služby virtuální sítě](tutorial-restrict-network-access-to-resources.md).
- Naučte se [svázat účet služby Azure Storage s virtuální sítí](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Naučte se [svázat účet služby Azure SQL Database s virtuální sítí](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Zjistěte, jak [zabezpečit službu Azure SQL Data Warehouse k virtuální síti](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- Přečtěte si o [integraci služeb Azure ve virtuálních sítích](virtual-network-for-azure-services.md).
- Další informace o [zásadami koncových bodů služeb virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)
-  Rychlý Start: [Šablona Azure Resource Manageru](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration) pro nastavení koncového bodu služby v podsíti virtuální sítě a zabezpečení účtu Azure Storage v této podsíti.

