---
title: Architektura připojení
titleSuffix: Azure SQL Managed Instance
description: Přečtěte si o komunikaci a architektuře připojení spravované instance Azure SQL a o tom, jak komponenty směrují provoz do spravované instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 10/22/2020
ms.openlocfilehash: c91b0231271c6cbcf9ec92c7ad6d25f1bc0f9136
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105960465"
---
# <a name="connectivity-architecture-for-azure-sql-managed-instance"></a>Architektura připojení pro službu Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Tento článek vysvětluje komunikaci ve spravované instanci Azure SQL. Také popisuje architekturu připojení a způsob, jakým součásti směrují provoz do spravované instance.  

Spravovaná instance SQL je umístěná ve virtuální síti Azure a v podsíti, která je vyhrazená pro spravované instance. Toto nasazení poskytuje:

- Zabezpečená privátní IP adresa.
- Možnost připojit místní síť k spravované instanci SQL.
- Možnost připojit spravovanou instanci SQL k odkazovanému serveru nebo jinému místnímu úložišti dat.
- Možnost připojit ke zdrojům Azure spravovanou instanci SQL.

## <a name="communication-overview"></a>Přehled komunikace

Následující diagram znázorňuje entity, které se připojují ke spravované instanci SQL. Zobrazuje také prostředky, které potřebují komunikovat se spravovanými instancemi. Proces komunikace v dolní části diagramu představuje aplikace a nástroje pro zákazníky, které se připojují ke spravované instanci SQL jako zdroje dat.  

![Entity v architektuře připojení](./media/connectivity-architecture-overview/connectivityarch001.png)

Spravovaná instance SQL je nabídka typu platforma jako služba (PaaS). Azure používá ke správě této služby automatizované agenty (správu, nasazení a údržbu) na základě datových proudů telemetrie. Vzhledem k tomu, že Azure zodpovídá za správu, zákazníci nemají přístup k počítačům s virtuálním clusterem spravované instance SQL prostřednictvím protokol RDP (Remote Desktop Protocol) (RDP).

Některé operace spouštěné koncovými uživateli nebo aplikacemi můžou vyžadovat interakci spravované instance SQL s platformou. Jediným případem je vytvoření databáze spravované instance SQL. Tento prostředek se zveřejňuje prostřednictvím Azure Portal, PowerShellu, rozhraní příkazového řádku Azure a REST API.

Spravovaná instance SQL závisí na službách Azure, jako jsou Azure Storage pro zálohování, Azure Event Hubs pro telemetrii, Azure Active Directory (Azure AD) pro ověřování, Azure Key Vault pro transparentní šifrování dat (TDE) a na několika službách platformy Azure, které poskytují funkce zabezpečení a podpory. Služba SQL Managed instance umožňuje připojení k těmto službám.

Veškerá komunikace je šifrovaná a podepsaná pomocí certifikátů. Aby bylo možné kontrolovat důvěryhodnost komunikujících stran, spravovaná instance SQL tyto certifikáty neustále ověřuje prostřednictvím seznamů odvolaných certifikátů. Pokud dojde k odvolání certifikátů, spravovaná instance SQL ukončí připojení k ochraně dat.

## <a name="high-level-connectivity-architecture"></a>Architektura připojení vysoké úrovně

Na vysoké úrovni je spravovaná instance SQL sada součástí služby. Tyto komponenty se hostují na vyhrazené sadě izolovaných virtuálních počítačů, které běží v podsíti virtuální sítě zákazníka. Tyto počítače tvoří virtuální cluster.

Virtuální cluster může hostovat víc spravovaných instancí. V případě potřeby se cluster automaticky rozšíří nebo vymění smlouvy, když zákazník změní počet zřízených instancí v podsíti.

Zákaznické aplikace se můžou připojit ke spravované instanci SQL a můžou zadávat dotazy a aktualizovat databáze uvnitř virtuální sítě, partnerské virtuální sítě nebo sítě připojené přes VPN nebo Azure ExpressRoute. Tato síť musí používat koncový bod a privátní IP adresu.  

