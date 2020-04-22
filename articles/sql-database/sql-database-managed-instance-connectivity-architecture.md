---
title: Architektura připojení pro spravovanou instanci
description: Přečtěte si o komunikaci spravovaných instancí Azure SQL Database a o architektuře připojení a o tom, jak komponenty směrují provoz na spravovanou instanci.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: e4d6098b7b4de76461e924fc7d42d039046d7ce5
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677175"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Architektura připojení pro spravovanou instanci v Azure SQL Database

Tento článek vysvětluje komunikaci ve spravované instanci Azure SQL Database. Popisuje také architekturu připojení a způsob, jakým komponenty směrují provoz na spravovanou instanci.  

Instance spravované sql database se umístí uvnitř virtuální sítě Azure a podsítě, která je vyhrazena pro spravované instance. Toto nasazení poskytuje:

- Zabezpečená privátní IP adresa.
- Možnost připojení místní sítě ke spravované instanci.
- Možnost připojení spravované instance k propojenému serveru nebo jinému místnímu úložišti dat.
- Možnost připojení spravované instance k prostředkům Azure.

## <a name="communication-overview"></a>Přehled komunikace

Následující diagram znázorňuje entity, které se připojují ke spravované instanci. Zobrazuje také prostředky, které je třeba komunikovat se spravovanou instancí. Proces komunikace v dolní části diagramu představuje zákaznické aplikace a nástroje, které se připojují ke spravované instanci jako zdroje dat.  

