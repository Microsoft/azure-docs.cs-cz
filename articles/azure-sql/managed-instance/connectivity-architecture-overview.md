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
ms.date: 03/17/2020
ms.openlocfilehash: 81d0731f6ea77325b3f33f91bf8d5d1386dab2fb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91283373"
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
- **Dostatečná IP adresa:** Podsíť spravované instance SQL musí mít aspoň 16 IP adres. Doporučené minimum je 32 IP adres. Další informace najdete v tématu [Určení velikosti podsítě pro spravovanou instanci SQL](vnet-subnet-determine-size.md). Spravované instance můžete nasadit v [existující síti](vnet-existing-add-subnet.md) poté, co ji nakonfigurujete tak, aby splňovala [požadavky na síť pro spravovanou instanci SQL](#network-requirements). Jinak vytvořte [novou síť a podsíť](virtual-network-subnet-create-arm-template.md).

> [!IMPORTANT]
> Při vytváření spravované instance se v podsíti použije zásada záměru sítě, aby se zabránilo nekompatibilním změnám nastavení sítě. Po odebrání poslední instance z podsítě se odstraní také zásada záměru sítě.

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>Povinná příchozí pravidla zabezpečení s konfigurací podsítě s podporou služby

| Name       |Port                        |Protokol|Zdroj           |Cíl|Akce|
|------------|----------------------------|--------|-----------------|-----------|------|
|správa  |9000, 9003, 1438, 1440, 1452|TCP     |SqlManagement    |PODSÍŤ MI  |Povolit |
|            |9000, 9003                  |TCP     |CorpnetSaw       |PODSÍŤ MI  |Povolit |
|            |9000, 9003                  |TCP     |CorpnetPublic    |PODSÍŤ MI  |Povolit |
|mi_subnet   |Všechny                         |Všechny     |PODSÍŤ MI        |PODSÍŤ MI  |Povolit |
|health_probe|Všechny                         |Všechny     |AzureLoadBalancer|PODSÍŤ MI  |Povolit |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>Povinná odchozí pravidla zabezpečení s konfigurací podsítě s podporou služby

| Name       |Port          |Protokol|Zdroj           |Cíl|Akce|
|------------|--------------|--------|-----------------|-----------|------|
|správa  |443, 12000    |TCP     |PODSÍŤ MI        |AzureCloud |Povolit |
|mi_subnet   |Všechny           |Všechny     |PODSÍŤ MI        |PODSÍŤ MI  |Povolit |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>Uživatelem definované trasy s konfigurací podsítě s podporou služby

|Name|Předpona adresy|Další směrování|
|----|--------------|-------|
|podsíť do vnetlocal|PODSÍŤ MI|Virtuální síť|
|mi-13-64-11-nexthop-Internet|13.64.0.0/11|Internet|
|mi-13-104-14-nexthop-Internet|13.104.0.0/14|Internet|
|mi-20-33-16-nexthop-Internet|20.33.0.0/16|Internet|
|mi-20-34-15-nexthop-Internet|20.34.0.0/15|Internet|
|mi-20-36-14-nexthop-Internet|20.36.0.0/14|Internet|
|mi-20-40-13-nexthop-Internet|20.40.0.0/13|Internet|
|mi-20-48-12-nexthop-Internet|20.48.0.0/12|Internet|
|mi-20-64-10-nexthop-Internet|20.64.0.0/10|Internet|
|mi-20-128-16-nexthop-Internet|20.128.0.0/16|Internet|
|mi-20-135-16-nexthop-Internet|20.135.0.0/16|Internet|
|mi-20-136-16-nexthop-Internet|20.136.0.0/16|Internet|
|mi-20-140-15-nexthop-Internet|20.140.0.0/15|Internet|
|mi-20-143-16-nexthop-Internet|20.143.0.0/16|Internet|
|mi-20-144-14-nexthop-Internet|20.144.0.0/14|Internet|
|mi-20-150-15-nexthop-Internet|20.150.0.0/15|Internet|
|mi-20-160-12-nexthop-Internet|20.160.0.0/12|Internet|
|mi-20-176-14-nexthop-Internet|20.176.0.0/14|Internet|
|mi-20-180-14-nexthop-Internet|20.180.0.0/14|Internet|
|mi-20-184-13-nexthop-Internet|20.184.0.0/13|Internet|
|mi-20-192-10-nexthop-Internet|20.192.0.0/10|Internet|
|mi-40-64-10-nexthop-Internet|40.64.0.0/10|Internet|
|mi-51-4-15-nexthop-Internet|51.4.0.0/15|Internet|
|mi-51-8-16-nexthop-Internet|51.8.0.0/16|Internet|
|mi-51-10-15-nexthop-Internet|51.10.0.0/15|Internet|
|mi-51-18-16-nexthop-Internet|51.18.0.0/16|Internet|
|mi-51-51-16-nexthop-Internet|51.51.0.0/16|Internet|
|mi-51-53-16-nexthop-Internet|51.53.0.0/16|Internet|
|mi-51-103-16-nexthop-Internet|51.103.0.0/16|Internet|
|mi-51-104-15-nexthop-Internet|51.104.0.0/15|Internet|
|mi-51-132-16-nexthop-Internet|51.132.0.0/16|Internet|
|mi-51-136-15-nexthop-Internet|51.136.0.0/15|Internet|
|mi-51-138-16-nexthop-Internet|51.138.0.0/16|Internet|
|mi-51-140-14-nexthop-Internet|51.140.0.0/14|Internet|
|mi-51-144-15-nexthop-Internet|51.144.0.0/15|Internet|
|mi-52-96-12-nexthop-Internet|52.96.0.0/12|Internet|
|mi-52-112-14-nexthop-Internet|52.112.0.0/14|Internet|
|mi-52-125-16-nexthop-Internet|52.125.0.0/16|Internet|
|mi-52-126-15-nexthop-Internet|52.126.0.0/15|Internet|
|mi-52-130-15-nexthop-Internet|52.130.0.0/15|Internet|
|mi-52-132-14-nexthop-Internet|52.132.0.0/14|Internet|
|mi-52-136-13-nexthop-Internet|52.136.0.0/13|Internet|
|mi-52-145-16-nexthop-Internet|52.145.0.0/16|Internet|
|mi-52-146-15-nexthop-Internet|52.146.0.0/15|Internet|
|mi-52-148-14-nexthop-Internet|52.148.0.0/14|Internet|
|mi-52-152-13-nexthop-Internet|52.152.0.0/13|Internet|
|mi-52-160-11-nexthop-Internet|52.160.0.0/11|Internet|
|mi-52-224-11-nexthop-Internet|52.224.0.0/11|Internet|
|mi-64-4-18-nexthop-Internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-Internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-Internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-Internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-Internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-Internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-Internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-Internet|103.9.8.0/22|Internet|
|mi-103-25-156-24-nexthop-Internet|103.25.156.0/24|Internet|
|mi-103-25-157-24-nexthop-Internet|103.25.157.0/24|Internet|
|mi-103-25-158-23-nexthop-Internet|103.25.158.0/23|Internet|
|mi-103-36-96-22-nexthop-Internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-Internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-Internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-Internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-Internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-Internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-Internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-Internet|129.75.0.0/16|Internet|
|mi-131-107-16-nexthop-Internet|131.107.0.0/16|Internet|
|mi-131-253-1-24-nexthop-Internet|131.253.1.0/24|Internet|
|mi-131-253-3-24-nexthop-Internet|131.253.3.0/24|Internet|
|mi-131-253-5-24-nexthop-Internet|131.253.5.0/24|Internet|
|mi-131-253-6-24-nexthop-Internet|131.253.6.0/24|Internet|
|mi-131-253-8-24-nexthop-Internet|131.253.8.0/24|Internet|
|mi-131-253-12-22-nexthop-Internet|131.253.12.0/22|Internet|
|mi-131-253-16-23-nexthop-Internet|131.253.16.0/23|Internet|
|mi-131-253-18-24-nexthop-Internet|131.253.18.0/24|Internet|
|mi-131-253-21-24-nexthop-Internet|131.253.21.0/24|Internet|
|mi-131-253-22-23-nexthop-Internet|131.253.22.0/23|Internet|
|mi-131-253-24-21-nexthop-Internet|131.253.24.0/21|Internet|
|mi-131-253-32-20-nexthop-Internet|131.253.32.0/20|Internet|
|mi-131-253-61-24-nexthop-Internet|131.253.61.0/24|Internet|
|mi-131-253-62-23-nexthop-Internet|131.253.62.0/23|Internet|
|mi-131-253-64-18-nexthop-Internet|131.253.64.0/18|Internet|
|mi-131-253-128-17-nexthop-Internet|131.253.128.0/17|Internet|
|mi-132-245-16-nexthop-Internet|132.245.0.0/16|Internet|
|mi-134-170-16-nexthop-Internet|134.170.0.0/16|Internet|
|mi-134-177-16-nexthop-Internet|134.177.0.0/16|Internet|
|mi-137-116-15-nexthop-Internet|137.116.0.0/15|Internet|
|mi-137-135-16-nexthop-Internet|137.135.0.0/16|Internet|
|mi-138-91-16-nexthop-Internet|138.91.0.0/16|Internet|
|mi-138-196-16-nexthop-Internet|138.196.0.0/16|Internet|
|mi-139-217-16-nexthop-Internet|139.217.0.0/16|Internet|
|mi-139-219-16-nexthop-Internet|139.219.0.0/16|Internet|
|mi-141-251-16-nexthop-Internet|141.251.0.0/16|Internet|
|mi-146-147-16-nexthop-Internet|146.147.0.0/16|Internet|
|mi-147-243-16-nexthop-Internet|147.243.0.0/16|Internet|
|mi-150-171-16-nexthop-Internet|150.171.0.0/16|Internet|
|mi-150-242-48-22-nexthop-Internet|150.242.48.0/22|Internet|
|mi-157-54-15-nexthop-Internet|157.54.0.0/15|Internet|
|mi-157-56-14-nexthop-Internet|157.56.0.0/14|Internet|
|mi-157-60-16-nexthop-Internet|157.60.0.0/16|Internet|
|mi-167-105-16-nexthop-Internet|167.105.0.0/16|Internet|
|mi-167-220-16-nexthop-Internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-Internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-Internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-Internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-Internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-Internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-Internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-Internet|192.84.160.0/23|Internet|
|mi-192-197-157-24-nexthop-Internet|192.197.157.0/24|Internet|
|mi-193-149-64-19-nexthop-Internet|193.149.64.0/19|Internet|
|mi-193-221-113-24-nexthop-Internet|193.221.113.0/24|Internet|
|mi-194-69-96-19-nexthop-Internet|194.69.96.0/19|Internet|
|mi-194-110-197-24-nexthop-Internet|194.110.197.0/24|Internet|
|mi-198-105-232-22-nexthop-Internet|198.105.232.0/22|Internet|
|mi-198-200-130-24-nexthop-Internet|198.200.130.0/24|Internet|
|mi-198-206-164-24-nexthop-Internet|198.206.164.0/24|Internet|
|mi-199-60-28-24-nexthop-Internet|199.60.28.0/24|Internet|
|mi-199-74-210-24-nexthop-Internet|199.74.210.0/24|Internet|
|mi-199-103-90-23-nexthop-Internet|199.103.90.0/23|Internet|
|mi-199-103-122-24-nexthop-Internet|199.103.122.0/24|Internet|
|mi-199-242-32-20-nexthop-Internet|199.242.32.0/20|Internet|
|mi-199-242-48-21-nexthop-Internet|199.242.48.0/21|Internet|
|mi-202-89-224-20-nexthop-Internet|202.89.224.0/20|Internet|
|mi-204-13-120-21-nexthop-Internet|204.13.120.0/21|Internet|
|mi-204-14-180-22-nexthop-Internet|204.14.180.0/22|Internet|
|mi-204-79-135-24-nexthop-Internet|204.79.135.0/24|Internet|
|mi-204-79-179-24-nexthop-Internet|204.79.179.0/24|Internet|
|mi-204-79-181-24-nexthop-Internet|204.79.181.0/24|Internet|
|mi-204-79-188-24-nexthop-Internet|204.79.188.0/24|Internet|
|mi-204-79-195-24-nexthop-Internet|204.79.195.0/24|Internet|
|mi-204-79-196-23-nexthop-Internet|204.79.196.0/23|Internet|
|mi-204-79-252-24-nexthop-Internet|204.79.252.0/24|Internet|
|mi-204-152-18-23-nexthop-Internet|204.152.18.0/23|Internet|
|mi-204-152-140-23-nexthop-Internet|204.152.140.0/23|Internet|
|mi-204-231-192-24-nexthop-Internet|204.231.192.0/24|Internet|
|mi-204-231-194-23-nexthop-Internet|204.231.194.0/23|Internet|
|mi-204-231-197-24-nexthop-Internet|204.231.197.0/24|Internet|
|mi-204-231-198-23-nexthop-Internet|204.231.198.0/23|Internet|
|mi-204-231-200-21-nexthop-Internet|204.231.200.0/21|Internet|
|mi-204-231-208-20-nexthop-Internet|204.231.208.0/20|Internet|
|mi-204-231-236-24-nexthop-Internet|204.231.236.0/24|Internet|
|mi-205-174-224-20-nexthop-Internet|205.174.224.0/20|Internet|
|mi-206-138-168-21-nexthop-Internet|206.138.168.0/21|Internet|
|mi-206-191-224-19-nexthop-Internet|206.191.224.0/19|Internet|
|mi-207-46-16-nexthop-Internet|207.46.0.0/16|Internet|
|mi-207-68-128-18-nexthop-Internet|207.68.128.0/18|Internet|
|mi-208-68-136-21-nexthop-Internet|208.68.136.0/21|Internet|
|mi-208-76-44-22-nexthop-Internet|208.76.44.0/22|Internet|
|mi-208-84-21-nexthop-Internet|208.84.0.0/21|Internet|
|mi-209-240-192-19-nexthop-Internet|209.240.192.0/19|Internet|
|mi-213-199-128-18-nexthop-Internet|213.199.128.0/18|Internet|
|mi-216-32-180-22-nexthop-Internet|216.32.180.0/22|Internet|
|mi-216-220-208-20-nexthop-Internet|216.220.208.0/20|Internet|
|mi-23-96-13-nexthop-Internet|23.96.0.0/13|Internet|
|mi-42-159-16-nexthop-Internet|42.159.0.0/16|Internet|
|mi-51-13-17-nexthop-Internet|51.13.0.0/17|Internet|
|mi-51-107-16-nexthop-Internet|51.107.0.0/16|Internet|
|mi-51-116-16-nexthop-Internet|51.116.0.0/16|Internet|
|mi-51-120-16-nexthop-Internet|51.120.0.0/16|Internet|
|mi-51-120-128-17-nexthop-Internet|51.120.128.0/17|Internet|
|mi-51-124-16-nexthop-Internet|51.124.0.0/16|Internet|
|mi-102-37-18-nexthop-Internet|102.37.0.0/18|Internet|
|mi-102-133-16-nexthop-Internet|102.133.0.0/16|Internet|
|mi-199-30-16-20-nexthop-Internet|199.30.16.0/20|Internet|
|mi-204-79-180-24-nexthop-Internet|204.79.180.0/24|Internet|
||||

\* PODSÍŤ MI odkazuje na rozsah IP adres podsítě ve formátu x. x. x. x/y. Tyto informace můžete najít v Azure Portal ve vlastnostech podsítě.

Kromě toho můžete přidat položky do směrovací tabulky pro směrování provozu, který má místní rozsahy privátních IP adres jako cíl prostřednictvím brány virtuální sítě nebo zařízení virtuální sítě (síťové virtuální zařízení).

Pokud virtuální síť obsahuje vlastní DNS, vlastní server DNS musí být schopný přeložit veřejné záznamy DNS. Použití dalších funkcí, jako je ověřování Azure AD, může vyžadovat vyřešení dalších plně kvalifikovaných názvů domén. Další informace najdete v tématu [nastavení vlastního DNS](custom-dns-configure.md).

### <a name="networking-constraints"></a>Omezení sítě

Pro **odchozí připojení se vynutilo tls 1,2**: v lednu 2020 Microsoft vynutila 1,2 TLS pro provoz uvnitř služby ve všech službách Azure. U spravované instance Azure SQL to vedlo k vymáhání TLS 1,2 u odchozích připojení používaných pro replikaci a připojení k serveru SQL Server. Pokud používáte verze SQL Server starší než 2016 se službou SQL Managed instance, zajistěte, aby byly použity [specifické aktualizace TLS 1,2](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) .

U spravované instance SQL se aktuálně nepodporují následující funkce virtuální sítě:

- **Partnerský vztah Microsoftu**: povolení [partnerského vztahu Microsoftu](../../expressroute/expressroute-faqs.md#microsoft-peering) na okruhech ExpressRoute partnerských vztahů přímo nebo v transitu s virtuální sítí, kde se nachází spravovaná instance SQL, ovlivňuje tok přenosů mezi komponentami spravované instance SQL uvnitř virtuální sítě a služeb, na kterých závisí, a způsobující problémy s dostupností. Očekává se, že nasazení spravované instance SQL do virtuální sítě s partnerským vztahem Microsoftu je už povolené.
- **Globální partnerské vztahy virtuálních sítí**: připojení [partnerských vztahů virtuálních sítí](../../virtual-network/virtual-network-peering-overview.md) napříč oblastmi Azure nefunguje pro SQL Managed instance z důvodu [dokumentovaných omezení nástroje pro vyrovnávání zatížení](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).
- **AzurePlatformDNS**: použití [značky služby](../../virtual-network/service-tags-overview.md) AZUREPLATFORMDNS k blokování překladu DNS platformy by vygenerovalo nedostupné spravované instance SQL. I když spravovaná instance SQL podporuje DNS definované uživatelem pro překlad DNS v rámci motoru, je závislá na platformě DNS platformy pro operace platforem.
- **Brána NAT**: použití služby [Azure Virtual Network NAT](../../virtual-network/nat-overview.md) k řízení odchozího připojení s konkrétní veřejnou IP adresou by nedostupné pro vykreslování spravované instance SQL. Služba SQL Managed instance je momentálně omezená na použití základního nástroje pro vyrovnávání zatížení, který neposkytuje koexistenci příchozích a odchozích toků s Virtual Network překladem adres (NAT).

### <a name="deprecated-network-requirements-without-service-aided-subnet-configuration"></a>Zastaralé Požadavky na síť bez konfigurace podsítě pro službu

Nasaďte spravovanou instanci SQL do vyhrazené podsítě uvnitř virtuální sítě. Podsíť musí mít tyto charakteristiky:

- **Vyhrazená podsíť:** Podsíť spravované instance SQL nemůže obsahovat žádnou jinou cloudovou službu, která je k ní přidružená, a nemůže to být podsíť brány. Podsíť nemůže obsahovat žádný prostředek, ale spravovanou instanci SQL a nemůžete později přidat další typy prostředků v podsíti.
- **Skupina zabezpečení sítě (NSG):** NSG, která je přidružená k virtuální síti, musí definovat [příchozí pravidla zabezpečení](#mandatory-inbound-security-rules) a [odchozí pravidla zabezpečení](#mandatory-outbound-security-rules) před všemi ostatními pravidly. Pomocí NSG můžete řídit přístup ke koncovému bodu dat spravované instance SQL pomocí filtrování provozu na portech 1433 a porty 11000-11999 při konfiguraci spravované instance SQL pro připojení přesměrování.
- **Tabulka uživatelsky definované trasy (udr):** Tabulka UDR, která je přidružená k virtuální síti, musí zahrnovat konkrétní [položky](#user-defined-routes).
- **Žádné koncové body služby:** K podsíti spravované instance SQL by neměl být přidružen žádný koncový bod služby. Ujistěte se, že je při vytváření virtuální sítě možnost koncové body služby zakázaná.
- **Dostatečná IP adresa:** Podsíť spravované instance SQL musí mít aspoň 16 IP adres. Doporučené minimum je 32 IP adres. Další informace najdete v tématu [Určení velikosti podsítě pro spravovanou instanci SQL](vnet-subnet-determine-size.md). Spravované instance můžete nasadit v [existující síti](vnet-existing-add-subnet.md) poté, co ji nakonfigurujete tak, aby splňovala [požadavky na síť pro spravovanou instanci SQL](#network-requirements). Jinak vytvořte [novou síť a podsíť](virtual-network-subnet-create-arm-template.md).

> [!IMPORTANT]
> Nemůžete nasadit novou spravovanou instanci, pokud v cílové podsíti chybí tyto vlastnosti. Při vytváření spravované instance se v podsíti použije zásada záměru sítě, aby se zabránilo nekompatibilním změnám nastavení sítě. Po odebrání poslední instance z podsítě se odstraní také zásada záměru sítě.

### <a name="mandatory-inbound-security-rules"></a>Povinná příchozí pravidla zabezpečení

| Name       |Port                        |Protokol|Zdroj           |Cíl|Akce|
|------------|----------------------------|--------|-----------------|-----------|------|
|správa  |9000, 9003, 1438, 1440, 1452|TCP     |Všechny              |PODSÍŤ MI  |Povolit |
|mi_subnet   |Všechny                         |Všechny     |PODSÍŤ MI        |PODSÍŤ MI  |Povolit |
|health_probe|Všechny                         |Všechny     |AzureLoadBalancer|PODSÍŤ MI  |Povolit |

### <a name="mandatory-outbound-security-rules"></a>Povinná odchozí pravidla zabezpečení

| Name       |Port          |Protokol|Zdroj           |Cíl|Akce|
|------------|--------------|--------|-----------------|-----------|------|
|správa  |443, 12000    |TCP     |PODSÍŤ MI        |AzureCloud |Povolit |
|mi_subnet   |Všechny           |Všechny     |PODSÍŤ MI        |PODSÍŤ MI  |Povolit |

> [!IMPORTANT]
> Zajistěte, aby existovalo pouze jedno příchozí pravidlo pro porty 9000, 9003, 1438, 1440 a 1452 a jedno odchozí pravidlo pro porty 443 a 12000. Zřizování spravované instance SQL prostřednictvím Azure Resource Manager nasazení se nezdaří, pokud jsou příchozí a odchozí pravidla konfigurovaná samostatně pro každý port. Pokud jsou tyto porty v samostatných pravidlech, nasazení se nezdaří s kódem chyby `VnetSubnetConflictWithIntendedPolicy` .

\* PODSÍŤ MI odkazuje na rozsah IP adres podsítě ve formátu x. x. x. x/y. Tyto informace můžete najít v Azure Portal ve vlastnostech podsítě.

> [!IMPORTANT]
> I když požadovaná příchozí pravidla zabezpečení umožňují provoz z _libovolného_ zdroje na portech 9000, 9003, 1438, 1440 a 1452, jsou tyto porty chráněny integrovanou bránou firewall. Další informace najdete v tématu [určení adresy koncového bodu správy](management-endpoint-find-ip-address.md).

> [!NOTE]
> Pokud ve spravované instanci SQL použijete transakční replikaci a jako vydavatele nebo distributora použijete jakoukoli databázi instance, otevřete port 445 (odchozí TCP) v pravidlech zabezpečení podsítě. Tento port umožní přístup ke sdílené složce Azure.

### <a name="user-defined-routes"></a>Trasy definované uživatelem

|Name|Předpona adresy|Další směrování|
|----|--------------|-------|
|subnet_to_vnetlocal|PODSÍŤ MI|Virtuální síť|
|mi-13-64-11-nexthop-Internet|13.64.0.0/11|Internet|
|mi-13-104-14-nexthop-Internet|13.104.0.0/14|Internet|
|mi-20-33-16-nexthop-Internet|20.33.0.0/16|Internet|
|mi-20-34-15-nexthop-Internet|20.34.0.0/15|Internet|
|mi-20-36-14-nexthop-Internet|20.36.0.0/14|Internet|
|mi-20-40-13-nexthop-Internet|20.40.0.0/13|Internet|
|mi-20-48-12-nexthop-Internet|20.48.0.0/12|Internet|
|mi-20-64-10-nexthop-Internet|20.64.0.0/10|Internet|
|mi-20-128-16-nexthop-Internet|20.128.0.0/16|Internet|
|mi-20-135-16-nexthop-Internet|20.135.0.0/16|Internet|
|mi-20-136-16-nexthop-Internet|20.136.0.0/16|Internet|
|mi-20-140-15-nexthop-Internet|20.140.0.0/15|Internet|
|mi-20-143-16-nexthop-Internet|20.143.0.0/16|Internet|
|mi-20-144-14-nexthop-Internet|20.144.0.0/14|Internet|
|mi-20-150-15-nexthop-Internet|20.150.0.0/15|Internet|
|mi-20-160-12-nexthop-Internet|20.160.0.0/12|Internet|
|mi-20-176-14-nexthop-Internet|20.176.0.0/14|Internet|
|mi-20-180-14-nexthop-Internet|20.180.0.0/14|Internet|
|mi-20-184-13-nexthop-Internet|20.184.0.0/13|Internet|
|mi-20-192-10-nexthop-Internet|20.192.0.0/10|Internet|
|mi-40-64-10-nexthop-Internet|40.64.0.0/10|Internet|
|mi-51-4-15-nexthop-Internet|51.4.0.0/15|Internet|
|mi-51-8-16-nexthop-Internet|51.8.0.0/16|Internet|
|mi-51-10-15-nexthop-Internet|51.10.0.0/15|Internet|
|mi-51-18-16-nexthop-Internet|51.18.0.0/16|Internet|
|mi-51-51-16-nexthop-Internet|51.51.0.0/16|Internet|
|mi-51-53-16-nexthop-Internet|51.53.0.0/16|Internet|
|mi-51-103-16-nexthop-Internet|51.103.0.0/16|Internet|
|mi-51-104-15-nexthop-Internet|51.104.0.0/15|Internet|
|mi-51-132-16-nexthop-Internet|51.132.0.0/16|Internet|
|mi-51-136-15-nexthop-Internet|51.136.0.0/15|Internet|
|mi-51-138-16-nexthop-Internet|51.138.0.0/16|Internet|
|mi-51-140-14-nexthop-Internet|51.140.0.0/14|Internet|
|mi-51-144-15-nexthop-Internet|51.144.0.0/15|Internet|
|mi-52-96-12-nexthop-Internet|52.96.0.0/12|Internet|
|mi-52-112-14-nexthop-Internet|52.112.0.0/14|Internet|
|mi-52-125-16-nexthop-Internet|52.125.0.0/16|Internet|
|mi-52-126-15-nexthop-Internet|52.126.0.0/15|Internet|
|mi-52-130-15-nexthop-Internet|52.130.0.0/15|Internet|
|mi-52-132-14-nexthop-Internet|52.132.0.0/14|Internet|
|mi-52-136-13-nexthop-Internet|52.136.0.0/13|Internet|
|mi-52-145-16-nexthop-Internet|52.145.0.0/16|Internet|
|mi-52-146-15-nexthop-Internet|52.146.0.0/15|Internet|
|mi-52-148-14-nexthop-Internet|52.148.0.0/14|Internet|
|mi-52-152-13-nexthop-Internet|52.152.0.0/13|Internet|
|mi-52-160-11-nexthop-Internet|52.160.0.0/11|Internet|
|mi-52-224-11-nexthop-Internet|52.224.0.0/11|Internet|
|mi-64-4-18-nexthop-Internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-Internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-Internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-Internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-Internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-Internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-Internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-Internet|103.9.8.0/22|Internet|
|mi-103-25-156-24-nexthop-Internet|103.25.156.0/24|Internet|
|mi-103-25-157-24-nexthop-Internet|103.25.157.0/24|Internet|
|mi-103-25-158-23-nexthop-Internet|103.25.158.0/23|Internet|
|mi-103-36-96-22-nexthop-Internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-Internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-Internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-Internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-Internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-Internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-Internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-Internet|129.75.0.0/16|Internet|
|mi-131-107-16-nexthop-Internet|131.107.0.0/16|Internet|
|mi-131-253-1-24-nexthop-Internet|131.253.1.0/24|Internet|
|mi-131-253-3-24-nexthop-Internet|131.253.3.0/24|Internet|
|mi-131-253-5-24-nexthop-Internet|131.253.5.0/24|Internet|
|mi-131-253-6-24-nexthop-Internet|131.253.6.0/24|Internet|
|mi-131-253-8-24-nexthop-Internet|131.253.8.0/24|Internet|
|mi-131-253-12-22-nexthop-Internet|131.253.12.0/22|Internet|
|mi-131-253-16-23-nexthop-Internet|131.253.16.0/23|Internet|
|mi-131-253-18-24-nexthop-Internet|131.253.18.0/24|Internet|
|mi-131-253-21-24-nexthop-Internet|131.253.21.0/24|Internet|
|mi-131-253-22-23-nexthop-Internet|131.253.22.0/23|Internet|
|mi-131-253-24-21-nexthop-Internet|131.253.24.0/21|Internet|
|mi-131-253-32-20-nexthop-Internet|131.253.32.0/20|Internet|
|mi-131-253-61-24-nexthop-Internet|131.253.61.0/24|Internet|
|mi-131-253-62-23-nexthop-Internet|131.253.62.0/23|Internet|
|mi-131-253-64-18-nexthop-Internet|131.253.64.0/18|Internet|
|mi-131-253-128-17-nexthop-Internet|131.253.128.0/17|Internet|
|mi-132-245-16-nexthop-Internet|132.245.0.0/16|Internet|
|mi-134-170-16-nexthop-Internet|134.170.0.0/16|Internet|
|mi-134-177-16-nexthop-Internet|134.177.0.0/16|Internet|
|mi-137-116-15-nexthop-Internet|137.116.0.0/15|Internet|
|mi-137-135-16-nexthop-Internet|137.135.0.0/16|Internet|
|mi-138-91-16-nexthop-Internet|138.91.0.0/16|Internet|
|mi-138-196-16-nexthop-Internet|138.196.0.0/16|Internet|
|mi-139-217-16-nexthop-Internet|139.217.0.0/16|Internet|
|mi-139-219-16-nexthop-Internet|139.219.0.0/16|Internet|
|mi-141-251-16-nexthop-Internet|141.251.0.0/16|Internet|
|mi-146-147-16-nexthop-Internet|146.147.0.0/16|Internet|
|mi-147-243-16-nexthop-Internet|147.243.0.0/16|Internet|
|mi-150-171-16-nexthop-Internet|150.171.0.0/16|Internet|
|mi-150-242-48-22-nexthop-Internet|150.242.48.0/22|Internet|
|mi-157-54-15-nexthop-Internet|157.54.0.0/15|Internet|
|mi-157-56-14-nexthop-Internet|157.56.0.0/14|Internet|
|mi-157-60-16-nexthop-Internet|157.60.0.0/16|Internet|
|mi-167-105-16-nexthop-Internet|167.105.0.0/16|Internet|
|mi-167-220-16-nexthop-Internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-Internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-Internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-Internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-Internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-Internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-Internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-Internet|192.84.160.0/23|Internet|
|mi-192-197-157-24-nexthop-Internet|192.197.157.0/24|Internet|
|mi-193-149-64-19-nexthop-Internet|193.149.64.0/19|Internet|
|mi-193-221-113-24-nexthop-Internet|193.221.113.0/24|Internet|
|mi-194-69-96-19-nexthop-Internet|194.69.96.0/19|Internet|
|mi-194-110-197-24-nexthop-Internet|194.110.197.0/24|Internet|
|mi-198-105-232-22-nexthop-Internet|198.105.232.0/22|Internet|
|mi-198-200-130-24-nexthop-Internet|198.200.130.0/24|Internet|
|mi-198-206-164-24-nexthop-Internet|198.206.164.0/24|Internet|
|mi-199-60-28-24-nexthop-Internet|199.60.28.0/24|Internet|
|mi-199-74-210-24-nexthop-Internet|199.74.210.0/24|Internet|
|mi-199-103-90-23-nexthop-Internet|199.103.90.0/23|Internet|
|mi-199-103-122-24-nexthop-Internet|199.103.122.0/24|Internet|
|mi-199-242-32-20-nexthop-Internet|199.242.32.0/20|Internet|
|mi-199-242-48-21-nexthop-Internet|199.242.48.0/21|Internet|
|mi-202-89-224-20-nexthop-Internet|202.89.224.0/20|Internet|
|mi-204-13-120-21-nexthop-Internet|204.13.120.0/21|Internet|
|mi-204-14-180-22-nexthop-Internet|204.14.180.0/22|Internet|
|mi-204-79-135-24-nexthop-Internet|204.79.135.0/24|Internet|
|mi-204-79-179-24-nexthop-Internet|204.79.179.0/24|Internet|
|mi-204-79-181-24-nexthop-Internet|204.79.181.0/24|Internet|
|mi-204-79-188-24-nexthop-Internet|204.79.188.0/24|Internet|
|mi-204-79-195-24-nexthop-Internet|204.79.195.0/24|Internet|
|mi-204-79-196-23-nexthop-Internet|204.79.196.0/23|Internet|
|mi-204-79-252-24-nexthop-Internet|204.79.252.0/24|Internet|
|mi-204-152-18-23-nexthop-Internet|204.152.18.0/23|Internet|
|mi-204-152-140-23-nexthop-Internet|204.152.140.0/23|Internet|
|mi-204-231-192-24-nexthop-Internet|204.231.192.0/24|Internet|
|mi-204-231-194-23-nexthop-Internet|204.231.194.0/23|Internet|
|mi-204-231-197-24-nexthop-Internet|204.231.197.0/24|Internet|
|mi-204-231-198-23-nexthop-Internet|204.231.198.0/23|Internet|
|mi-204-231-200-21-nexthop-Internet|204.231.200.0/21|Internet|
|mi-204-231-208-20-nexthop-Internet|204.231.208.0/20|Internet|
|mi-204-231-236-24-nexthop-Internet|204.231.236.0/24|Internet|
|mi-205-174-224-20-nexthop-Internet|205.174.224.0/20|Internet|
|mi-206-138-168-21-nexthop-Internet|206.138.168.0/21|Internet|
|mi-206-191-224-19-nexthop-Internet|206.191.224.0/19|Internet|
|mi-207-46-16-nexthop-Internet|207.46.0.0/16|Internet|
|mi-207-68-128-18-nexthop-Internet|207.68.128.0/18|Internet|
|mi-208-68-136-21-nexthop-Internet|208.68.136.0/21|Internet|
|mi-208-76-44-22-nexthop-Internet|208.76.44.0/22|Internet|
|mi-208-84-21-nexthop-Internet|208.84.0.0/21|Internet|
|mi-209-240-192-19-nexthop-Internet|209.240.192.0/19|Internet|
|mi-213-199-128-18-nexthop-Internet|213.199.128.0/18|Internet|
|mi-216-32-180-22-nexthop-Internet|216.32.180.0/22|Internet|
|mi-216-220-208-20-nexthop-Internet|216.220.208.0/20|Internet|
|mi-23-96-13-nexthop-Internet|23.96.0.0/13|Internet|
|mi-42-159-16-nexthop-Internet|42.159.0.0/16|Internet|
|mi-51-13-17-nexthop-Internet|51.13.0.0/17|Internet|
|mi-51-107-16-nexthop-Internet|51.107.0.0/16|Internet|
|mi-51-116-16-nexthop-Internet|51.116.0.0/16|Internet|
|mi-51-120-16-nexthop-Internet|51.120.0.0/16|Internet|
|mi-51-120-128-17-nexthop-Internet|51.120.128.0/17|Internet|
|mi-51-124-16-nexthop-Internet|51.124.0.0/16|Internet|
|mi-102-37-18-nexthop-Internet|102.37.0.0/18|Internet|
|mi-102-133-16-nexthop-Internet|102.133.0.0/16|Internet|
|mi-199-30-16-20-nexthop-Internet|199.30.16.0/20|Internet|
|mi-204-79-180-24-nexthop-Internet|204.79.180.0/24|Internet|
||||

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [co je Azure SQL Managed instance?](sql-managed-instance-paas-overview.md).
- Naučte se, jak [nastavit novou virtuální síť Azure](virtual-network-subnet-create-arm-template.md) nebo [existující virtuální síť Azure](vnet-existing-add-subnet.md) , kde můžete nasadit spravovanou instanci SQL.
- [Vypočítá velikost podsítě](vnet-subnet-determine-size.md) , do které chcete nasadit SPRAVOVANOU instanci SQL.
- Naučte se vytvořit spravovanou instanci:
  - Z [Azure Portal](instance-create-quickstart.md).
  - Pomocí [prostředí PowerShell](scripts/create-configure-managed-instance-powershell.md).
  - Pomocí [šablony Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Pomocí [šablony Azure Resource Manager (s použitím JumpBox, včetně SSMS)](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/).
