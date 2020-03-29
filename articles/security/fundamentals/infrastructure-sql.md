---
title: Funkce zabezpečení Azure SQL Database
description: Tento článek obsahuje obecný popis toho, jak Azure SQL Database chrání zákaznická data v Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/09/2020
ms.author: terrylan
ms.openlocfilehash: ad6d3992f03802174eb03aa30b57b8d3dac1d6c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942950"
---
# <a name="azure-sql-database-security-features"></a>Funkce zabezpečení Azure SQL Database    
Azure SQL Database poskytuje relační databázovou službu v Azure. K ochraně zákaznických dat a poskytování silných funkcí zabezpečení, které zákazníci očekávají od relační databázové služby, má databáze SQL vlastní sady funkcí zabezpečení. Tyto funkce se řídí ovládacími prvky, které jsou zděděné z Azure.

## <a name="security-capabilities"></a>Možnosti zabezpečení

### <a name="usage-of-the-tds-protocol"></a>Použití protokolu TDS
Azure SQL Database podporuje jenom tabulkový datový tok (TDS) protokol, který vyžaduje, aby databáze byla přístupná pouze přes výchozí port TCP/1433.

### <a name="azure-sql-database-firewall"></a>Brána firewall azure databáze SQL
Z důvodu ochrany zákaznických dat obsahuje Azure SQL Database funkci brány firewall, která ve výchozím nastavení zabraňuje veškerému přístupu k databázovému serveru SQL, jak je znázorněno níže.

![Brána firewall azure databáze SQL](./media/infrastructure-sql/sql-database-firewall.png)

Brána firewall brány může omezit adresy, což umožňuje zákazníkům podrobné řízení určit rozsahy přijatelných IP adres. Brána firewall uděluje přístup na základě původní ip adresy každého požadavku.

Zákazníci mohou dosáhnout konfigurace brány firewall pomocí portálu pro správu nebo programově pomocí rozhraní REST API azure sql database management. Brána brány Azure SQL Database ve výchozím nastavení zabraňuje všem zákazníkům přístup u tds k instancím databáze Azure SQL. Zákazníci musí nakonfigurovat přístup pomocí seznamů řízení přístupu (ACLs) povolit připojení Azure SQL Database podle zdrojové a cílové internetové adresy, protokoly a čísla portů.

### <a name="dosguard"></a>Dosguard
Útoky typu DoS (Denial of Service) jsou omezeny službou brány databáze SQL s názvem DoSGuard. DoSGuard aktivně sleduje neúspěšné přihlášení z IP adres. Pokud existuje více neúspěšných přihlášení z určité adresy IP v časovém období, ip adresa je blokován přístup k jakékoli prostředky ve službě pro předem definované časové období.

Kromě toho brána Azure SQL Database provádí:

- Vyjednávání o možnostech zabezpečeného kanálu k implementaci šifrovaných připojení ověřených protokolem TDS FIPS 140-2 při připojení k databázovým serverům.
- Stavová kontrola paketů TDS, zatímco přijímá připojení od klientů. Brána ověří informace o připojení a předá pakety TDS příslušnému fyzickému serveru na základě názvu databáze určeného v připojovacím řetězci.

Zastřešujícíprincip pro zabezpečení sítě nabídky Azure SQL Database je povolit pouze připojení a komunikaci, která je nezbytná k povolení služby k provozu. Všechny ostatní porty, protokoly a připojení jsou ve výchozím nastavení blokovány. Virtuální místní sítě (VLAN) a seznamy ACL se používají k omezení síťové komunikace podle zdrojových a cílových sítí, protokolů a čísel portů.

Mechanismy, které jsou schváleny k implementaci síťových aloků, zahrnují alokace ACna na směrovačích a nástroj pro vyrovnávání zatížení. Tyto mechanismy jsou spravované sítě Azure, brána firewall hostovaného virtuálního počítače a pravidla brány brány Azure SQL Database brány, které nakonfiguruje zákazník.

## <a name="data-segregation-and-customer-isolation"></a>Oddělení dat a izolace zákazníků
Produkční síť Azure je strukturovaná tak, aby veřejně přístupné systémové součásti byly odděleny od interních prostředků. Fyzické a logické hranice existují mezi webovými servery, které poskytují přístup k veřejnému portálu Azure a základní virtuální infrastruktuře Azure, kde se nacházejí instance zákaznických aplikací a zákaznická data.

Všechny veřejně přístupné informace se spravují v rámci produkční sítě Azure. Produkční síť podléhá dvoufaktorovým mechanismům ověřování a ochraně hranic, používá bránu firewall a sadu funkcí zabezpečení, která je popsána v předchozí části, a používá funkce izolace dat, jak je uvedeno v následujících částech.

### <a name="unauthorized-systems-and-isolation-of-the-fc"></a>Neautorizované systémy a izolace FC
Vzhledem k tomu, že řadič prostředků infrastruktury (FC) je centrální orchestrátor prostředků Azure fabric, jsou zavedeny významné ovládací prvky ke zmírnění hrozeb pro něj, zejména z potenciálně ohrožené FINANČNÍ UMÍSTĚNÍ v rámci zákaznických aplikací. Fc nerozpozná žádný hardware, jehož informace o zařízení (například MAC adresa) není předem načtenv fc. Servery DHCP v fc mají nakonfigurované seznamy MAC adres uzlů, které jsou ochotny spustit. I v případě, že jsou připojeny neautorizované systémy, nejsou začleněny do inventáře tkaniny, a proto nejsou připojeny nebo oprávněny komunikovat s žádným systémem v rámci inventarizace tkaniny. To snižuje riziko komunikace neoprávněných systémů s FC a získání přístupu k síti VLAN a Azure.