![Entity v architektuře připojení](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

Spravovaná instance je platforma jako služba (PaaS) nabízí. Společnost Microsoft používá automatizované agenty (správu, nasazení a údržbu) ke správě této služby na základě telemetrických datových proudů. Vzhledem k tomu, že společnost Microsoft je zodpovědná za správu, nemají zákazníci přístup k virtuálním počítačům spravované instance prostřednictvím protokolu RDP (Remote Desktop Protocol).

Některé operace SQL Serveru spuštěné koncovými uživateli nebo aplikacemi mohou vyžadovat spravované instance pro interakci s platformou. Jedním případem je vytvoření databáze spravovaných instancí. Tento prostředek je vystavenprostřednictvím portálu Azure, PowerShell, Azure CLI a rozhraní REST API.

Spravované instance závisí na službách Azure, jako je Azure Storage pro zálohování, Azure Event Hubs for telemetrie, Azure Active Directory pro ověřování, Azure Key Vault for Transparent Data Encryption (TDE) a několik služeb platformy Azure, které poskytují funkce zabezpečení a podpory. Spravované instance vytvoří připojení k těmto službám.

Veškerá komunikace je šifrována a podepsána pomocí certifikátů. Chcete-li zkontrolovat důvěryhodnost komunikujících stran, spravované instance neustále ověřují tyto certifikáty prostřednictvím seznamů odvolaných certifikátů. Pokud jsou certifikáty odvolány, spravovaná instance zavře připojení k ochraně dat.

## <a name="high-level-connectivity-architecture"></a>Architektura připojení na vysoké úrovni

Na vysoké úrovni spravované instance je sada součástí služby. Tyto součásti jsou hostované na vyhrazené sadě izolovaných virtuálních počítačů, které běží uvnitř podsítě virtuální sítě zákazníka. Tyto počítače tvoří virtuální cluster.

Virtuální cluster může hostovat více spravovaných instancí. V případě potřeby se cluster automaticky rozbalí nebo uvalí smlouvy, když zákazník změní počet zřízených instancí v podsíti.

Zákaznické aplikace se můžou připojovat ke spravovaným instancím a můžou se dotazovat a aktualizovat databáze uvnitř virtuální sítě, partnerské virtuální sítě nebo sítě připojené pomocí sítě VPN nebo Azure ExpressRoute. Tato síť musí používat koncový bod a privátní IP adresu.  

![Diagram architektury připojení](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Služby správy a nasazení microsoftu běží mimo virtuální síť. Spravovaná instance a služby Microsoftu se připojují přes koncové body, které mají veřejné IP adresy. Když spravovaná instance vytvoří odchozí připojení, při příjmu překladu síťových adres (NAT) způsobí, že připojení pochází z této veřejné IP adresy.

Provoz správy toky prostřednictvím virtuální sítě zákazníka. To znamená, že prvky infrastruktury virtuální sítě může poškodit provoz správy tím, že instance nezdaří a stanou se nedostupnými.

> [!IMPORTANT]
> Aby se zlepšila zákaznická zkušenost a dostupnost služeb, microsoft použije zásady záměru sítě na prvky infrastruktury virtuální sítě Azure. Zásada může ovlivnit fungování spravované instance. Tento mechanismus platformy transparentně sděluje požadavky na síť uživatelům. Hlavním cílem zásady je zabránit chybné konfiguraci sítě a zajistit normální operace spravované instance. Když odstraníte spravovanou instanci, zásady záměru sítě se také odeberou.

## <a name="virtual-cluster-connectivity-architecture"></a>Architektura připojení virtuálního clusteru

Podívejme se hlouběji do architektury připojení pro spravované instance. Následující diagram znázorňuje koncepční rozložení virtuálního clusteru.

![Architektura připojení virtuálního clusteru](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Klienti se připojují ke spravované instanci pomocí názvu hostitele, který má formulář `<mi_name>.<dns_zone>.database.windows.net`. Tento název hostitele se překládá na privátní IP adresu, i když je zaregistrován ve veřejné zóně DNS (Domain Name System) a je veřejně řešitelný. Automaticky `zone-id` se vygeneruje při vytváření clusteru. Pokud nově vytvořený cluster hostuje sekundární spravovanou instanci, sdílí id zóny s primárním clusterem. Další informace naleznete [v tématu Použití skupin automatického převzetí služeb při selhání k povolení transparentního a koordinovaného převzetí služeb při selhání více databází](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets).

Tato privátní IP adresa patří do interního systému vyrovnávání zatížení spravované instance. Nástroj pro vyrovnávání zatížení směruje provoz do brány spravované instance. Vzhledem k tomu, že více spravovaných instancí může běžet uvnitř stejného clusteru, brána používá název hostitele spravované instance k přesměrování provozu na správnou službu modulu SQL.

Služby správy a nasazení se připojují ke spravované instanci pomocí [koncového bodu správy,](#management-endpoint) který se mapuje na externí systém vyrovnávání zatížení. Provoz je směrován do uzlů pouze v případě, že je přijat na předdefinovanou sadu portů, které používají pouze součásti správy spravované instance. Vestavěná brána firewall na uzlech je nastavena tak, aby umožňovala přenosy pouze z rozsahů IP adres společnosti Microsoft. Certifikáty vzájemně ověřují veškerou komunikaci mezi součástmi správy a rovinou správy.

## <a name="management-endpoint"></a>Koncový bod správy

Společnost Microsoft spravuje spravovanou instanci pomocí koncového bodu správy. Tento koncový bod je uvnitř virtuálního clusteru instance. Koncový bod správy je chráněn vestavěnou bránou firewall na úrovni sítě. Na úrovni aplikace je chráněn a vzájemné ověření certifikátu. Informace o adrese IP koncového bodu naleznete v [tématu Určení ip adresy koncového bodu pro správu](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Když připojení spustit uvnitř spravované instance (stejně jako u záloh a protokoly auditu), provoz se zobrazí spustit z koncového bodu správy veřejné IP adresy. Přístup k veřejným službám ze spravované instance můžete omezit nastavením pravidel brány firewall tak, aby umožňovala pouze adresu IP spravované instance. Další informace naleznete [v tématu Ověření integrované brány firewall spravované instance](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Provoz, který přejde do služeb Azure, které jsou uvnitř oblasti spravované instance je optimalizovaný a z tohoto důvodu není NATed spravované instance správy koncový bod veřejné IP adresy. Z tohoto důvodu, pokud potřebujete použít pravidla brány firewall založené na protokolu IP, nejčastěji pro úložiště, služba musí být v jiné oblasti než spravované instance.

## <a name="service-aided-subnet-configuration"></a>Konfigurace podsítě s podporou služeb

Chcete-li řešit požadavky na zabezpečení a správu zákazníků, spravovaná instance přechází z ruční konfigurace podsítě s podporou služeb.

S podporou služby konfigurace podsítě uživatel je plně pod kontrolou dat (TDS) provoz, zatímco spravovaná instance přebírá odpovědnost za zajištění nepřetržitého toku provozu správy za účelem splnění smlouvy SLA.

Konfigurace podsítě podporovaná službou se staví nad funkcí [delegování podsítě](../virtual-network/subnet-delegation-overview.md) virtuální sítě a poskytuje automatickou správu konfigurace sítě a povoluje koncové body služby. Koncové body služby lze použít ke konfiguraci pravidel brány firewall virtuální sítě pro účty úložiště, které uchovávají protokoly záloh / auditu.

### <a name="network-requirements"></a>Síťové požadavky 

Nasazení spravované instance ve vyhrazené podsíti uvnitř virtuální sítě. Podsíť musí mít tyto vlastnosti:

- **Vyhrazená podsíť:** Podsíť spravované instance nemůže obsahovat žádnou jinou cloudovou službu, která je k ní přidružená, a nemůže se jedná o podsíť brány. Podsíť nemůže obsahovat žádný prostředek kromě spravované instance a později nelze přidat další typy prostředků v podsíti.
- **Delegování podsítě:** Podsíť spravované instance musí být delegována na `Microsoft.Sql/managedInstances` poskytovatele prostředků.
- **Skupina zabezpečení sítě (NSG):** Skupina nsg musí být přidružena k podsíti spravované instance. Skupina zabezpečení sítě můžete použít k řízení přístupu ke koncovému bodu dat spravované instance filtrováním přenosů na portu 1433 a portech 11000-11999, když je spravovaná instance nakonfigurována pro přesměrování připojení. Služba bude automaticky zjištovat a udržovat aktuální [pravidla](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) potřebná k umožnění nepřetržitého toku provozu pro správu.
- **Uživatelem definovaná tabulka trasy (UDR):** Tabulka UDR musí být přidružena k podsíti spravované instance. Do směrovací tabulky můžete přidat položky pro směrování provozu, který má místní privátní rozsahy IP adres jako cíl prostřednictvím brány virtuální sítě nebo zařízení virtuální sítě (NVA). Služba bude automaticky zřcovávat a udržovat aktuální [položky](#user-defined-routes-with-service-aided-subnet-configuration) potřebné k umožnění nepřetržitého toku provozu pro správu.
- **Dostatečné IP adresy:** Podsíť spravované instance musí mít alespoň 16 adres IP. Doporučené minimum je 32 IP adres. Další informace naleznete [v tématu Určení velikosti podsítě pro spravované instance](sql-database-managed-instance-determine-size-vnet-subnet.md). Spravované instance můžete nasadit v [existující síti](sql-database-managed-instance-configure-vnet-subnet.md) po jejich konfiguraci tak, aby [splňovaly požadavky na síť pro spravované instance](#network-requirements). V opačném případě vytvořte [novou síť a podsíť](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Při vytváření spravované instance se v podsíti použije zásada záměru sítě, aby se zabránilo nekompatibilním změnám v nastavení sítě. Po odebrání poslední instance z podsítě je odebrána také zásada záměru sítě.

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>Povinná pravidla zabezpečení příchozích služeb s konfigurací podsítě podporovanou službou 

| Název       |Port                        |Protocol (Protokol)|Zdroj           |Cíl|Akce|
|------------|----------------------------|--------|-----------------|-----------|------|
|správa  |9000, 9003, 1438, 1440, 1452|TCP     |Řízení sqlmanagementu    |PODSÍŤ MI  |Povolit |
|            |9000, 9003                  |TCP     |CorpnetSaw       |PODSÍŤ MI  |Povolit |
|            |9000, 9003                  |TCP     |CorpnetPublic    |PODSÍŤ MI  |Povolit |
|mi_subnet   |Všechny                         |Všechny     |PODSÍŤ MI        |PODSÍŤ MI  |Povolit |
|health_probe|Všechny                         |Všechny     |AzureLoadBalancer|PODSÍŤ MI  |Povolit |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>Povinná pravidla odchozího zabezpečení s konfigurací podsítě podporovanou službou 

| Název       |Port          |Protocol (Protokol)|Zdroj           |Cíl|Akce|
|------------|--------------|--------|-----------------|-----------|------|
|správa  |443, 12000    |TCP     |PODSÍŤ MI        |AzureCloud |Povolit |
|mi_subnet   |Všechny           |Všechny     |PODSÍŤ MI        |PODSÍŤ MI  |Povolit |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>Uživatelem definované trasy s konfigurací podsítě podporovanou službou 

|Název|Předpona adresy|Další směrování|
|----|--------------|-------|
|podsíť-v-vnetlocal|PODSÍŤ MI|Virtuální síť|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|mi-20-33-16-nexthop-internet|20.33.0.0/16|Internet|
|mi-20-34-15-nexthop-internet|20.34.0.0/15|Internet|
|mi-20-36-14-nexthop-internet|20.36.0.0/14|Internet|
|mi-20-40-13-nexthop-internet|20.40.0.0/13|Internet|
|mi-20-48-12-nexthop-internet|20.48.0.0/12|Internet|
|mi-20-64-10-nexthop-internet|20.64.0.0/10|Internet|
|mi-20-128-16-nexthop-internet|20.128.0.0/16|Internet|
|mi-20-135-16-nexthop-internet|20.135.0.0/16|Internet|
|mi-20-136-16-nexthop-internet|20.136.0.0/16|Internet|
|mi-20-140-15-nexthop-internet|20.140.0.0/15|Internet|
|mi-20-143-16-nexthop-internet|20.143.0.0/16|Internet|
|mi-20-144-14-nexthop-internet|20.144.0.0/14|Internet|
|mi-20-150-15-nexthop-internet|20.150.0.0/15|Internet|
|mi-20-160-12-nexthop-internet|20.160.0.0/12|Internet|
|mi-20-176-14-nexthop-internet|20.176.0.0/14|Internet|
|mi-20-180-14-nexthop-internet|20.180.0.0/14|Internet|
|mi-20-184-13-nexthop-internet|20.184.0.0/13|Internet|
|mi-20-192-10-nexthop-internet|20.192.0.0/10|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|mi-51-4-15-nexthop-internet|51.4.0.0/15|Internet|
|mi-51-8-16-nexthop-internet|51.8.0.0/16|Internet|
|mi-51-10-15-nexthop-internet|51.10.0.0/15|Internet|
|mi-51-18-16-nexthop-internet|51.18.0.0/16|Internet|
|mi-51-51-16-nexthop-internet|51.51.0.0/16|Internet|
|mi-51-53-16-nexthop-internet|51.53.0.0/16|Internet|
|mi-51-103-16-nexthop-internet|51.103.0.0/16|Internet|
|mi-51-104-15-nexthop-internet|51.104.0.0/15|Internet|
|mi-51-132-16-nexthop-internet|51.132.0.0/16|Internet|
|mi-51-136-15-nexthop-internet|51.136.0.0/15|Internet|
|mi-51-138-16-nexthop-internet|51.138.0.0/16|Internet|
|mi-51-140-14-nexthop-internet|51.140.0.0/14|Internet|
|mi-51-144-15-nexthop-internet|51.144.0.0/15|Internet|
|mi-52-96-12-nexthop-internet|52.96.0.0/12|Internet|
|mi-52-112-14-nexthop-internet|52.112.0.0/14|Internet|
|mi-52-125-16-nexthop-internet|52.125.0.0/16|Internet|
|mi-52-126-15-nexthop-internet|52.126.0.0/15|Internet|
|mi-52-130-15-nexthop-internet|52.130.0.0/15|Internet|
|mi-52-132-14-nexthop-internet|52.132.0.0/14|Internet|
|mi-52-136-13-nexthop-internet|52.136.0.0/13|Internet|
|mi-52-145-16-nexthop-internet|52.145.0.0/16|Internet|
|mi-52-146-15-nexthop-internet|52.146.0.0/15|Internet|
|mi-52-148-14-nexthop-internet|52.148.0.0/14|Internet|
|mi-52-152-13-nexthop-internet|52.152.0.0/13|Internet|
|mi-52-160-11-nexthop-internet|52.160.0.0/11|Internet|
|mi-52-224-11-nexthop-internet|52.224.0.0/11|Internet|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|Internet|
|mi-103-25-156-24-nexthop-internet|103.25.156.0/24|Internet|
|mi-103-25-157-24-nexthop-internet|103.25.157.0/24|Internet|
|mi-103-25-158-23-nexthop-internet|103.25.158.0/23|Internet|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|Internet|
|mi-131-107-16-nexthop-internet|131.107.0.0/16|Internet|
|mi-131-253-1-24-nexthop-internet|131.253.1.0/24|Internet|
|mi-131-253-3-24-nexthop-internet|131.253.3.0/24|Internet|
|mi-131-253-5-24-nexthop-internet|131.253.5.0/24|Internet|
|mi-131-253-6-24-nexthop-internet|131.253.6.0/24|Internet|
|mi-131-253-8-24-nexthop-internet|131.253.8.0/24|Internet|
|mi-131-253-12-22-nexthop-internet|131.253.12.0/22|Internet|
|mi-131-253-16-23-nexthop-internet|131.253.16.0/23|Internet|
|mi-131-253-18-24-nexthop-internet|131.253.18.0/24|Internet|
|mi-131-253-21-24-nexthop-internet|131.253.21.0/24|Internet|
|mi-131-253-22-23-nexthop-internet|131.253.22.0/23|Internet|
|mi-131-253-24-21-nexthop-internet|131.253.24.0/21|Internet|
|mi-131-253-32-20-nexthop-internet|131.253.32.0/20|Internet|
|mi-131-253-61-24-nexthop-internet|131.253.61.0/24|Internet|
|mi-131-253-62-23-nexthop-internet|131.253.62.0/23|Internet|
|mi-131-253-64-18-nexthop-internet|131.253.64.0/18|Internet|
|mi-131-253-128-17-nexthop-internet|131.253.128.0/17|Internet|
|mi-132-245-16-nexthop-internet|132.245.0.0/16|Internet|
|mi-134-170-16-nexthop-internet|134.170.0.0/16|Internet|
|mi-134-177-16-nexthop-internet|134.177.0.0/16|Internet|
|mi-137-116-15-nexthop-internet|137.116.0.0/15|Internet|
|mi-137-135-16-nexthop-internet|137.135.0.0/16|Internet|
|mi-138-91-16-nexthop-internet|138.91.0.0/16|Internet|
|mi-138-196-16-nexthop-internet|138.196.0.0/16|Internet|
|mi-139-217-16-nexthop-internet|139.217.0.0/16|Internet|
|mi-139-219-16-nexthop-internet|139.219.0.0/16|Internet|
|mi-141-251-16-nexthop-internet|141.251.0.0/16|Internet|
|mi-146-147-16-nexthop-internet|146.147.0.0/16|Internet|
|mi-147-243-16-nexthop-internet|147.243.0.0/16|Internet|
|mi-150-171-16-nexthop-internet|150.171.0.0/16|Internet|
|mi-150-242-48-22-nexthop-internet|150.242.48.0/22|Internet|
|mi-157-54-15-nexthop-internet|157.54.0.0/15|Internet|
|mi-157-56-14-nexthop-internet|157.56.0.0/14|Internet|
|mi-157-60-16-nexthop-internet|157.60.0.0/16|Internet|
|mi-167-105-16-nexthop-internet|167.105.0.0/16|Internet|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
|mi-192-197-157-24-nexthop-internet|192.197.157.0/24|Internet|
|mi-193-149-64-19-nexthop-internet|193.149.64.0/19|Internet|
|mi-193-221-113-24-nexthop-internet|193.221.113.0/24|Internet|
|mi-194-69-96-19-nexthop-internet|194.69.96.0/19|Internet|
|mi-194-110-197-24-nexthop-internet|194.110.197.0/24|Internet|
|mi-198-105-232-22-nexthop-internet|198.105.232.0/22|Internet|
|mi-198-200-130-24-nexthop-internet|198.200.130.0/24|Internet|
|mi-198-206-164-24-nexthop-internet|198.206.164.0/24|Internet|
|mi-199-60-28-24-nexthop-internet|199.60.28.0/24|Internet|
|mi-199-74-210-24-nexthop-internet|199.74.210.0/24|Internet|
|mi-199-103-90-23-nexthop-internet|199.103.90.0/23|Internet|
|mi-199-103-122-24-nexthop-internet|199.103.122.0/24|Internet|
|mi-199-242-32-20-nexthop-internet|199.242.32.0/20|Internet|
|mi-199-242-48-21-nexthop-internet|199.242.48.0/21|Internet|
|mi-202-89-224-20-nexthop-internet|202.89.224.0/20|Internet|
|mi-204-13-120-21-nexthop-internet|204.13.120.0/21|Internet|
|mi-204-14-180-22-nexthop-internet|204.14.180.0/22|Internet|
|mi-204-79-135-24-nexthop-internet|204.79.135.0/24|Internet|
|mi-204-79-179-24-nexthop-internet|204.79.179.0/24|Internet|
|mi-204-79-181-24-nexthop-internet|204.79.181.0/24|Internet|
|mi-204-79-188-24-nexthop-internet|204.79.188.0/24|Internet|
|mi-204-79-195-24-nexthop-internet|204.79.195.0/24|Internet|
|mi-204-79-196-23-nexthop-internet|204.79.196.0/23|Internet|
|mi-204-79-252-24-nexthop-internet|204.79.252.0/24|Internet|
|mi-204-152-18-23-nexthop-internet|204.152.18.0/23|Internet|
|mi-204-152-140-23-nexthop-internet|204.152.140.0/23|Internet|
|mi-204-231-192-24-nexthop-internet|204.231.192.0/24|Internet|
|mi-204-231-194-23-nexthop-internet|204.231.194.0/23|Internet|
|mi-204-231-197-24-nexthop-internet|204.231.197.0/24|Internet|
|mi-204-231-198-23-nexthop-internet|204.231.198.0/23|Internet|
|mi-204-231-200-21-nexthop-internet|204.231.200.0/21|Internet|
|mi-204-231-208-20-nexthop-internet|204.231.208.0/20|Internet|
|mi-204-231-236-24-nexthop-internet|204.231.236.0/24|Internet|
|mi-205-174-224-20-nexthop-internet|205.174.224.0/20|Internet|
|mi-206-138-168-21-nexthop-internet|206.138.168.0/21|Internet|
|mi-206-191-224-19-nexthop-internet|206.191.224.0/19|Internet|
|mi-207-46-16-nexthop-internet|207.46.0.0/16|Internet|
|mi-207-68-128-18-nexthop-internet|207.68.128.0/18|Internet|
|mi-208-68-136-21-nexthop-internet|208.68.136.0/21|Internet|
|mi-208-76-44-22-nexthop-internet|208.76.44.0/22|Internet|
|mi-208-84-21-nexthop-internet|208.84.0.0/21|Internet|
|mi-209-240-192-19-nexthop-internet|209.240.192.0/19|Internet|
|mi-213-199-128-18-nexthop-internet|213.199.128.0/18|Internet|
|mi-216-32-180-22-nexthop-internet|216.32.180.0/22|Internet|
|mi-216-220-208-20-nexthop-internet|216.220.208.0/20|Internet|
|mi-23-96-13-nexthop-internet|23.96.0.0/13|Internet|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|Internet|
|mi-51-13-17-nexthop-internet|51.13.0.0/17|Internet|
|mi-51-107-16-nexthop-internet|51.107.0.0/16|Internet|
|mi-51-116-16-nexthop-internet|51.116.0.0/16|Internet|
|mi-51-120-16-nexthop-internet|51.120.0.0/16|Internet|
|mi-51-120-128-17-nexthop-internet|51.120.128.0/17|Internet|
|mi-51-124-16-nexthop-internet|51.124.0.0/16|Internet|
|mi-102-37-18-nexthop-internet|102.37.0.0/18|Internet|
|mi-102-133-16-nexthop-internet|102.133.0.0/16|Internet|
|mi-199-30-16-20-nexthop-internet|199.30.16.0/20|Internet|
|mi-204-79-180-24-nexthop-internet|204.79.180.0/24|Internet|
||||

\*Mi SUBNET odkazuje na rozsah IP adres pro podsíť ve formuláři x.x.x.x/y. Tyto informace najdete na webu Azure Portal ve vlastnostech podsítě.

Kromě toho můžete přidat položky do tabulky tras směrovat provoz, který má místní soukromé rozsahy IP jako cíl prostřednictvím brány virtuální sítě nebo zařízení virtuální sítě (NVA).

Pokud virtuální síť obsahuje vlastní DNS, musí být vlastní server DNS schopen vyřešit veřejné záznamy DNS. Použití dalších funkcí, jako je ověřování Azure AD může vyžadovat řešení dalších fqdn. Další informace naleznete [v tématu Nastavení vlastního DNS](sql-database-managed-instance-custom-dns.md).

### <a name="networking-constraints"></a>Omezení sítě

**TLS 1.2 se vynucuje na odchozích připojeních**: V lednu 2020 Microsoft vynutil TLS 1.2 pro vnitroobdislový provoz ve všech službách Azure. U instance spravované službou Azure SQL Database to mělo za následek vynucení tls 1.2 na odchozích připojeních používaných pro replikaci a připojení propojeného serveru k SQL Serveru. Pokud používáte verze SQL Serveru starší než 2016 se spravovanou instancí, ujistěte se, že byly použity [konkrétní aktualizace TLS 1.2.](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server)

Spravované instance momentálně nepodporují následující funkce virtuální sítě:
- **Partnerský vztah Microsoftu**: Povolení [partnerského vztahu Microsoftu](../expressroute/expressroute-faqs.md#microsoft-peering) na okruhech expresních tras přímo nebo přechodně s virtuální sítí, kde se nachází spravovaná instance, ovlivňuje tok provozu mezi součástmi spravované instance uvnitř virtuální sítě a službami, které závisí na příčinách problémů s dostupností. Očekává se, že nasazení spravovaných instancí do virtuální sítě s již povoleným partnerským vztahem Microsoftu se nezdaří.
- **Partnerský vztah globální virtuální sítě**: Připojení [partnerského vztahu virtuální sítě](../virtual-network/virtual-network-peering-overview.md) napříč oblastmi Azure nefunguje pro spravovanou instanci kvůli [dokumentovaným omezením nástroje pro vyrovnávání zatížení](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).
- **AzurePlatformDNS**: Použití [značky služby](../virtual-network/service-tags-overview.md) AzurePlatformDNS k blokování rozlišení DNS platformy by znemožnilo spravovanou instanci. Přestože spravovaná instance podporuje zákazníkem definované DNS pro rozlišení DNS uvnitř motoru, existuje závislost na službě DNS platformy pro operace platformy.
- **Brána NAT**: Použití [překladu adres Virtuální sítě](../virtual-network/nat-overview.md) k řízení odchozího připojení s konkrétní veřejnou IP adresou by znemožnilo spravovanou instanci. Služba spravované instance je aktuálně omezena na použití základního nástroje pro vyrovnávání zatížení, který neposkytuje koexistenci příchozích a odchozích toků s překladem dat virtuální sítě.

### <a name="deprecated-network-requirements-without-service-aided-subnet-configuration"></a>[Zastaralé] Požadavky na síť bez konfigurace podsítě podporované službou

Nasazení spravované instance ve vyhrazené podsíti uvnitř virtuální sítě. Podsíť musí mít tyto vlastnosti:

- **Vyhrazená podsíť:** Podsíť spravované instance nemůže obsahovat žádnou jinou cloudovou službu, která je k ní přidružená, a nemůže se jedná o podsíť brány. Podsíť nemůže obsahovat žádný prostředek kromě spravované instance a později nelze přidat další typy prostředků v podsíti.
- **Skupina zabezpečení sítě (NSG):** Skupina zabezpečení sítě, která je přidružena k virtuální síti, musí před všemi ostatními pravidly definovat [příchozí pravidla zabezpečení](#mandatory-inbound-security-rules) a odchozí pravidla [zabezpečení.](#mandatory-outbound-security-rules) Skupina zabezpečení sítě můžete použít k řízení přístupu ke koncovému bodu dat spravované instance filtrováním přenosů na portu 1433 a portech 11000-11999, když je spravovaná instance nakonfigurována pro přesměrování připojení.
- **Uživatelem definovaná tabulka trasy (UDR):** Tabulka UDR, která je přidružená k virtuální síti, musí obsahovat konkrétní [položky](#user-defined-routes).
- **Žádné koncové body služby:** Ke podsíti spravované instance by neměl být přidružen žádný koncový bod služby. Ujistěte se, že možnost koncových bodů služby je zakázána při vytváření virtuální sítě.
- **Dostatečné IP adresy:** Podsíť spravované instance musí mít alespoň 16 adres IP. Doporučené minimum je 32 IP adres. Další informace naleznete [v tématu Určení velikosti podsítě pro spravované instance](sql-database-managed-instance-determine-size-vnet-subnet.md). Spravované instance můžete nasadit v [existující síti](sql-database-managed-instance-configure-vnet-subnet.md) po jejich konfiguraci tak, aby [splňovaly požadavky na síť pro spravované instance](#network-requirements). V opačném případě vytvořte [novou síť a podsíť](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Novou spravovanou instanci nelze nasadit, pokud cílová podsíť tyto charakteristiky nechybí. Při vytváření spravované instance se v podsíti použije zásada záměru sítě, aby se zabránilo nekompatibilním změnám v nastavení sítě. Po odebrání poslední instance z podsítě je odebrána také zásada záměru sítě.

### <a name="mandatory-inbound-security-rules"></a>Povinná pravidla zabezpečení příchozích

| Název       |Port                        |Protocol (Protokol)|Zdroj           |Cíl|Akce|
|------------|----------------------------|--------|-----------------|-----------|------|
|správa  |9000, 9003, 1438, 1440, 1452|TCP     |Všechny              |PODSÍŤ MI  |Povolit |
|mi_subnet   |Všechny                         |Všechny     |PODSÍŤ MI        |PODSÍŤ MI  |Povolit |
|health_probe|Všechny                         |Všechny     |AzureLoadBalancer|PODSÍŤ MI  |Povolit |

### <a name="mandatory-outbound-security-rules"></a>Povinná pravidla odchozího zabezpečení

| Název       |Port          |Protocol (Protokol)|Zdroj           |Cíl|Akce|
|------------|--------------|--------|-----------------|-----------|------|
|správa  |443, 12000    |TCP     |PODSÍŤ MI        |AzureCloud |Povolit |
|mi_subnet   |Všechny           |Všechny     |PODSÍŤ MI        |PODSÍŤ MI  |Povolit |

> [!IMPORTANT]
> Ujistěte se, že existuje pouze jedno příchozí pravidlo pro porty 9000, 9003, 1438, 1440, 1452 a jedno odchozí pravidlo pro porty 443, 12000. Zřizování spravované instance prostřednictvím nasazení Azure Resource Manageru se nezdaří, pokud jsou příchozí a odchozí pravidla nakonfigurovaná samostatně pro každý port. Pokud jsou tyto porty v samostatných pravidlech, nasazení se nezdaří s kódem chyby`VnetSubnetConflictWithIntendedPolicy`

\*Mi SUBNET odkazuje na rozsah IP adres pro podsíť ve formuláři x.x.x.x/y. Tyto informace najdete na webu Azure Portal ve vlastnostech podsítě.

> [!IMPORTANT]
> Přestože požadovaná pravidla příchozího zabezpečení umožňují přenosy z _libovolného_ zdroje na portech 9000, 9003, 1438, 1440 a 1452, jsou tyto porty chráněny integrovanou bránou firewall. Další informace naleznete [v tématu Určení adresy koncového bodu správy](sql-database-managed-instance-find-management-endpoint-ip-address.md).

> [!NOTE]
> Pokud používáte transakční replikaci ve spravované instanci a pokud používáte libovolnou databázi instancí jako vydavatele nebo distributora, otevřete port 445 (odchozí TCP) v pravidlech zabezpečení podsítě. Tento port umožní přístup ke sdílené složce Azure.

### <a name="user-defined-routes"></a>Uživatelem definované trasy

|Název|Předpona adresy|Další směrování|
|----|--------------|-------|
|subnet_to_vnetlocal|PODSÍŤ MI|Virtuální síť|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|mi-20-33-16-nexthop-internet|20.33.0.0/16|Internet|
|mi-20-34-15-nexthop-internet|20.34.0.0/15|Internet|
|mi-20-36-14-nexthop-internet|20.36.0.0/14|Internet|
|mi-20-40-13-nexthop-internet|20.40.0.0/13|Internet|
|mi-20-48-12-nexthop-internet|20.48.0.0/12|Internet|
|mi-20-64-10-nexthop-internet|20.64.0.0/10|Internet|
|mi-20-128-16-nexthop-internet|20.128.0.0/16|Internet|
|mi-20-135-16-nexthop-internet|20.135.0.0/16|Internet|
|mi-20-136-16-nexthop-internet|20.136.0.0/16|Internet|
|mi-20-140-15-nexthop-internet|20.140.0.0/15|Internet|
|mi-20-143-16-nexthop-internet|20.143.0.0/16|Internet|
|mi-20-144-14-nexthop-internet|20.144.0.0/14|Internet|
|mi-20-150-15-nexthop-internet|20.150.0.0/15|Internet|
|mi-20-160-12-nexthop-internet|20.160.0.0/12|Internet|
|mi-20-176-14-nexthop-internet|20.176.0.0/14|Internet|
|mi-20-180-14-nexthop-internet|20.180.0.0/14|Internet|
|mi-20-184-13-nexthop-internet|20.184.0.0/13|Internet|
|mi-20-192-10-nexthop-internet|20.192.0.0/10|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|mi-51-4-15-nexthop-internet|51.4.0.0/15|Internet|
|mi-51-8-16-nexthop-internet|51.8.0.0/16|Internet|
|mi-51-10-15-nexthop-internet|51.10.0.0/15|Internet|
|mi-51-18-16-nexthop-internet|51.18.0.0/16|Internet|
|mi-51-51-16-nexthop-internet|51.51.0.0/16|Internet|
|mi-51-53-16-nexthop-internet|51.53.0.0/16|Internet|
|mi-51-103-16-nexthop-internet|51.103.0.0/16|Internet|
|mi-51-104-15-nexthop-internet|51.104.0.0/15|Internet|
|mi-51-132-16-nexthop-internet|51.132.0.0/16|Internet|
|mi-51-136-15-nexthop-internet|51.136.0.0/15|Internet|
|mi-51-138-16-nexthop-internet|51.138.0.0/16|Internet|
|mi-51-140-14-nexthop-internet|51.140.0.0/14|Internet|
|mi-51-144-15-nexthop-internet|51.144.0.0/15|Internet|
|mi-52-96-12-nexthop-internet|52.96.0.0/12|Internet|
|mi-52-112-14-nexthop-internet|52.112.0.0/14|Internet|
|mi-52-125-16-nexthop-internet|52.125.0.0/16|Internet|
|mi-52-126-15-nexthop-internet|52.126.0.0/15|Internet|
|mi-52-130-15-nexthop-internet|52.130.0.0/15|Internet|
|mi-52-132-14-nexthop-internet|52.132.0.0/14|Internet|
|mi-52-136-13-nexthop-internet|52.136.0.0/13|Internet|
|mi-52-145-16-nexthop-internet|52.145.0.0/16|Internet|
|mi-52-146-15-nexthop-internet|52.146.0.0/15|Internet|
|mi-52-148-14-nexthop-internet|52.148.0.0/14|Internet|
|mi-52-152-13-nexthop-internet|52.152.0.0/13|Internet|
|mi-52-160-11-nexthop-internet|52.160.0.0/11|Internet|
|mi-52-224-11-nexthop-internet|52.224.0.0/11|Internet|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|Internet|
|mi-103-25-156-24-nexthop-internet|103.25.156.0/24|Internet|
|mi-103-25-157-24-nexthop-internet|103.25.157.0/24|Internet|
|mi-103-25-158-23-nexthop-internet|103.25.158.0/23|Internet|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|Internet|
|mi-131-107-16-nexthop-internet|131.107.0.0/16|Internet|
|mi-131-253-1-24-nexthop-internet|131.253.1.0/24|Internet|
|mi-131-253-3-24-nexthop-internet|131.253.3.0/24|Internet|
|mi-131-253-5-24-nexthop-internet|131.253.5.0/24|Internet|
|mi-131-253-6-24-nexthop-internet|131.253.6.0/24|Internet|
|mi-131-253-8-24-nexthop-internet|131.253.8.0/24|Internet|
|mi-131-253-12-22-nexthop-internet|131.253.12.0/22|Internet|
|mi-131-253-16-23-nexthop-internet|131.253.16.0/23|Internet|
|mi-131-253-18-24-nexthop-internet|131.253.18.0/24|Internet|
|mi-131-253-21-24-nexthop-internet|131.253.21.0/24|Internet|
|mi-131-253-22-23-nexthop-internet|131.253.22.0/23|Internet|
|mi-131-253-24-21-nexthop-internet|131.253.24.0/21|Internet|
|mi-131-253-32-20-nexthop-internet|131.253.32.0/20|Internet|
|mi-131-253-61-24-nexthop-internet|131.253.61.0/24|Internet|
|mi-131-253-62-23-nexthop-internet|131.253.62.0/23|Internet|
|mi-131-253-64-18-nexthop-internet|131.253.64.0/18|Internet|
|mi-131-253-128-17-nexthop-internet|131.253.128.0/17|Internet|
|mi-132-245-16-nexthop-internet|132.245.0.0/16|Internet|
|mi-134-170-16-nexthop-internet|134.170.0.0/16|Internet|
|mi-134-177-16-nexthop-internet|134.177.0.0/16|Internet|
|mi-137-116-15-nexthop-internet|137.116.0.0/15|Internet|
|mi-137-135-16-nexthop-internet|137.135.0.0/16|Internet|
|mi-138-91-16-nexthop-internet|138.91.0.0/16|Internet|
|mi-138-196-16-nexthop-internet|138.196.0.0/16|Internet|
|mi-139-217-16-nexthop-internet|139.217.0.0/16|Internet|
|mi-139-219-16-nexthop-internet|139.219.0.0/16|Internet|
|mi-141-251-16-nexthop-internet|141.251.0.0/16|Internet|
|mi-146-147-16-nexthop-internet|146.147.0.0/16|Internet|
|mi-147-243-16-nexthop-internet|147.243.0.0/16|Internet|
|mi-150-171-16-nexthop-internet|150.171.0.0/16|Internet|
|mi-150-242-48-22-nexthop-internet|150.242.48.0/22|Internet|
|mi-157-54-15-nexthop-internet|157.54.0.0/15|Internet|
|mi-157-56-14-nexthop-internet|157.56.0.0/14|Internet|
|mi-157-60-16-nexthop-internet|157.60.0.0/16|Internet|
|mi-167-105-16-nexthop-internet|167.105.0.0/16|Internet|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
|mi-192-197-157-24-nexthop-internet|192.197.157.0/24|Internet|
|mi-193-149-64-19-nexthop-internet|193.149.64.0/19|Internet|
|mi-193-221-113-24-nexthop-internet|193.221.113.0/24|Internet|
|mi-194-69-96-19-nexthop-internet|194.69.96.0/19|Internet|
|mi-194-110-197-24-nexthop-internet|194.110.197.0/24|Internet|
|mi-198-105-232-22-nexthop-internet|198.105.232.0/22|Internet|
|mi-198-200-130-24-nexthop-internet|198.200.130.0/24|Internet|
|mi-198-206-164-24-nexthop-internet|198.206.164.0/24|Internet|
|mi-199-60-28-24-nexthop-internet|199.60.28.0/24|Internet|
|mi-199-74-210-24-nexthop-internet|199.74.210.0/24|Internet|
|mi-199-103-90-23-nexthop-internet|199.103.90.0/23|Internet|
|mi-199-103-122-24-nexthop-internet|199.103.122.0/24|Internet|
|mi-199-242-32-20-nexthop-internet|199.242.32.0/20|Internet|
|mi-199-242-48-21-nexthop-internet|199.242.48.0/21|Internet|
|mi-202-89-224-20-nexthop-internet|202.89.224.0/20|Internet|
|mi-204-13-120-21-nexthop-internet|204.13.120.0/21|Internet|
|mi-204-14-180-22-nexthop-internet|204.14.180.0/22|Internet|
|mi-204-79-135-24-nexthop-internet|204.79.135.0/24|Internet|
|mi-204-79-179-24-nexthop-internet|204.79.179.0/24|Internet|
|mi-204-79-181-24-nexthop-internet|204.79.181.0/24|Internet|
|mi-204-79-188-24-nexthop-internet|204.79.188.0/24|Internet|
|mi-204-79-195-24-nexthop-internet|204.79.195.0/24|Internet|
|mi-204-79-196-23-nexthop-internet|204.79.196.0/23|Internet|
|mi-204-79-252-24-nexthop-internet|204.79.252.0/24|Internet|
|mi-204-152-18-23-nexthop-internet|204.152.18.0/23|Internet|
|mi-204-152-140-23-nexthop-internet|204.152.140.0/23|Internet|
|mi-204-231-192-24-nexthop-internet|204.231.192.0/24|Internet|
|mi-204-231-194-23-nexthop-internet|204.231.194.0/23|Internet|
|mi-204-231-197-24-nexthop-internet|204.231.197.0/24|Internet|
|mi-204-231-198-23-nexthop-internet|204.231.198.0/23|Internet|
|mi-204-231-200-21-nexthop-internet|204.231.200.0/21|Internet|
|mi-204-231-208-20-nexthop-internet|204.231.208.0/20|Internet|
|mi-204-231-236-24-nexthop-internet|204.231.236.0/24|Internet|
|mi-205-174-224-20-nexthop-internet|205.174.224.0/20|Internet|
|mi-206-138-168-21-nexthop-internet|206.138.168.0/21|Internet|
|mi-206-191-224-19-nexthop-internet|206.191.224.0/19|Internet|
|mi-207-46-16-nexthop-internet|207.46.0.0/16|Internet|
|mi-207-68-128-18-nexthop-internet|207.68.128.0/18|Internet|
|mi-208-68-136-21-nexthop-internet|208.68.136.0/21|Internet|
|mi-208-76-44-22-nexthop-internet|208.76.44.0/22|Internet|
|mi-208-84-21-nexthop-internet|208.84.0.0/21|Internet|
|mi-209-240-192-19-nexthop-internet|209.240.192.0/19|Internet|
|mi-213-199-128-18-nexthop-internet|213.199.128.0/18|Internet|
|mi-216-32-180-22-nexthop-internet|216.32.180.0/22|Internet|
|mi-216-220-208-20-nexthop-internet|216.220.208.0/20|Internet|
|mi-23-96-13-nexthop-internet|23.96.0.0/13|Internet|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|Internet|
|mi-51-13-17-nexthop-internet|51.13.0.0/17|Internet|
|mi-51-107-16-nexthop-internet|51.107.0.0/16|Internet|
|mi-51-116-16-nexthop-internet|51.116.0.0/16|Internet|
|mi-51-120-16-nexthop-internet|51.120.0.0/16|Internet|
|mi-51-120-128-17-nexthop-internet|51.120.128.0/17|Internet|
|mi-51-124-16-nexthop-internet|51.124.0.0/16|Internet|
|mi-102-37-18-nexthop-internet|102.37.0.0/18|Internet|
|mi-102-133-16-nexthop-internet|102.133.0.0/16|Internet|
|mi-199-30-16-20-nexthop-internet|199.30.16.0/20|Internet|
|mi-204-79-180-24-nexthop-internet|204.79.180.0/24|Internet|
||||

## <a name="next-steps"></a>Další kroky

- Přehled naleznete v tématu [SQL Database rozšířené zabezpečení dat](sql-database-managed-instance.md).
- Zjistěte, jak [nastavit novou virtuální síť Azure](sql-database-managed-instance-create-vnet-subnet.md) nebo existující virtuální síť [Azure,](sql-database-managed-instance-configure-vnet-subnet.md) kde můžete nasadit spravované instance.
- [Vypočítejte velikost podsítě,](sql-database-managed-instance-determine-size-vnet-subnet.md) ve které chcete nasadit spravované instance.
- Přečtěte si, jak vytvořit spravovanou instanci:
  - Z [portálu Azure](sql-database-managed-instance-get-started.md).
  - Pomocí [prostředí PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md).
  - Pomocí [šablony Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Pomocí [šablony Azure Resource Manager (pomocí JumpBox, včetně SSMS)](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/). 
