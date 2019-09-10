---
title: Architektura připojení pro spravovanou instanci v Azure SQL Database | Microsoft Docs
description: Přečtěte si o Azure SQL Database komunikaci spravované instance a architektuře připojení a také o tom, jak komponenty směrují provoz do spravované instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 04/16/2019
ms.openlocfilehash: d539bd569eee613eb43947e5fd0e3b0614ca5d79
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858619"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Architektura připojení pro spravovanou instanci v Azure SQL Database

Tento článek vysvětluje komunikaci ve spravované instanci Azure SQL Database. Také popisuje architekturu připojení a způsob, jakým součásti směrují provoz do spravované instance.  

SQL Database spravovaná instance se umístí do služby Azure Virtual Network a podsítě, která je vyhrazená pro spravované instance. Toto nasazení poskytuje:

- Zabezpečená privátní IP adresa.
- Možnost připojit místní síť ke spravované instanci.
- Možnost připojit spravovanou instanci k odkazovanému serveru nebo jinému místnímu úložišti dat.
- Možnost připojit spravovanou instanci k prostředkům Azure.

## <a name="communication-overview"></a>Přehled komunikace

Následující diagram znázorňuje entity, které se připojují ke spravované instanci. Zobrazuje také prostředky, které potřebují komunikovat se spravovanými instancemi. Proces komunikace v dolní části diagramu představuje aplikace zákazníka a nástroje, které se připojují ke spravované instanci jako zdroje dat.  

