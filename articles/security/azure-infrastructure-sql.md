---
title: Funkce zabezpečení Azure SQL Database
description: Tento článek obsahuje obecný popis, jak Azure SQL Database chrání zákaznických dat v Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: cd2ad16f910f5d2b3b801c8d54e9df7660751462
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56103944"
---
# <a name="azure-sql-database-security-features"></a>Funkce zabezpečení Azure SQL Database    
Azure SQL Database poskytuje relační databázovou službu v Azure. Pro ochranu dat zákazníků a poskytuje silné zabezpečení funkce, které zákazníci očekávat od služba relační databáze, databáze SQL má svou vlastní sadu možností zabezpečení. Tyto možnosti jsou postavené ovládací prvky, které jsou zděděny z Azure.

## <a name="security-capabilities"></a>Možnosti zabezpečení

### <a name="usage-of-the-tds-protocol"></a>Využití protokolu TDS.
Azure SQL Database podporuje pouze protokol stream (TDS) tabulková data, která vyžaduje, aby databáze byla přístupná přes pouze výchozí port z protokol TCP/1433.

### <a name="azure-sql-database-firewall"></a>Brána firewall služby Azure SQL Database
Azure SQL Database pomáhá chránit zákaznická data, obsahuje funkci brány firewall, která ve výchozím nastavení brání veškerému přístupu k databázi SQL serveru, jak je znázorněno níže.

![Brána firewall služby Azure SQL Database][1]

Brány firewall můžete omezit adresy, které zákazníkům umožňuje podrobnou kontrolu určit rozsahy přípustných IP adres. Brána firewall uděluje přístup na základě původní IP adresy každé žádosti.

Zákazníky můžete dosáhnout konfigurace brány firewall pomocí portálu pro správu nebo programově pomocí rozhraní REST API Azure SQL Database Management. Brány firewall Azure SQL Database ve výchozím nastavení brání všech zákazníků TDS přístup do instance databáze Azure SQL. Zákazníkům musíte nakonfigurovat přístup pomocí seznamů řízení přístupu (ACL) tak, aby povolovala připojení k databázi SQL Azure ve zdrojové a cílové adresy internet, protokoly a čísla portů.

### <a name="dosguard"></a>DoSGuard
Útoky na dostupnost služby (DoS) jsou sníženy brány služby SQL Database zvané Dosguardu. Dosguardu aktivně sleduje neúspěšných přihlášení z IP adres. Pokud v časovém období několik neúspěšných přihlášení z konkrétní adresy IP, IP adresa má zablokovaný přístup k žádné prostředky ve službě pro předem definované časové období.

Kromě toho provádí brána Azure SQL Database:

- Funkce jednání zabezpečený kanál pro implementaci TDS FIPS 140-2 ověřit šifrovaná připojení při připojování k databázovým serverům.
- Stavové TDS kontroly paketů při přijímá připojení od klientů. Brány ověří informace o připojení a předá TDS pakety k příslušnému fyzického serveru na základě názvu databáze, která je zadaná v připojovacím řetězci.

Zastřešujícího zásady zabezpečení sítě nabídky Azure SQL Database je umožnit pouze připojení a komunikaci, která je potřeba povolit provoz služby. Ve výchozím nastavení jsou blokovány všechny ostatní porty, protokoly a připojení. Virtuální místní sítě (VLAN) a seznamy řízení přístupu se používají k omezení síťové komunikace mezi zdrojovou a cílovou sítí, protokoly a čísla portů.

Mechanismy, které jsou schváleny pro implementaci seznamů ACL založených na síti zahrnují seznamy ACL na směrovačích a nástroje pro vyrovnávání zatížení. Tyto mechanismy se spravují pomocí sítě Azure, brána firewall hosta virtuálního počítače a pravidla brány firewall brány Azure SQL Database, které jsou nakonfigurované zákazníka.

## <a name="data-segregation-and-customer-isolation"></a>Izolace dat oddělení a zákazníků
Produkční sítě Azure je strukturována tak, aby veřejně přístupné součásti jsou odděleni od interním prostředkům. Hranice fyzická a logická existují mezi webové servery, které poskytují přístup k webu Azure portal veřejnou a základní virtuální infrastrukturu Azure, kde jsou umístěné zákazníka instancemi aplikace a data zákazníků.

Veškeré informace veřejně přístupné spravovanou v rámci produkční sítě Azure. Produkční sítě je v souladu s dvoufaktorovým ověřováním a hranice mechanismy ochrany, použije sada funkcí brány firewall a zabezpečení, je popsaný v předchozí části, který používá funkce izolace dat, jak je uvedeno v dalších částech.

### <a name="unauthorized-systems-and-isolation-of-the-fc"></a>Neoprávněné systémy a izolaci FC
Protože kontroler prostředků infrastruktury (FC) je centrálního orchestrátora prostředky infrastruktury Azure, významné ovládací prvky jsou na místě ke zmírnění hrozeb, zejména z potenciálně napadeného FAs v rámci zákaznických aplikací. FC nebyl rozpoznán žádný hardware, jehož informace o zařízení (například adresy MAC) není předem načíst v rámci FC. Servery DHCP na FC nakonfigurovali seznamy adres MAC z uzlů, které jsou natolik, abyste spuštění. I v případě neoprávněného systémy jsou připojené, jejich nejsou součástí inventář prostředků infrastruktury a proto připojený nebo oprávnění ke komunikaci u každého systému v rámci inventář prostředků infrastruktury. Tím se snižuje riziko neoprávněného systémů komunikuje FC a získání přístupu k síti VLAN a Azure.