![Diagram architektury připojení](./media/connectivity-architecture-overview/connectivityarch002.png)

Služby správy a nasazení Azure se spouštějí mimo virtuální síť. Spravovaná instance SQL a služby Azure se připojují přes koncové body, které mají veřejné IP adresy. Když spravovaná instance SQL vytvoří odchozí připojení, pak na přijímajícím konci překladu síťových adres (NAT) bude připojení vypadat jako z této veřejné IP adresy.

Provoz správy prochází přes virtuální síť zákazníka. To znamená, že prvky infrastruktury virtuální sítě mohou poškodit provoz správy tím, že instance selže a nebude k dispozici.

> [!IMPORTANT]
> Pro zlepšení zkušeností zákazníků a dostupnosti služeb používá Azure zásady záměru sítě na prvcích infrastruktury virtuální sítě Azure. Tato zásada může ovlivnit fungování spravované instance SQL. Tento mechanismus platforem transparentně komunikuje s požadavky na síť uživatelům. Hlavním cílem této zásady je zabránit nesprávnému nastavení sítě a zajistit normální operace SQL spravované instance. Při odstranění spravované instance se odstraní také zásada záměru sítě.

## <a name="virtual-cluster-connectivity-architecture"></a>Architektura připojení virtuálních clusterů

Pojďme si pořizovat hlubší podrobně architektury připojení pro spravovanou instanci SQL. Následující diagram znázorňuje koncepční rozložení virtuálního clusteru.

![Architektura připojení virtuálního clusteru](./media/connectivity-architecture-overview/connectivityarch003.png)

Klienti se připojují ke spravované instanci SQL pomocí názvu hostitele, který má formu `<mi_name>.<dns_zone>.database.windows.net` . Tento název hostitele se překládá na soukromou IP adresu, i když je zaregistrovaný ve veřejné zóně DNS (Domain Name System) a je veřejně přeložitelný. `zone-id`Při vytváření clusteru se automaticky vygeneruje. Pokud je nově vytvořený cluster hostitelem sekundární spravované instance, sdílí své ID zóny s primárním clusterem. Další informace najdete v tématu [použití skupin automatického převzetí služeb při selhání k zajištění transparentního a koordinovaného převzetí služeb při selhání více databází](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets).

Tato privátní IP adresa patří do interního nástroje pro vyrovnávání zatížení pro spravovanou instanci SQL. Nástroj pro vyrovnávání zatížení směruje provoz do brány spravované instance SQL. Vzhledem k tomu, že je možné spustit více spravovaných instancí v rámci stejného clusteru, brána používá název hostitele spravované instance SQL pro přesměrování provozu do správné služby SQL Engine.

