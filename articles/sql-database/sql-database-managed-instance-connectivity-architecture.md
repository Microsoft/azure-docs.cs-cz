---
title: Azure SQL Database managed instance připojení architektury | Dokumentace Microsoftu
description: Tento článek poskytuje Azure SQL Database managed instance komunikace přehled a architektura připojení také vysvětluje, jak různé součásti fungovat pro směrování provozu do spravované instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 02/18/2019
ms.openlocfilehash: 70206fac7bfe34ea1a138437db35720a1f02337d
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2019
ms.locfileid: "56585364"
---
# <a name="azure-sql-database-managed-instance-connectivity-architecture"></a>Architektura připojení k Azure SQL Database managed instance

Tento článek poskytuje Azure SQL Database managed instance komunikace přehled a architektura připojení také vysvětluje, jak různé součásti fungovat pro směrování provozu do spravované instance.  

Spravované instance Azure SQL Database je umístěn uvnitř virtuální sítě Azure a podsíť vyhrazený pro spravované instance. Toto nasazení umožňuje následující scénáře:

- Zabezpečené privátní IP adresu.
- Připojení k managed instance přímo z místní sítě.
- Připojení managed instance pro odkazovaný server nebo jiné v místním úložišti.
- Připojení k prostředkům Azure managed instance.

## <a name="communication-overview"></a>Přehled komunikace

Následující diagram znázorňuje entity, které se připojují na spravované instanci, jakož i prostředky, které managed instance má k oslovení mohl správně fungovat.