![Entity v architektuře připojení](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

Spravovaná instance je nabídka typu platforma jako služba (PaaS). Microsoft používá pro správu této služby automatizované agenty (správu, nasazení a údržbu) na základě datových proudů telemetrie. Vzhledem k tomu, že společnost Microsoft zodpovídá za správu, zákazníci nemají přístup k počítačům s virtuálním clusterem spravované instance prostřednictvím protokol RDP (Remote Desktop Protocol) (RDP).

Některé operace SQL Server spuštěné koncovými uživateli nebo aplikacemi můžou pro interakci s platformou vyžadovat spravované instance. Jediným případem je vytvoření databáze spravované instance. Tento prostředek se zveřejňuje prostřednictvím Azure Portal, PowerShellu, rozhraní příkazového řádku Azure a REST API.

Spravované instance závisí na službách Azure, jako jsou Azure Storage pro zálohování, Azure Event Hubs pro telemetrii, Azure Active Directory pro ověřování Azure Key Vault pro transparentní šifrování dat (TDE) a několik služeb platformy Azure, které poskytují funkce zabezpečení a podpory. Spravované instance vytváří připojení k těmto službám.

Veškerá komunikace je šifrovaná a podepsaná pomocí certifikátů. Pro kontrolu věrohodnosti komunikujících stran budou spravované instance průběžně ověřovat tyto certifikáty prostřednictvím seznamů odvolaných certifikátů. Pokud jsou certifikáty odvolány, spravovaná instance ukončí připojení k ochraně dat.

## <a name="high-level-connectivity-architecture"></a>Architektura připojení vysoké úrovně

V nejvyšší úrovni je spravovaná instance sadou součástí služby. Tyto komponenty se hostují na vyhrazené sadě izolovaných virtuálních počítačů, které běží v podsíti virtuální sítě zákazníka. Tyto počítače tvoří virtuální cluster.

Virtuální cluster může hostovat víc spravovaných instancí. V případě potřeby se cluster automaticky rozšíří nebo vymění smlouvy, když zákazník změní počet zřízených instancí v podsíti.

Zákaznické aplikace se můžou připojit ke spravovaným instancím a můžou zadávat dotazy a aktualizovat databáze uvnitř virtuální sítě, partnerské virtuální sítě nebo sítě připojené přes VPN nebo Azure ExpressRoute. Tato síť musí používat koncový bod a privátní IP adresu.  

![Diagram architektury připojení](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Služba Microsoft Management and Deployment Services se spouští mimo virtuální síť. Spravovaná instance a služby Microsoftu se připojují přes koncové body, které mají veřejné IP adresy. Když spravovaná instance vytvoří odchozí připojení, pak při přijetí koncového překladu adres sítě (NAT) bude připojení vypadat jako z této veřejné IP adresy.

Provoz správy prochází přes virtuální síť zákazníka. To znamená, že prvky infrastruktury virtuální sítě mohou poškodit provoz správy tím, že instance selže a nebude k dispozici.

> [!IMPORTANT]
> Pro zlepšení zkušeností zákazníků a dostupnosti služeb používá společnost Microsoft zásady záměru sítě na prvcích infrastruktury virtuální sítě Azure. Tato zásada může mít vliv na to, jak spravovaná instance funguje. Tento mechanismus platforem transparentně komunikuje s požadavky na síť uživatelům. Hlavním cílem této zásady je zabránit nesprávnému nastavení sítě a zajistit normální operace spravované instance. Při odstranění spravované instance se odstraní také zásada záměru sítě.

## <a name="virtual-cluster-connectivity-architecture"></a>Architektura připojení virtuálních clusterů

Pojďme se na architekturu připojení pro spravované instance pořizovat hlubší podrobně. Následující diagram znázorňuje koncepční rozložení virtuálního clusteru.

![Architektura připojení virtuálního clusteru](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Klienti se připojují ke spravované instanci pomocí názvu hostitele, který má formu `<mi_name>.<dns_zone>.database.windows.net`. Tento název hostitele se překládá na privátní IP adresu, i když je zaregistrovaný ve veřejné zóně DNS (Domain Name System) a je veřejně přeložitelný. Při vytváření clusteru se automaticky vygeneruje. `zone-id` Pokud je nově vytvořený cluster hostitelem sekundární spravované instance, sdílí své ID zóny s primárním clusterem. Další informace najdete v tématu [použití skupin automatického převzetí služeb při selhání k zajištění transparentního a koordinovaného převzetí služeb při selhání více databází](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets).

Tato privátní IP adresa patří do interního nástroje pro vyrovnávání zatížení spravované instance. Nástroj pro vyrovnávání zatížení směruje provoz do brány spravované instance. Vzhledem k tomu, že je možné spustit více spravovaných instancí v rámci stejného clusteru, brána používá název hostitele spravované instance pro přesměrování provozu do správné služby SQL Engine.

Služba správy a nasazení se připojí ke spravované instanci pomocí koncového [bodu správy](#management-endpoint) , který se mapuje na externí nástroj pro vyrovnávání zatížení. Provoz se směruje na uzly pouze v případě, že se obdrží na předdefinované sadě portů, které používají pouze součásti správy spravované instance. Integrovaná brána firewall na uzlech je nastavená tak, aby povolovala přenosy jenom z rozsahů IP adres Microsoftu. Certifikáty vzájemně ověřují veškerou komunikaci mezi součástmi pro správu a rovinou správy.

## <a name="management-endpoint"></a>Koncový bod správy

Microsoft spravuje spravovanou instanci pomocí koncového bodu správy. Tento koncový bod je uvnitř virtuálního clusteru instance. Koncový bod správy je chráněn integrovanou bránou firewall na úrovni sítě. Na úrovni aplikace je ochrana pomocí vzájemného ověření certifikátu. IP adresu koncového bodu najdete v tématu [určení IP adresy koncového bodu správy](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Když se připojení spouštějí v rámci spravované instance (stejně jako u protokolů zálohování a auditu), zobrazí se provoz z veřejné IP adresy koncového bodu správy. Přístup k veřejným službám ze spravované instance můžete omezit nastavením pravidel brány firewall tak, aby povolovala jenom IP adresu spravované instance. Další informace najdete v tématu [ověření integrované brány firewall spravované instance](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Provoz, který směřuje do služeb Azure, které jsou uvnitř oblasti spravované instance, je optimalizovaný a z tohoto důvodu se NATed do veřejné IP adresy koncového bodu správy spravované instance. Z tohoto důvodu, pokud potřebujete použít pravidla brány firewall založená na protokolu IP, je nejčastěji pro službu Storage v jiné oblasti než spravovaná instance.

## <a name="network-requirements"></a>Síťové požadavky

Nasaďte spravovanou instanci ve vyhrazené podsíti uvnitř virtuální sítě. Podsíť musí mít tyto vlastnosti:

- **Vyhrazená podsíť:** Podsíť spravované instance nemůže obsahovat žádnou jinou cloudovou službu, která je k ní přidružená, a nemůže to být podsíť brány. Podsíť nemůže obsahovat žádný prostředek, ale spravovanou instanci a nelze později přidat další typy prostředků v podsíti.
- **Skupina zabezpečení sítě (NSG):** NSG, která je přidružená k virtuální síti, musí definovat [příchozí pravidla zabezpečení](#mandatory-inbound-security-rules) a [odchozí pravidla zabezpečení](#mandatory-outbound-security-rules) před všemi ostatními pravidly. Pomocí NSG můžete řídit přístup ke koncovému bodu dat spravované instance pomocí filtrování provozu na portech 1433 a porty 11000-11999, pokud je spravovaná instance nakonfigurovaná pro připojení přesměrování.
- **Tabulka uživatelsky definované trasy (UDR):** Tabulka UDR, která je přidružená k virtuální síti, musí zahrnovat konkrétní [položky](#user-defined-routes).
- **Žádné koncové body služby:** K podsíti spravované instance by neměl být přidružen žádný koncový bod služby. Ujistěte se, že je při vytváření virtuální sítě možnost koncové body služby zakázaná.
- **Dostatečná IP adresa:** Podsíť spravované instance musí mít aspoň 16 IP adres. Doporučené minimum jsou 32 IP adresy. Další informace najdete v tématu [Určení velikosti podsítě pro spravované instance](sql-database-managed-instance-determine-size-vnet-subnet.md). Spravované instance můžete nasadit v [existující síti](sql-database-managed-instance-configure-vnet-subnet.md) poté, co ji nakonfigurujete tak, aby splňovala [požadavky na síť pro spravované instance](#network-requirements). V opačném případě vytvořte [novou síť a podsíť](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Nemůžete nasadit novou spravovanou instanci, pokud v cílové podsíti chybí tyto vlastnosti. Při vytváření spravované instance se v podsíti použije zásada záměru sítě, aby se zabránilo nekompatibilním změnám nastavení sítě. Po odebrání poslední instance z podsítě se odstraní také zásada záměru sítě.

### <a name="mandatory-inbound-security-rules"></a>Povinná příchozí pravidla zabezpečení

| Name       |Port                        |Protocol|Zdroj           |Cíl|Action|
|------------|----------------------------|--------|-----------------|-----------|------|
|management  |9000, 9003, 1438, 1440, 1452|TCP     |Any              |PODSÍŤ MI  |Allow |
|mi_subnet   |Any                         |Any     |PODSÍŤ MI        |PODSÍŤ MI  |Allow |
|health_probe|Any                         |Any     |AzureLoadBalancer|PODSÍŤ MI  |Allow |

### <a name="mandatory-outbound-security-rules"></a>Povinná odchozí pravidla zabezpečení

| Name       |Port          |Protocol|Zdroj           |Cíl|Action|
|------------|--------------|--------|-----------------|-----------|------|
|management  |80, 443, 12000|TCP     |PODSÍŤ MI        |AzureCloud |Allow |
|mi_subnet   |Any           |Any     |PODSÍŤ MI        |PODSÍŤ MI  |Allow |

> [!IMPORTANT]
> Zajistěte, aby existovalo pouze jedno příchozí pravidlo pro porty 9000, 9003, 1438, 1440, 1452 a jedno odchozí pravidlo pro porty 80, 443, 12000. Zřizování spravovaných instancí prostřednictvím Azure Resource Manager nasazení se nezdaří, pokud jsou příchozí a odchozí pravidla konfigurovaná samostatně pro každý port. Pokud jsou tyto porty v samostatných pravidlech, nasazení se nezdaří s kódem chyby.`VnetSubnetConflictWithIntendedPolicy`

\*PODSÍŤ MI odkazuje na rozsah IP adres podsítě ve formátu 10. x. x. x. x/y. Tyto informace můžete najít v Azure Portal ve vlastnostech podsítě.

> [!IMPORTANT]
> I když požadovaná příchozí pravidla zabezpečení umožňují provoz z _libovolného_ zdroje na portech 9000, 9003, 1438, 1440 a 1452, jsou tyto porty chráněny integrovanou bránou firewall. Další informace najdete v tématu [určení adresy koncového bodu správy](sql-database-managed-instance-find-management-endpoint-ip-address.md).
> [!NOTE]
> Pokud ve spravované instanci použijete transakční replikaci a jako vydavatele nebo distributora použijete jakoukoli databázi instance, otevřete port 445 (odchozí TCP) v pravidlech zabezpečení podsítě. Tento port umožní přístup ke sdílené složce Azure.

### <a name="user-defined-routes"></a>Trasy definované uživatelem

|Name|Předpona adresy|Další segment směrování|
|----|--------------|-------|
|subnet_to_vnetlocal|PODSÍŤ MI|Virtuální síť|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-96-13-nexthop-internet|13.96.0.0/13|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|mi-20-8-nexthop-internet|20.0.0.0/8|Internet|
|mi-23-96-13-nexthop-internet|23.96.0.0/13|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|Internet|
|mi-51-8-nexthop-internet|51.0.0.0/8|Internet|
|mi-52-8-nexthop-internet|52.0.0.0/8|Internet|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|Internet|
|mi-103-25-156-22-nexthop-internet|103.25.156.0/22|Internet|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|Internet|
|mi-131-253-16-nexthop-internet|131.253.0.0/16|Internet|
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
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
|mi-192-100-102-24-nexthop-internet|192.100.102.0/24|Internet|
|mi-192-100-103-24-nexthop-internet|192.100.103.0/24|Internet|
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
||||

Kromě toho můžete přidat položky do směrovací tabulky pro směrování provozu, který má místní rozsahy privátních IP adres jako cíl prostřednictvím brány virtuální sítě nebo zařízení virtuální sítě (síťové virtuální zařízení).

Pokud virtuální síť obsahuje vlastní DNS, vlastní server DNS musí být schopný přeložit veřejné záznamy DNS. Použití dalších funkcí, jako je ověřování Azure AD, může vyžadovat vyřešení dalších plně kvalifikovaných názvů domén. Další informace najdete v tématu [nastavení vlastního DNS](sql-database-managed-instance-custom-dns.md).

## <a name="next-steps"></a>Další kroky

- Přehled najdete v tématu [SQL Database pokročilé zabezpečení dat](sql-database-managed-instance.md).
- Přečtěte si, jak [nastavit novou virtuální síť Azure](sql-database-managed-instance-create-vnet-subnet.md) nebo [existující virtuální síť Azure](sql-database-managed-instance-configure-vnet-subnet.md) , kde můžete nasadit spravované instance.
- [Vypočítá velikost podsítě](sql-database-managed-instance-determine-size-vnet-subnet.md) , do které chcete nasadit spravované instance.
- Naučte se vytvořit spravovanou instanci:
  - Z [Azure Portal](sql-database-managed-instance-get-started.md).
  - Pomocí [prostředí PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md).
  - Pomocí [šablony Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Pomocí [šablony Azure Resource Manager (s použitím JumpBox, včetně SSMS)](https://azure.microsoft.com/en-us/resources/templates/201-sqlmi-new-vnet-w-jumpbox/). 
