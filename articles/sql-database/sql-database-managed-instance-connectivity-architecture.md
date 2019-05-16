---
title: Architektura připojení pro spravovanou instanci Azure SQL Database | Dokumentace Microsoftu
description: Další informace o Azure SQL Database managed instance komunikace a architektura připojení a jak součásti směrovat provoz do spravované instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
manager: craigg
ms.date: 04/16/2019
ms.openlocfilehash: dbb5ee122e715aeaa66d786f02966beedd2447c3
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522322"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Architektura připojení pro spravovanou instanci Azure SQL Database

Tento článek vysvětluje, komunikace ve spravované instanci Azure SQL Database. Také popisuje architekturu připojení a jak součásti směrovat provoz do spravované instance.  

Spravované instance SQL Database je umístěn uvnitř virtuální sítě Azure a podsíť, která je vyhrazen pro spravované instance. Toto nasazení poskytuje:

- Zabezpečené privátní IP adresu.
- Možnost připojení místní sítě k managed instance.
- Možnost připojení managed instance pro odkazovaný server nebo jiné v místním úložišti.
- Možnost připojení k prostředkům Azure managed instance.

## <a name="communication-overview"></a>Přehled komunikace

Následující diagram znázorňuje entity, které se připojují k managed instance. Také ukazuje prostředky, které potřebují komunikovat s spravované instance. Proces komunikace v dolní části diagramu představuje zákaznických aplikací a nástrojů, které jako zdroj dat připojit do spravované instance.  