![připojení k architektuře entity](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

Komunikaci, která je znázorněn v dolní části diagramu představuje zákazníka aplikacím a nástrojům připojení do spravované instance jako zdroj dat.  

Jak managed instance je na platformu jako služby (PaaS nabídky), Microsoft spravuje tato služba pomocí automatizovaných agents (Správa, nasazení a údržbě) založené na datových proudů telemetrie. Jako spravované instance správu zodpovídá za výhradně od Microsoftu, zákazníci se nemají mít přístup k clusteru virtuální počítače spravované instance pomocí protokolu RDP.

Některé systému SQL Server, které mohou vyžadovat operací iniciovaných koncoví uživatelé nebo aplikace spravované instance pro interakci s platformou. Jeden případ je vytvoření databáze spravované instance - prostředek, který je k dispozici prostřednictvím webu Azure portal, Powershellu, rozhraní příkazového řádku Azure a rozhraní REST API.

Spravovaná instance závisí na jiné služby Azure pro správné fungování (jako je například Azure Storage pro zálohování, telemetrických dat služby Azure Service Bus, Azure AD pro ověřování služby Azure Key Vault pro transparentní šifrování dat a tak dále) a zahájí připojení k nim odpovídajícím způsobem.

Veškerá komunikace, uvedené výše, jsou zašifrovaná a podepsaná certifikáty. Pokud chcete mít jistotu, že jsou důvěryhodná komunikujících stran, spravované instance neustále ověří tyto certifikáty kontaktováním certifikační autority. Pokud jsou odvolané certifikáty nebo spravované instance nelze ověřit jejich, uzavře připojení k ochraně dat.

## <a name="high-level-connectivity-architecture"></a>Architektura vysoké úrovně připojení

Na vysoké úrovni managed instance je sada součástí služby hostované na vyhrazenou sadu izolované virtuální počítače, které jsou spouštěny v rámci podsítě virtuální sítě zákazníků a vytvoří virtuální cluster.

Do jediného virtuálního clusteru je možné hostovat několik spravovaných instancí. Cluster automaticky rozbalená nebo v případě potřeby při změně zákazníka číslo zřízené instance v podsíti na zakázku.

Zákaznické aplikace by se mohl připojit do spravované instance, dotazování a aktualizaci databáze pouze v případě, že spuštěny ve virtuální síti nebo v partnerském vztahu virtuálních sítí nebo VPN / Express Route propojená síť pomocí koncového bodu se privátní IP adresou.  

![diagram architektury připojení](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Nasazení a správu služby běží mimo virtuální síť, takže připojení mezi managed instance a služeb poskytovaných Microsoftem prochází přes koncové body s veřejnými IP adresami. Když mi vytvoří odchozí připojení, při přijetí koncovým pravděpodobně pochází z této veřejné IP adresy z důvodu překlad síťových adres (NAT).

Provoz správy, prochází přes virtuální síti zákazníka. To znamená, že ovlivňují prvky infrastruktury virtuální sítě a by mohlo potenciálně provoz správy poškození příčinou instance do chybného stavu a přestanou být dostupné.

> [!IMPORTANT]
> K vylepšení zkušeností uživatelů a dostupnost služeb, Microsoft využívá zásady sítě záměr na prvky infrastruktury virtuální síť Azure, které by mohly ovlivnit fungování spravované instance. Jedná se o platformu mechanismus pro komunikaci transparentně síťové požadavky na koncové uživatele, s hlavním cílem zabránit chybného síťového nastavení a zajistit provoz normální spravované instance. Při odstranění spravované instance bude odebrán také záměr zásady sítě.

## <a name="virtual-cluster-connectivity-architecture"></a>Virtuální cluster architektura připojení

Pojďme se dozvědět více o v architektuře připojení spravované instance. Následující diagram znázorňuje koncepční rozložení virtuálních clusterů.

![připojení k architektuře diagram virtuální cluster](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Klienti připojovat k spravované instance pomocí názvu hostitele, který obsahuje formulář `<mi_name>.<dns_zone>.database.windows.net`. Tento název hostitele se přeloží na privátní IP adresu, i když je zaregistrovaný ve veřejné zóny DNS a je veřejně přeložitelného. `zone-id` Se automaticky vygeneruje, když se cluster vytvoří. Pokud nově vytvořený cluster je hostitelem sekundární managed instance, sdílí jeho ID zóny s primární clusteru. Další informace najdete v tématu [-automatické převzetí služeb při selhání skupiny](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets)

Tato privátní IP adresa patří spravované instance interní nástroj pro vyrovnávání zatížení (ILB), která směruje provoz do spravované instance brány (gs). Několik spravovaných instancí může potenciálně spuštění ve stejném clusteru GW používá název hostitele spravované instance pro přesměrování přenosu dat na správné služby stroj SQL.

Připojení správy a nasazení služby do spravované instance pomocí [koncový bod správy](#management-endpoint) , že mapuje externí nástroj pro vyrovnávání zatížení. Provoz se směruje do uzlů pouze v případě, že byla přijata na předdefinovanou sadu porty, které používá jediná komponentami pro správu spravované instance. Integrované brána firewall na uzlech je nakonfigurována pro povolení provozu pouze z konkrétní rozsahy IP Microsoft. Veškerá komunikace mezi komponentami správy a rovina správy se vzájemně certifikát ověřit.

## <a name="management-endpoint"></a>Koncový bod správy

Virtuální cluster spravované instance obsahuje koncový bod správy, který Microsoft používá ke správě spravované instance. Koncový bod správy je chráněný pomocí předdefinovaných brány firewall sítě certifikát úrovně a proces vzájemného ověření na úrovni aplikace. Je možné [najít ip adresu koncového bodu správy](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Při připojení inicializována z uvnitř spravované instance (zálohování protokolu auditu), zobrazí se, že provoz pochází z veřejnou IP adresu koncového bodu správy. Může omezit přístup k veřejné služby ze spravované instance pomocí nastavení pravidla brány firewall pro povolení IP adres spravované instance pouze. Další informace o metodě, která se dá [ověření integrované firewall spravovanou instanci](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> To neplatí pro nastavení pravidla brány firewall služby Azure, které jsou ve stejné oblasti jako spravované instance, protože Platforma Azure má optimalizace pro provoz, který prochází mezi službami, které jsou společně umístěná.

## <a name="network-requirements"></a>Síťové požadavky

Nasadíte spravovanou instanci ve vyhrazené podsíti (podsíti spravované instance) uvnitř virtuální sítě, který splňuje následující požadavky:

- **Vyhrazené podsíti**: Podsíti spravované instance nesmí obsahovat jiné cloudové služby s ním spojená, a nesmí se jednat o podsíť brány. Nebudete mít k vytvoření managed instance v podsíti, která obsahuje prostředky než spravovaná instance a dalších prostředků nelze přidat později v podsíti.
- **Skupina zabezpečení sítě (NSG)**: Skupina zabezpečení sítě přidruženou k virtuální síti musí obsahovat tyto definované povinné [příchozí pravidla zabezpečení](#mandatory-inbound-security-rules) a [odchozí pravidla zabezpečení](#mandatory-outbound-security-rules) před všechna pravidla. Skupina zabezpečení sítě můžete použít plně řídit přístup ke koncovému bodu data spravované instance pomocí filtrování provozu na portu 1433.
- **Tabulka trasy definované uživatelem (UDR)**: Přidružené k virtuální síti trasy definované uživatelem tabulka musí obsahovat tyto [položky](#user-defined-routes) trasy definované uživatelem tabulky.
- **Žádné koncové body služby**: Podsíť spravované instance nesmí mít přiřazen koncový bod služby. Ujistěte se, že při vytváření virtuální sítě je zakázána možnost koncových bodů služby.
- **Dostatek IP adres**: Spravovaná instance podsíť musí mít minimální 16 IP adres (Doporučená minimální je 32 IP adresy). Další informace najdete v tématu [určit velikost podsítě pro spravované instance](sql-database-managed-instance-determine-size-vnet-subnet.md). Můžete nasadit spravované instance v [existující síť](sql-database-managed-instance-configure-vnet-subnet.md) po můžete nakonfigurovat k uspokojení [managed instance požadavky na síť](#network-requirements), nebo vytvořte [novou síť a podsíť](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Nebude moct nasadit nové spravované instance, pokud není kompatibilní se všemi těmito požadavky cílové podsíti. Po vytvoření managed instance *záměr zásady sítě* se použije v podsíti, aby se zabránilo nekompatibilních změn konfigurace sítě. Po poslední instance se odebere z podsítě *zásady sítě záměr* bude odebrán také

### <a name="mandatory-inbound-security-rules"></a>Pravidla povinné zabezpečení příchozích dat

| Název       |Port                        |Protocol (Protokol)|Zdroj           |Cíl|Akce|
|------------|----------------------------|--------|-----------------|-----------|------|
|Správa  |9000, 9003, 1438, 1440, 1452|TCP     |Všechny              |Všechny        |Povolit |
|mi_subnet   |Všechny                         |Všechny     |MI SUBNET        |Všechny        |Povolit |
|health_probe|Všechny                         |Všechny     |AzureLoadBalancer|Všechny        |Povolit |

### <a name="mandatory-outbound-security-rules"></a>Povinné odchozí pravidla zabezpečení

| Název       |Port          |Protocol (Protokol)|Zdroj           |Cíl|Akce|
|------------|--------------|--------|-----------------|-----------|------|
|Správa  |80, 443, 12000|TCP     |Všechny              |Internet   |Povolit |
|mi_subnet   |Všechny           |Všechny     |Všechny              |MI PODSÍTĚ *  |Povolit |

\* PODSÍŤ MI odkazuje na rozsah IP adres podsítě v 10.x.x.x/y formuláře. Tyto informace můžete najít na webu Azure Portal (prostřednictvím vlastnosti podsítě).

> [!IMPORTANT]
> I když povinné zabezpečení příchozích pravidel povolit provoz z _jakékoli_ zdroje na portech 9000 9003, 1438, 1440, 1452 tyto porty jsou chráněné bránou firewall integrované. To [článku](sql-database-managed-instance-find-management-endpoint-ip-address.md) ukazuje, jak lze zjistit IP adresu koncového bodu správy a ověřte pravidla brány firewall.
> [!NOTE]
> Pokud používáte transakční replikace ve spravované instanci a všechny instance databáze slouží jako vydavatel nebo distributora, port 445 (odchozí TCP) také musí být otevřené v pravidlech zabezpečení podsítě pro přístup ke sdílené složce Azure.

### <a name="user-defined-routes"></a>Trasy definované uživatelem

|Název|Předpona adresy|NET směrování|
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

Kromě toho můžete přidat položky do směrovací tabulky směruje provoz, který má místní rozsahy privátních IP jako cíl přes bránu virtuální sítě nebo virtuální síťové zařízení (NVA).

- **Volitelné vlastní DNS**: Pokud vlastní DNS je zadána ve službě virtual network, Azure rekurzivní překladač IP adresu (například adresy 168.63.129.16) musí být přidaný do seznamu. Další informace najdete v tématu [konfigurace vlastního DNS](sql-database-managed-instance-custom-dns.md). Vlastní server DNS musí být schopen překladu názvů hostitelů na následující domény a jejich subdomény: *microsoft.com*, *windows.net*, *windows.com*, *msocsp.com*, *digicert.com*, *live.com*, *microsoftonline.com*, a *microsoftonline-p.com*.

## <a name="next-steps"></a>Další postup

- Přehled najdete v tématu [co je managed instance](sql-database-managed-instance.md)
- Zjistěte, jak [nakonfigurovat nové sítě VNet](sql-database-managed-instance-create-vnet-subnet.md) nebo [konfigurace stávající virtuální síť](sql-database-managed-instance-configure-vnet-subnet.md) kde můžete nasadit spravované instance.
- [Vypočítat na velikost podsítě](sql-database-managed-instance-determine-size-vnet-subnet.md) nasazování spravované instance.
- Rychlé starty najdete v tom, jak vytvořit spravovanou instanci:
  - Z [webu Azure portal](sql-database-managed-instance-get-started.md)
  - pomocí [prostředí PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/27/quick-start-script-create-azure-sql-managed-instance-using-powershell/)
  - pomocí [šablony Azure Resource Manageru](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/)
  - pomocí [šablony Azure Resource Manageru (jumpbox pomocí SSMS zahrnuté)](https://portal.azure.com/)