Služba správy a nasazení se připojí k spravované instanci SQL pomocí [koncového bodu správy](#management-endpoint) , který se mapuje na externí nástroj pro vyrovnávání zatížení. Provoz se směruje na uzly pouze v případě, že se obdrží na předdefinované sadě portů, které používají pouze součásti správy spravované instance SQL. Integrovaná brána firewall na uzlech je nastavená tak, aby povolovala přenosy jenom z rozsahů IP adres Microsoftu. Certifikáty vzájemně ověřují veškerou komunikaci mezi součástmi pro správu a rovinou správy.

## <a name="management-endpoint"></a>Koncový bod správy

Azure spravuje spravované instance SQL pomocí koncového bodu správy. Tento koncový bod je uvnitř virtuálního clusteru instance. Koncový bod správy je chráněn integrovanou bránou firewall na úrovni sítě. Na úrovni aplikace je ochrana pomocí vzájemného ověření certifikátu. IP adresu koncového bodu najdete v tématu [určení IP adresy koncového bodu správy](management-endpoint-find-ip-address.md).

Když se připojení spouštějí v rámci spravované instance SQL (stejně jako u protokolů zálohování a auditu), zobrazí se provoz z veřejné IP adresy koncového bodu správy. Přístup k veřejným službám z spravované instance SQL můžete omezit nastavením pravidel brány firewall tak, aby povolovala pouze IP adresu spravované instance SQL. Další informace najdete v tématu [ověření integrované brány firewall spravované instance SQL](management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Provoz, který směřuje do služeb Azure, které jsou uvnitř oblasti spravované instance SQL, je optimalizován a z tohoto důvodu se NATed na veřejnou IP adresu pro koncový bod správy. Z tohoto důvodu Pokud potřebujete použít pravidla brány firewall založená na protokolu IP, nejčastěji pro úložiště, musí být služba v jiné oblasti než spravovaná instance SQL.

## <a name="service-aided-subnet-configuration"></a>Konfigurace podsítě s podporou služeb

Za účelem vyřešení požadavků na zabezpečení a možnosti správy je spravovaná instance SQL přechodná z ruční na konfiguraci podsítě s podporou služby.

Při konfiguraci podsítě s podporou služby je uživatel plně řízen provozem dat (TDS), zatímco spravované instance SQL vezme zodpovědnost za zajištění nepřerušovaného toku provozu správy za účelem splnění smlouvy SLA.

Konfigurace podsítě s podporou služby je založena na funkci [delegování podsítě](../../virtual-network/subnet-delegation-overview.md) virtuální sítě, která poskytuje automatickou správu konfigurace sítě a povoluje koncové body služby. 

Koncové body služby se daly použít ke konfiguraci pravidel brány firewall virtuální sítě pro účty úložiště, které udržují protokoly zálohování a auditu. I s povolenými koncovými body služby doporučujeme zákazníkům používat [privátní odkaz](../../private-link/private-link-overview.md) , který poskytuje další zabezpečení nad koncovými body služby.

> [!IMPORTANT]
> Z důvodu specifičnosti konfigurace roviny řízení by konfigurace podsítě pro službu nepovolovala koncové body služby v národních cloudech. 

### <a name="network-requirements"></a>Požadavky sítě

Nasaďte spravovanou instanci SQL do vyhrazené podsítě uvnitř virtuální sítě. Podsíť musí mít tyto charakteristiky:

- **Vyhrazená podsíť:** Podsíť spravované instance SQL nemůže obsahovat žádnou jinou cloudovou službu, která je k ní přidružená, a nemůže to být podsíť brány. Podsíť nemůže obsahovat žádný prostředek, ale spravovanou instanci SQL a nemůžete později přidat další typy prostředků v podsíti.
- **Delegování podsítě:** Podsíť spravované instance SQL musí být delegovaná na `Microsoft.Sql/managedInstances` poskytovatele prostředků.
- **Skupina zabezpečení sítě (NSG):** NSG musí být přidružený k podsíti spravované instance SQL. Pomocí NSG můžete řídit přístup ke koncovému bodu dat spravované instance SQL pomocí filtrování provozu na portech 1433 a porty 11000-11999 při konfiguraci spravované instance SQL pro připojení přesměrování. Služba automaticky zřídí a zachová aktuální [pravidla](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) nutná k tomu, aby bylo možné nepřerušovaný tok provozu správy.
- **Tabulka uživatelsky definované trasy (udr):** Tabulka UDR musí být přidružena k podsíti spravované instance SQL. Prostřednictvím brány virtuální sítě nebo síťového virtuálního zařízení můžete do směrovací tabulky přidat záznamy pro směrování provozu, který má jako cíl místní rozsahy privátních IP adres. Služba bude automaticky zřizovat a udržovat aktuální [záznamy](#user-defined-routes-with-service-aided-subnet-configuration) požadované k umožnění nepřerušovaného toku provozu správy.
- **Dostatečná IP adresa:** Podsíť spravované instance SQL musí mít minimálně 32 IP adres. Další informace najdete v tématu [Určení velikosti podsítě pro spravovanou instanci SQL](vnet-subnet-determine-size.md). Spravované instance můžete nasadit v [existující síti](vnet-existing-add-subnet.md) poté, co ji nakonfigurujete tak, aby splňovala [požadavky na síť pro spravovanou instanci SQL](#network-requirements). Jinak vytvořte [novou síť a podsíť](virtual-network-subnet-create-arm-template.md).

> [!IMPORTANT]
> Při vytváření spravované instance se v podsíti použije zásada záměru sítě, aby se zabránilo nekompatibilním změnám nastavení sítě. Po odebrání poslední instance z podsítě se odstraní také zásada záměru sítě. Níže uvedená pravidla se týkají pouze informativních účelů a neměli byste je nasazovat pomocí šablon ARM/PowerShell/CLI. Pokud chcete použít nejnovější oficiální šablonu, můžete [ji kdykoli načíst z portálu](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>Povinná příchozí pravidla zabezpečení s konfigurací podsítě s podporou služby

| Název       |Port                        |Protokol|Zdroj           |Cíl|Akce|
|------------|----------------------------|--------|-----------------|-----------|------|
|správa  |9000, 9003, 1438, 1440, 1452|TCP     |SqlManagement    |PODSÍŤ MI  |Povolit |
|            |9000, 9003                  |TCP     |CorpnetSaw       |PODSÍŤ MI  |Povolit |
|            |9000, 9003                  |TCP     |CorpnetPublic    |PODSÍŤ MI  |Povolit |
|mi_subnet   |Všechny                         |Všechny     |PODSÍŤ MI        |PODSÍŤ MI  |Povolit |
|health_probe|Všechny                         |Všechny     |AzureLoadBalancer|PODSÍŤ MI  |Povolit |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>Povinná odchozí pravidla zabezpečení s konfigurací podsítě s podporou služby

| Název       |Port          |Protokol|Zdroj           |Cíl|Akce|
|------------|--------------|--------|-----------------|-----------|------|
|správa  |443, 12000    |TCP     |PODSÍŤ MI        |AzureCloud |Povolit |
|mi_subnet   |Všechny           |Všechny     |PODSÍŤ MI        |PODSÍŤ MI  |Povolit |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>Uživatelem definované trasy s konfigurací podsítě s podporou služby

|Name|Předpona adresy|Další směrování|
|----|--------------|-------|
|podsíť do vnetlocal|PODSÍŤ MI|Virtuální síť|
|mi – azurecloud-REGION-Internet|AzureCloud. REGION|Internet|
|mi – azurecloud-REGION_PAIR – Internet|AzureCloud.REGION_PAIR|Internet|
|mi – azuremonitor – Internet|AzureMonitor|Internet|
|mi – corpnetpublic – Internet|CorpNetPublic|Internet|
|mi – corpnetsaw – Internet|CorpNetSaw|Internet|
|mi – eventhub – eventhub – oblast – Internet|EventHub. REGION|Internet|
|mi – eventhub – REGION_PAIR – Internet|EventHub.REGION_PAIR|Internet|
|mi – sqlmanagement – Internet|SqlManagement|Internet|
|mi – Storage – Internet|Storage|Internet|
|mi – Storage-REGION-Internet|Storage. REGION|Internet|
|mi – Storage-REGION_PAIR-Internet|Storage.REGION_PAIR|Internet|
||||

\* PODSÍŤ MI odkazuje na rozsah IP adres podsítě ve formátu x. x. x. x/y. Tyto informace můžete najít v Azure Portal ve vlastnostech podsítě.

\** Pokud je cílová adresa pro jednu ze služeb Azure, Azure směruje provoz přímo do služby přes páteřní síť Azure místo směrování provozu na Internet. Provoz mezi službami Azure neprochází přes internet, a to bez ohledu na to, ve které oblasti Azure existuje virtuální síť nebo ve které oblasti Azure je nasazená instance služby Azure. Další podrobnosti najdete na [stránce dokumentace k udr](../../virtual-network/virtual-networks-udr-overview.md).

Kromě toho můžete přidat položky do směrovací tabulky pro směrování provozu, který má místní rozsahy privátních IP adres jako cíl prostřednictvím brány virtuální sítě nebo zařízení virtuální sítě (síťové virtuální zařízení).

Pokud virtuální síť obsahuje vlastní DNS, vlastní server DNS musí být schopný přeložit veřejné záznamy DNS. Použití dalších funkcí, jako je ověřování Azure AD, může vyžadovat vyřešení dalších plně kvalifikovaných názvů domén. Další informace najdete v tématu [nastavení vlastního DNS](custom-dns-configure.md).

### <a name="networking-constraints"></a>Omezení sítě

Pro **odchozí připojení se vynutilo tls 1,2**: v lednu 2020 Microsoft vynutila 1,2 TLS pro provoz uvnitř služby ve všech službách Azure. U spravované instance Azure SQL to vedlo k vymáhání TLS 1,2 u odchozích připojení používaných pro replikaci a připojení k serveru SQL Server. Pokud používáte verze SQL Server starší než 2016 se službou SQL Managed instance, zajistěte, aby byly použity [specifické aktualizace TLS 1,2](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) .

U spravované instance SQL se aktuálně *nepodporují* následující funkce virtuální sítě:

- **Partnerský vztah Microsoftu**: povolení [partnerského vztahu Microsoftu](../../expressroute/expressroute-faqs.md#microsoft-peering) na okruhech ExpressRoute partnerských vztahů přímo nebo v transitu s virtuální sítí, kde se nachází spravovaná instance SQL, ovlivňuje tok přenosů mezi komponentami spravované instance SQL uvnitř virtuální sítě a služeb, na kterých závisí, a způsobující problémy s dostupností. Očekává se, že nasazení spravované instance SQL do virtuální sítě s partnerským vztahem Microsoftu je už povolené.
- **Globální partnerské vztahy virtuálních sítí**: připojení [partnerských vztahů virtuálních sítí](../../virtual-network/virtual-network-peering-overview.md) napříč oblastmi Azure nefunguje pro spravované instance SQL umístěné v podsítích vytvořených před 9/22/2020.
- **AzurePlatformDNS**: použití [značky služby](../../virtual-network/service-tags-overview.md) AZUREPLATFORMDNS k blokování překladu DNS platformy by vygenerovalo nedostupné spravované instance SQL. I když spravovaná instance SQL podporuje DNS definované uživatelem pro překlad DNS v rámci motoru, je závislá na platformě DNS platformy pro operace platforem.
- **Brána NAT**: použití služby [Azure Virtual Network NAT](../../virtual-network/nat-overview.md) k řízení odchozího připojení s konkrétní veřejnou IP adresou by nedostupné pro vykreslování spravované instance SQL. Služba SQL Managed instance je momentálně omezená na použití základního nástroje pro vyrovnávání zatížení, který neposkytuje koexistenci příchozích a odchozích toků s Virtual Network překladem adres (NAT).
- **Protokol IPv6 pro Azure Virtual Network**: očekává se, že nasazení spravované instance SQL do [virtuálních sítí IPv4/IPv6 ve duálním zásobníku](../../virtual-network/ipv6-overview.md) se nezdaří. Přidružení skupiny zabezpečení sítě (NSG) nebo směrovací tabulky (UDR) obsahující předpony adresy IPv6 k podsíti spravované instance SQL nebo přidání předpon IPv6 adres do NSG nebo UDR, která je již přidružena k podsíti spravované instance, by způsobila nedostupnost spravované instance SQL. U nasazení spravované instance SQL do podsítě s NSG a UDR, které už mají předpony IPv6, se očekává selhání.

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [co je Azure SQL Managed instance?](sql-managed-instance-paas-overview.md).
- Naučte se, jak [nastavit novou virtuální síť Azure](virtual-network-subnet-create-arm-template.md) nebo [existující virtuální síť Azure](vnet-existing-add-subnet.md) , kde můžete nasadit spravovanou instanci SQL.
- [Vypočítá velikost podsítě](vnet-subnet-determine-size.md) , do které chcete nasadit SPRAVOVANOU instanci SQL.
- Naučte se vytvořit spravovanou instanci:
  - Z [Azure Portal](instance-create-quickstart.md).
  - Pomocí [prostředí PowerShell](scripts/create-configure-managed-instance-powershell.md).
  - Pomocí [šablony Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Pomocí [šablony Azure Resource Manager (s použitím JumpBox, včetně SSMS)](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/).