![Entity v architektura připojení](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

Managed instance je platforma jako služba (PaaS). Společnost Microsoft používá automatizované agents (Správa, nasazení a údržbě) ke správě této služby založené na datových proudů telemetrie. Protože společnost Microsoft zodpovídá za správu, zákazníci nemají přístup k clusteru virtuální počítače spravované instance prostřednictvím protokolu RDP (Remote Desktop).

Některé systému SQL Server, které mohou vyžadovat operace tím, že koncoví uživatelé nebo aplikace spravované instance pro interakci s platformou. Jeden případ je vytvoření databáze spravované instance. Tento prostředek je přístupný prostřednictvím webu Azure portal, Powershellu, rozhraní příkazového řádku Azure a rozhraní REST API.

Spravované instance závisí na služby Azure, například Azure Storage pro zálohování, Azure Event Hubs pro telemetrii, pro ověřování Azure Active Directory, Azure Key Vault pro transparentní šifrování dat (TDE) a několik služeb platformy Azure, které poskytují funkce zabezpečení a možnosti podpory. Spravované instance díky připojení k těmto službám.

Veškerá komunikace se zašifrovaná a podepsaná certifikáty. Zkontrolujte důvěryhodnost komunikaci strany, spravované instance neustále ověříte, jestli tyto certifikáty prostřednictvím seznamů odvolaných certifikátů. Pokud jsou odvolané certifikáty, spravované instance zavře připojení k ochraně dat.

## <a name="high-level-connectivity-architecture"></a>Architektura vysoké úrovně připojení

Na vysoké úrovni managed instance je sada součástí služby. Tyto součásti jsou hostované na vyhrazenou sadu izolované virtuální počítače, na kterých běží v podsíti virtuální sítě zákazníka. Tyto počítače tvoří virtuální cluster.

Virtuální cluster může být hostitelem více spravovaných instancí. V případě potřeby clusteru automaticky rozbalí nebo smluv týkajících se při změně zákazníka číslo zřízené instance v podsíti.

Zákaznické aplikace může připojit ke spravované instance a můžete dotazování a aktualizace databází ve virtuální síti, partnerské virtuální síti, nebo síť připojenou pomocí Azure ExpressRoute nebo VPN. Tato síť musíte použít koncový bod a privátní IP adresu.  

![diagram architektury připojení](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Spuštění služby pro nasazení a správu mimo virtuální síť. Managed instance a služeb poskytovaných Microsoftem připojit prostřednictvím koncových bodů, které mají veřejné IP adresy. Když do spravované instance vytvoří odchozí připojení, na přijímající straně překladu adres (NAT) usnadňuje připojení vypadají ho pochází z této veřejné IP adresy.

Provoz správy, prochází přes virtuální síti zákazníka. To znamená, že prvky infrastruktury virtuální sítě může poškodit přenos pro správu tím, že instance nezdaří a není dostupná.

> [!IMPORTANT]
> K vylepšení zkušeností uživatelů a dostupnost služeb, Microsoft uplatní záměru zásady sítě na prvky infrastruktury virtuální sítě Azure. Zásady můžou ovlivnit fungování spravované instance. Tento mechanismus platformy transparentně komunikuje se požadavky na síť pro uživatele. Zásady hlavním cílem je zabránit chybného síťového nastavení a zajistit provoz normální spravované instance. Při odstranění spravované instance záměru zásad sítě se také odebere.

## <a name="virtual-cluster-connectivity-architecture"></a>Virtuální cluster architektura připojení

Pojďme se dozvědět více o do připojení architektury pro spravované instance. Následující diagram znázorňuje koncepční rozložení virtuálních clusterů.

![Architektura připojení z virtuální cluster](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Klienti připojit k managed instance pomocí názvu hostitele, který má tvar `<mi_name>.<dns_zone>.database.windows.net`. Tento název hostitele se přeloží na privátní IP adresu, i když je zaregistrovaný v zóně Name System (DNS) veřejné domény a je veřejně přeložitelného. `zone-id` Není automaticky vygenerován při vytváření clusteru. Pokud je to nově vytvořený cluster hostitelem sekundární managed instance, sdílí jeho ID zóny s primární clusteru. Další informace najdete v tématu [povolit transparentní a koordinovaný převzetí služeb při selhání několika databází pomocí skupin automatického převzetí služeb při selhání](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets).

Tato privátní IP adresa patří do spravované instance interního nástroje load balancer. Nástroje pro vyrovnávání zatížení bude směrovat provoz do spravované instance brány. Protože více spravovaných instancí lze spustit ve stejném clusteru, brána používá název hostitele spravované instance pro přesměrování přenosu dat do služby správný modul SQL.

Nasazení a správu služeb připojení k managed instance pomocí [koncový bod správy](#management-endpoint) , že mapuje externí nástroj pro vyrovnávání zatížení. Provoz se směruje do uzlů pouze v případě, že je přijatá v předdefinovanou sadu porty, které používají pouze spravované instance komponentami pro správu. Integrované brány firewall na uzlech je nastavit pro povolení provozu pouze z oblastí Microsoft IP. Certifikáty mezi sebou vzájemně ověřovat veškerá komunikace mezi komponentami pro správu a rovině správy.

## <a name="management-endpoint"></a>Koncový bod správy

Microsoft spravuje spravované instance pomocí koncového bodu správy. Tento koncový bod nachází uvnitř virtuální instance clusteru. Koncový bod správy je chráněn integrované brány firewall na úrovni sítě. Na úrovni aplikace je chráněn certifikát vzájemné ověření. IP adresa koncového bodu, najdete v tématu [zjistit koncový bod správy IP adres](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Při připojení spustit uvnitř spravované instance (stejně jako u zálohy a protokoly auditu), provoz se zobrazí spuštění z veřejné IP adresy koncového bodu správy. Můžete omezit přístup k veřejné služby ze spravované instance pomocí nastavení pravidla brány firewall pro povolení pouze spravované instance IP adres. Další informace najdete v tématu [ověření integrované firewall spravovanou instanci](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Provoz, který přejde do služby Azure, které jsou uvnitř oblasti spravované instance je optimalizované a z tohoto důvodu není NATed do spravované instance koncový bod veřejné IP adresa pro správu. Z tohoto důvodu potřebujete používat pravidla brány firewall na základě IP adresy, nejčastěji pro úložiště, služba musí být v jiné oblasti než spravovaná instance.

## <a name="network-requirements"></a>Síťové požadavky

Nasazení spravované instance ve vyhrazené podsíti ve virtuální síti. Podsíť musí mít tyto charakteristiky:

- **Vyhrazenou podsíť:** Podsíť spravované instance nesmí obsahovat kteroukoli cloudovou službu, která k němu má přidružené, a nemůže být podsíť brány. Podsítě nesmí obsahovat všechny prostředky, ale spravované instance a dalších typů prostředků nelze přidat později v podsíti.
- **Skupina zabezpečení sítě (NSG):** Skupina zabezpečení sítě, který je spojen s virtuální sítí musí definovat [příchozí pravidla zabezpečení](#mandatory-inbound-security-rules) a [odchozí pravidla zabezpečení](#mandatory-outbound-security-rules) před všechna pravidla. Skupina zabezpečení sítě můžete použít k řízení přístupu ke koncovému bodu data spravované instance pomocí filtrování provozu na portu 1433 a porty 11000 11999 Pokud spravované instance je nakonfigurovaná pro přesměrování připojení.
- **Uživatelská tabulka definovanou trasou (UDR):** Tabulka směrování definovaného uživatelem, který je spojen s virtuální sítí musí obsahovat konkrétní [položky](#user-defined-routes).
- **Žádné koncové body služby:** Žádný koncový bod služby by měly být přidruženy s podsítí spravované instance. Ujistěte se, že při vytváření virtuální sítě je zakázána možnost koncových bodů služby.
- **Dostatek IP adres:** Spravovaná instance podsíť musí mít aspoň 16 IP adres. Doporučená minimální hodnota je 32 IP adresy. Další informace najdete v tématu [určit velikost podsítě pro spravované instance](sql-database-managed-instance-determine-size-vnet-subnet.md). Můžete nasadit spravované instance v [existující síť](sql-database-managed-instance-configure-vnet-subnet.md) po dokončení konfigurace splňovat [síťové požadavky pro spravované instance](#network-requirements). V opačném případě vytvořte [novou síť a podsíť](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Pokud cílové podsíti chybí tyto vlastnosti se nedají nasadit nové spravované instance. Když vytvoříte managed instance, použije se záměru zásad sítě pro podsíť, kterou chcete zabránit změnám nekompatibilní síťové instalace. Po poslední instance je odebrána z podsítě, záměru zásad sítě se také odebere.

### <a name="mandatory-inbound-security-rules"></a>Pravidla povinné zabezpečení příchozích dat

| Název       |Port                        |Protocol|Zdroj           |Cíl|Akce|
|------------|----------------------------|--------|-----------------|-----------|------|
|Správa  |9000, 9003, 1438, 1440, 1452|TCP     |Vše              |MI SUBNET  |Povolit |
|mi_subnet   |Vše                         |Vše     |MI SUBNET        |MI SUBNET  |Povolit |
|health_probe|Vše                         |Vše     |AzureLoadBalancer|MI SUBNET  |Povolit |

### <a name="mandatory-outbound-security-rules"></a>Povinné odchozí pravidla zabezpečení

| Název       |Port          |Protocol|Zdroj           |Cíl|Akce|
|------------|--------------|--------|-----------------|-----------|------|
|Správa  |80, 443, 12000|TCP     |MI SUBNET        |AzureCloud |Povolit |
|mi_subnet   |Vše           |Vše     |MI SUBNET        |MI SUBNET  |Povolit |

> [!IMPORTANT]
> Zajistěte existovala jenom jedno příchozí pravidlo pro porty 9000 9003, 1438, 1440, 1452 a jeden odchozí pravidlo pro port 80, 443, 12000. Managed Instance zřizování prostřednictvím nasazení se nezdaří, pokud jsou pravidla pro příchozí a odchozí nakonfigurovat jednotlivě pro každý z portů programem Azure Resource Manageru. Pokud jsou tyto porty v pravidlech samostatné, nasazení selže s kódem chyby `VnetSubnetConflictWithIntendedPolicy`

\* PODSÍŤ MI odkazuje na rozsah IP adres podsítě v 10.x.x.x/y formuláře. Tyto informace můžete najít na webu Azure Portal, v okně Vlastnosti podsítě.

> [!IMPORTANT]
> I když požadované zabezpečení příchozích pravidel povolit provoz z _jakékoli_ zdroje na portech 9000, 9003, 1438, 1440 a 1452, tyto porty jsou chráněné bránou firewall integrované. Další informace najdete v tématu [určit adresu koncového bodu správy](sql-database-managed-instance-find-management-endpoint-ip-address.md).
> [!NOTE]
> Použití transakční replikace v managed instance a použít libovolnou databázi instance jako vydavatel nebo distributora, otevřete port 445 (odchozí TCP) v pravidlech zabezpečení podsítě. Tento port vám umožní přístup ke sdílené složce Azure.

### <a name="user-defined-routes"></a>Trasy definované uživatelem

|Název|Předpona adresy|Další směrování|
|----|--------------|-------|
|subnet_to_vnetlocal|MI SUBNET|Virtuální síť|
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

Kromě toho můžete přidat položky do směrovací tabulky pro směrování provozu, který má místní rozsahy privátních IP jako cíl přes bránu virtuální sítě nebo virtuální síťové zařízení (NVA).

Pokud virtuální síť vlastní DNS, vlastní server DNS musí být schopen překladu názvu hostitele v \*. core.windows.net zóny. Pomocí další funkce, jako je ověřování Azure AD můžou vyžadovat vyřešení dalších plně kvalifikovaných názvů domény. Další informace najdete v tématu [nastavit vlastní službu DNS](sql-database-managed-instance-custom-dns.md).

## <a name="next-steps"></a>Další postup

- Přehled najdete v tématu [rozšířené zabezpečení dat ve službě SQL Database](sql-database-managed-instance.md).
- Zjistěte, jak [nastavit novou virtuální síť Azure](sql-database-managed-instance-create-vnet-subnet.md) nebo [existující virtuální sítě Azure](sql-database-managed-instance-configure-vnet-subnet.md) kde můžete nasadit spravované instance.
- [Vypočítat velikost podsítě](sql-database-managed-instance-determine-size-vnet-subnet.md) ve které chcete nasadit spravované instance.
- Zjistěte, jak vytvořit spravovanou instanci:
  - Z [webu Azure portal](sql-database-managed-instance-get-started.md).
  - S použitím [Powershellu](scripts/sql-database-create-configure-managed-instance-powershell.md).
  - S použitím [šablony Azure Resource Manageru](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - S použitím [šablonu Azure Resource Manageru (s použitím Jumpboxu, pomocí aplikace SSMS zahrnuté)](https://portal.azure.com/). 