### <a name="vlan-isolation"></a>Izolace sítě VLAN
Produkční síť Azure je logicky oddělená do tří primárních sítí VLAN:

- Hlavní síť VLAN: Propojuje nedůvěryhodné uzly zákazníků.
- FC VLAN: Obsahuje důvěryhodné řadiče domény a podpůrné systémy.
- Zařízení VLAN: Obsahuje důvěryhodnou síť a další zařízení infrastruktury.

### <a name="packet-filtering"></a>Filtrování paketů
Filtr IPFilter a softwarové brány firewall, které jsou implementovány v kořenovém osu a hostovaném os uzly vynucují omezení připojení a zabránit neoprávněnému provozu mezi virtuálními počítači.

### <a name="hypervisor-root-os-and-guest-vms"></a>Hypervisor, kořenový operační operační program a hostované virtuální uživatele
Izolace kořenového operačního režimu z virtuálních virtuálních měn hosta a virtuálních virtuálních ms hosta od sebe navzájem je spravovánhypervisor a kořenového operačního režimu.

### <a name="types-of-rules-on-firewalls"></a>Typy pravidel pro brány firewall
Pravidlo je definováno jako:

{Src IP, Src Port, Destination IP, Cílový port, Cílový protokol, Dovnitř/Out, Stavový/Bezstavový, Časový čas toku stavu}.

Synchronní nečinné znakové pakety (SYN) jsou povoleny dovnitř nebo ven pouze v případě, že to některé z pravidel umožňuje. Pro TCP Azure používá bezstavová pravidla, kde princip je, že umožňuje pouze všechny pakety bez SYN do nebo z virtuálního počítače. Předpoklad zabezpečení je, že všechny zásobníky hostitele je odolný ignorování non-SYN, pokud nebyl a dosud neviděl syn paket. Protokol TCP sám o sobě je stavové a v kombinaci s bezstavové pravidlo založené na SYN dosahuje celkové chování stavové implementace.

Pro protokol UDP (User Datagram Protocol) používá Azure stavové pravidlo. Pokaždé, když paket UDP odpovídá pravidlu, je v opačném směru vytvořen zpětný tok. Tento tok má vestavěný časový čas.

Zákazníci jsou zodpovědní za nastavení vlastních bran firewall nad základě toho, co Azure poskytuje. Zde mohou zákazníci definovat pravidla pro příchozí a odchozí provoz.

### <a name="production-configuration-management"></a>Správa konfigurace výroby
Standardní zabezpečené konfigurace jsou spravovány příslušnými provozními týmy v Azure a Azure SQL Database. Všechny změny konfigurace výrobních systémů jsou dokumentovány a sledovány prostřednictvím centrálního sledovacího systému. Změny softwaru a hardwaru jsou sledovány prostřednictvím centrálního sledovacího systému. Síťové změny, které se vztahují k acl jsou sledovány pomocí služby správy acl.

Všechny změny konfigurace v Azure jsou vyvíjeny a testovány v testovacím prostředí a poté se nasazují v produkčním prostředí. Sestavení softwaru jsou kontrolována jako součást testování. Kontroly zabezpečení a ochrany osobních údajů jsou kontrolovány jako součást kritérií kontrolního seznamu položek. Změny jsou nasazeny v naplánovaných intervalech příslušným týmem nasazení. Verze jsou kontrolovány a odpovězeny příslušným týmem nasazení před jejich nasazením do produkčního prostředí.

Změny jsou sledovány pro úspěch. V případě selhání je změna vrácena zpět do předchozího stavu nebo je nasazena oprava hotfix k řešení selhání se schválením určeného personálu. Source Depot, Git, TFS, Master Data Services (MDS), běžci, monitorování zabezpečení Azure, FC a platforma WinFabric se používají k centrální správě, použití a ověření nastavení konfigurace ve virtuálním prostředí Azure.

Podobně změny hardwaru a sítě zavedly kroky ověření k vyhodnocení jejich dodržování požadavků na sestavení. Verze jsou kontrolovány a autorizovány prostřednictvím koordinovaného poradního sboru pro změny (CAB) příslušných skupin v celém zásobníku.

## <a name="next-steps"></a>Další kroky
Další informace o tom, co Microsoft dělá pro zabezpečení infrastruktury Azure, najdete v tématu:

- [Zařízení Azure, prostory a fyzické zabezpečení](physical-security.md)
- [Dostupnost infrastruktury Azure](infrastructure-availability.md)
- [Součásti a hranice informačního systému Azure](infrastructure-components.md)
- [Architektura sítě Azure](infrastructure-network.md)
- [Produkční síť Azure](production-network.md)
- [Produkční operace a správa Azure](infrastructure-operations.md)
- [Monitorování infrastruktury Azure](infrastructure-monitoring.md)
- [Integrita infrastruktury Azure](infrastructure-integrity.md)
- [Ochrana zákaznických dat Azure](protection-customer-data.md)
