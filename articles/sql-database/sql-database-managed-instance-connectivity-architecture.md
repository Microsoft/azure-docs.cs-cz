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
ms.date: 02/26/2019
ms.openlocfilehash: 801294241f399097d363dd8dc2682f158c0bf2cc
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2019
ms.locfileid: "59358278"
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

Spravované instance závisí na služby Azure, například Azure Storage pro zálohování, telemetrických dat služby Azure Service Bus, Azure Active Directory pro ověřování a služby Azure Key Vault pro transparentní šifrování dat (TDE). Spravované instance vytvořit připojení k těmto službám.

Veškerá komunikace používat certifikáty pro šifrování a podepisování. Zkontrolujte důvěryhodnost komunikaci strany, spravované instance neustále ověříte, jestli tyto certifikáty kontaktováním certifikační autority. Pokud certifikáty byly odvolány, nebo nelze ověřit, spravované instance zavře připojení k ochraně dat.

## <a name="high-level-connectivity-architecture"></a>Architektura vysoké úrovně připojení

Na vysoké úrovni managed instance je sada součástí služby. Tyto součásti jsou hostované na vyhrazenou sadu izolované virtuální počítače, na kterých běží v podsíti virtuální sítě zákazníka. Tyto počítače tvoří virtuální cluster.

Virtuální cluster může být hostitelem více spravovaných instancí. V případě potřeby clusteru automaticky rozbalí nebo smluv týkajících se při změně zákazníka číslo zřízené instance v podsíti.

Zákaznické aplikace do spravované instance se můžete připojit a můžete zadávat dotazy a aktualizaci databáze pouze v případě, že spuštění uvnitř virtuální sítě v partnerském vztahu virtuální sítě nebo síť připojenou pomocí Azure ExpressRoute nebo VPN. Tato síť musíte použít koncový bod a privátní IP adresu.  

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
> Na rozdíl od brány firewall pro připojení, které začínají ve spravované instanci služby Azure, které jsou uvnitř oblasti spravovanou instanci mít bránu firewall, která je optimalizovaná pro provoz, který prochází mezi těmito službami.

## <a name="network-requirements"></a>Síťové požadavky

Nasazení spravované instance ve vyhrazené podsíti ve virtuální síti. Podsíť musí mít tyto charakteristiky:

- **Vyhrazenou podsíť:** Podsíť spravované instance nesmí obsahovat kteroukoli cloudovou službu, která k němu má přidružené, a nemůže být podsíť brány. Podsítě nesmí obsahovat všechny prostředky, ale spravované instance a nemůžete později přidat prostředky v podsíti.
- **Skupina zabezpečení sítě (NSG):** Skupina zabezpečení sítě, který je spojen s virtuální sítí musí definovat [příchozí pravidla zabezpečení](#mandatory-inbound-security-rules) a [odchozí pravidla zabezpečení](#mandatory-outbound-security-rules) před všechna pravidla. Skupina zabezpečení sítě můžete řídit přístup ke koncovému bodu data spravované instance pomocí filtrování provozu na portu 1433.
- **Uživatelská tabulka definovanou trasou (UDR):** Tabulka směrování definovaného uživatelem, který je spojen s virtuální sítí musí obsahovat konkrétní [položky](#user-defined-routes).
- **Žádné koncové body služby:** Žádný koncový bod služby by měly být přidruženy s podsítí spravované instance. Ujistěte se, že při vytváření virtuální sítě je zakázána možnost koncových bodů služby.
- **Dostatek IP adres:** Spravovaná instance podsíť musí mít aspoň 16 IP adres. Doporučená minimální hodnota je 32 IP adresy. Další informace najdete v tématu [určit velikost podsítě pro spravované instance](sql-database-managed-instance-determine-size-vnet-subnet.md). Můžete nasadit spravované instance v [existující síť](sql-database-managed-instance-configure-vnet-subnet.md) po dokončení konfigurace splňovat [síťové požadavky pro spravované instance](#network-requirements). V opačném případě vytvořte [novou síť a podsíť](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Pokud cílové podsíti chybí tyto vlastnosti se nedají nasadit nové spravované instance. Když vytvoříte managed instance, použije se záměru zásad sítě pro podsíť, kterou chcete zabránit změnám nekompatibilní síťové instalace. Po poslední instance je odebrána z podsítě, záměru zásad sítě se také odebere.

### <a name="mandatory-inbound-security-rules"></a>Pravidla povinné zabezpečení příchozích dat

| Název       |Port                        |Protocol (Protokol)|Zdroj           |Cíl|Akce|
|------------|----------------------------|--------|-----------------|-----------|------|
|Správa  |9000, 9003, 1438, 1440, 1452|TCP     |Všechny              |Všechny        |Povolit |
|mi_subnet   |Všechny                         |Všechny     |MI SUBNET        |Všechny        |Povolit |
|health_probe|Všechny                         |Všechny     |AzureLoadBalancer|Všechny        |Povolit |

### <a name="mandatory-outbound-security-rules"></a>Povinné odchozí pravidla zabezpečení

| Název       |Port          |Protocol (Protokol)|Zdroj           |Cíl|Akce|
|------------|--------------|--------|-----------------|-----------|------|
|Správa  |80, 443, 12000|TCP     |Všechny              |AzureCloud  |Povolit |
|mi_subnet   |Všechny           |Všechny     |Všechny              |MI PODSÍTĚ *  |Povolit |

> [!IMPORTANT]
> Zajistěte existovala jenom jedno příchozí pravidlo pro porty 9000 9003, 1438, 1440, 1452 a jeden odchozí pravidlo pro port 80, 443, 12000. Spravovaná Instance zřizování prostřednictvím nasazení ARM se nezdaří, pokud vstupní a výstupní pravidla jsou nakonfigurované samostatně pro každý z portů. Pokud jsou tyto porty v pravidlech samostatné, nasazení selže s kódem chyby `VnetSubnetConflictWithIntendedPolicy`

\* PODSÍŤ MI odkazuje na rozsah IP adres podsítě v 10.x.x.x/y formuláře. Tyto informace můžete najít na webu Azure Portal, v okně Vlastnosti podsítě.

> [!IMPORTANT]
> I když požadované zabezpečení příchozích pravidel povolit provoz z _jakékoli_ zdroje na portech 9000, 9003, 1438, 1440 a 1452, tyto porty jsou chráněné bránou firewall integrované. Další informace najdete v tématu [určit adresu koncového bodu správy](sql-database-managed-instance-find-management-endpoint-ip-address.md).
> [!NOTE]
> Použití transakční replikace v managed instance a použít libovolnou databázi instance jako vydavatel nebo distributora, otevřete port 445 (odchozí TCP) v pravidlech zabezpečení podsítě. Tento port vám umožní přístup ke sdílené složce Azure.

### <a name="user-defined-routes"></a>Trasy definované uživatelem

|Název|Předpona adresy|Další směrování|
|----|--------------|-------|
|subnet_to_vnetlocal|[mi_subnet]|Virtuální síť|
|mi-0-5-next-hop-internet|0.0.0.0/5|Internet|
|mi-11-8-nexthop-internet|11.0.0.0/8|Internet|
|mi-12-6-nexthop-internet|12.0.0.0/6|Internet|
|mi-128-3-nexthop-internet|128.0.0.0/3|Internet|
|mi-16-4-nexthop-internet|16.0.0.0/4|Internet|
|mi-160-5-nexthop-internet|160.0.0.0/5|Internet|
|mi-168-6-nexthop-internet|168.0.0.0/6|Internet|
|mi-172-12-nexthop-internet|172.0.0.0/12|Internet|
|mi-172-128-9-nexthop-internet|172.128.0.0/9|Internet|
|mi-172-32-11-nexthop-internet|172.32.0.0/11|Internet|
|mi-172-64-10-nexthop-internet|172.64.0.0/10|Internet|
|mi-173-8-nexthop-internet|173.0.0.0/8|Internet|
|mi-174-7-nexthop-internet|174.0.0.0/7|Internet|
|mi-176-4-nexthop-internet|176.0.0.0/4|Internet|
|mi-192-128-11-nexthop-internet|192.128.0.0/11|Internet|
|mi-192-160-13-nexthop-internet|192.160.0.0/13|Internet|
|mi-192-169-16-nexthop-internet|192.169.0.0/16|Internet|
|mi-192-170-15-nexthop-internet|192.170.0.0/15|Internet|
|mi-192-172-14-nexthop-internet|192.172.0.0/14|Internet|
|mi-192-176-12-nexthop-internet|192.176.0.0/12|Internet|
|mi-192-192-10-nexthop-internet|192.192.0.0/10|Internet|
|mi-192-9-nexthop-internet|192.0.0.0/9|Internet|
|mi-193-8-nexthop-internet|193.0.0.0/8|Internet|
|mi-194-7-nexthop-internet|194.0.0.0/7|Internet|
|mi-196-6-nexthop-internet|196.0.0.0/6|Internet|
|mi-200-5-nexthop-internet|200.0.0.0/5|Internet|
|mi-208-4-nexthop-internet|208.0.0.0/4|Internet|
|mi-224-3-nexthop-internet|224.0.0.0/3|Internet|
|mi-32-3-nexthop-internet|32.0.0.0/3|Internet|
|mi-64-2-nexthop-internet|64.0.0.0/2|Internet|
|mi-8-7-nexthop-internet|8.0.0.0/7|Internet|
||||

Kromě toho můžete přidat položky do směrovací tabulky pro směrování provozu, který má místní rozsahy privátních IP jako cíl přes bránu virtuální sítě nebo virtuální síťové zařízení (NVA).

Pokud virtuální síť obsahuje vlastní DNS, přidejte záznam pro Azure rekurzivní překladač IP adresu (například adresy 168.63.129.16). Další informace najdete v tématu [nastavit vlastní službu DNS](sql-database-managed-instance-custom-dns.md). Vlastní server DNS musí být schopen překladu názvů hostitelů na tyto domény a jejich subdomény: *microsoft.com*, *windows.net*, *windows.com*,  *msocsp.com*, *digicert.com*, *live.com*, *microsoftonline.com*, a *microsoftonline-p.com*.

## <a name="next-steps"></a>Další postup

- Přehled najdete v tématu [rozšířené zabezpečení dat ve službě SQL Database](sql-database-managed-instance.md).
- Zjistěte, jak [nastavit novou virtuální síť Azure](sql-database-managed-instance-create-vnet-subnet.md) nebo [existující virtuální sítě Azure](sql-database-managed-instance-configure-vnet-subnet.md) kde můžete nasadit spravované instance.
- [Vypočítat velikost podsítě](sql-database-managed-instance-determine-size-vnet-subnet.md) ve které chcete nasadit spravované instance.
- Zjistěte, jak vytvořit spravovanou instanci:
  - Z [webu Azure portal](sql-database-managed-instance-get-started.md).
  - S použitím [Powershellu](scripts/sql-database-create-configure-managed-instance-powershell.md).
  - S použitím [šablony Azure Resource Manageru](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - S použitím [šablonu Azure Resource Manageru (s použitím Jumpboxu, pomocí aplikace SSMS zahrnuté)](https://portal.azure.com/).