### <a name="vlan-isolation"></a>Izolace sítě VLAN
Produkční sítě Azure je logicky rozdělen na tři primární sítě VLAN:

- Hlavní sítě VLAN: Propojení uzlů nedůvěryhodné zákazníka.
- FC VLAN: Obsahuje důvěryhodné FCs a podpůrných systémů.
- Zařízení sítě VLAN: Obsahuje důvěryhodné sítě a jiných zařízeních infrastruktury.

### <a name="packet-filtering"></a>Filtrování paketů
IPFilter software brány firewall, které jsou implementovány v kořenovém operačním systému a hostovaného operačního systému uzlů vynutit omezení připojení a zabránit neoprávněné přenosy mezi virtuálními počítači.

### <a name="hypervisor-root-os-and-guest-vms"></a>Hypervisor, kořenové operačního systému a virtuálních počítačů hosta
Izolace kořenové operačního systému z virtuálních počítačů hosta a virtuální počítače hosta od sebe spravuje hypervisoru a kořenové operačního systému.

### <a name="types-of-rules-on-firewalls"></a>Typy pravidla brány firewall
Pravidlo je definována takto:

{Security Response Center (Src), Src Port, cílové IP, cílový Port cíl protokolu IP, příchozí/odchozí tok stavové a bezstavové, stavové vypršení časového limitu}.

Pakety synchronní nečinný znak (SYN) jsou povoleny pouze v případě, že některá pravidla to umožňuje snížení nebo navýšení kapacity. Pro protokol TCP Azure využívá Bezstavová pravidla, kde se zásadou je, že všechny pakety jiných SYN umožňuje do nebo z virtuálního počítače. Místní zabezpečení je odolné bez SYN ignorovat, pokud ji ještě před tím nezobrazil paket SYN jakoukoli sadu hostitelů. Samotný protokol TCP je stavový a v kombinaci s Bezstavová pravidla na základě SYN dosahuje celkové chování stavové implementace.

Pro protokol UDP (User Datagram), Azure využívá stavové pravidlo. Pokaždé, když se paket UDP, který odpovídá pravidlu, vytvoří zpětný tok v opačném směru. Tento tok má integrované vypršení časového limitu.

Zákazníci odpovídají za své vlastní brány firewall na Azure poskytuje nastavení. Tady zákazníci mají možnost definovat pravidla pro příchozí a odchozí provoz.

### <a name="production-configuration-management"></a>Konfigurace řízení výroby
Standardní konfigurace zabezpečeného spravuje příslušných provozní týmy v Azure a Azure SQL Database. Všechny změny konfigurace na provozní systémy jsou zdokumentované a sledovat prostřednictvím centrální sledovacím systémem. Softwarové a hardwarové změny jsou sledovány pomocí centrální sledování systému. Změny v sítích, které se týkají ACL jsou sledovány pomocí služby service management seznamu ACL.

Všechny změny konfigurace do Azure jsou vyvíjeny a testování v testovacím prostředí, a poté jsou nasazené v produkčním prostředí. Softwarová sestavení jsou kontrolovány jako součást testování. Kontroly zabezpečení a ochrany osobních údajů jsou kontrolovány jako součást kritérií kontrolní seznam pro položku. Změny jsou nasazeny v naplánovaných intervalech týmem příslušného nasazení. Verze jsou zkontrolovány a proběhlo schválení pracovníky týmu příslušných nasazení před jejich nasazením do produkčního prostředí.

Změny jsou sledovány pro úspěch. V případě selhání změny se vrátí zpět do předchozího stavu nebo opravu hotfix je nasazený k vyřešení selhání pomocí schválení určeným pracovníků. Depot zdroje, Git, TFS, Master Data Services (MDS), spouštěčů, monitorování zabezpečení Azure, FC a platformu WinFabric umožňují centrálně spravovat, použití a ověřte nastavení konfigurace v Azure virtuální prostředí.

Podobně změny hardwaru a sítě zavedli kroky ověření vyhodnotit jejich splňuje požadavky na sestavení. Verze jsou zkontrolovány a oprávnění prostřednictvím koordinovat poradní výbor změn (CAB) z příslušných skupin napříč zásobníku.

## <a name="next-steps"></a>Další postup
Další informace o Microsoft nemá pro zabezpečení infrastruktury Azure, najdete v tématech:

- [Zařízení Azure, místním prostředí a fyzické zabezpečení](azure-physical-security.md)
- [Dostupnost infrastruktury Azure](azure-infrastructure-availability.md)
- [Součásti systému Azure informace a hranice](azure-infrastructure-components.md)
- [Architektura sítě Azure](azure-infrastructure-network.md)
- [Produkční sítě Azure](azure-production-network.md)
- [Operace Azure výroby a správu](azure-infrastructure-operations.md)
- [Monitorování infrastruktury Azure](azure-infrastructure-monitoring.md)
- [Integrity infrastruktury Azure](azure-infrastructure-integrity.md)
- [Ochrana dat zákazníků Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-sql/sql-database-firewall.png
