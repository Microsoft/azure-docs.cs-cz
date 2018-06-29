---
title: Funkce zabezpečení služby Azure SQL Database
description: Tento článek poskytuje že obecný popis Azure SQL Database chrání data zákazníků v Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: cca8febb004029b13b0df09a047da701c4528e8e
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102226"
---
# <a name="microsoft-azure-sql-database-security-features"></a>Zabezpečení funkce Microsoft Azure SQL Database    
Microsoft Azure SQL Database nabízí služba relační databáze v Azure. K ochraně dat zákazníků a poskytují spolehlivé zabezpečení funkce, které zákazníci očekávají služba relační databáze, SQL Database má svou vlastní sadu funkce zabezpečení. Tyto možnosti stavějí ovládacích prvků, které se dědí z Azure.

## <a name="security-capabilities"></a>Možnosti zabezpečení

### <a name="usage-of-tabular-data-stream-tds-protocol"></a>Použití protokolu datový proud TDS (Tabular Data)
Microsoft Azure SQL Database podporuje jenom protokol TDS, který vyžaduje databáze byla přístupná jenom přes výchozí port z TCP/1433.

### <a name="microsoft-azure-sql-database-firewall"></a>Brány Firewall pro Microsoft Azure SQL Database
K ochraně dat zákazníků, Microsoft Azure SQL Database zahrnuje funkce brány firewall, který ve výchozím nastavení brání veškerý přístup k databázi SQL serveru, jak je uvedeno níže.

![Azure SQL Database brány firewall][1]

Brány firewall poskytuje možnost omezit umožní pro přesná kontrola zákazníkům zadejte rozsahy přijatelné adres IP adresy. Brána firewall uděluje přístup na základě původní IP adresy každého požadavku.

Konfigurace brány firewall, můžete to udělat pomocí portálu pro správu nebo programově pomocí Microsoft Azure SQL Database Management REST API. Microsoft Azure SQL Database brány firewall ve výchozím nastavení brání veškerý přístup TDS zákazníka k databázím SQL Microsoft Azure. Přístup musí být nakonfigurovaný pomocí seznamů řízení přístupu tak, aby povolovala připojení Microsoft Azure SQL Database zdrojové a cílové adresy Internet, protokoly a čísla portů.

### <a name="dosguard"></a>Dosguardu
Odmítnutí služby (DoS) útoky jsou sníženy služby SQL Database brány názvem Dosguardu. Dosguardu aktivně sleduje neúspěšných přihlášení z IP adres. Pokud je v časovém období několik neúspěšných přihlášení z konkrétní IP adresu, IP adresa je blokovaný přístup k veškeré prostředky ve službě pro předem definované časové období.

Kromě výše uvedeného také provádí bránu Microsoft Azure SQL Database:

- Zabezpečený kanál schopností jednání implementovat TDS FIPS 140-2 ověřit šifrované připojení při připojování k serveru databáze.
- Stavová kontroly paketů TDS při přijímání připojení od klientů. Brány ověří informace o připojení a předá na pakety TDS příslušný fyzický server na základě názvu databáze zadaná v připojovacím řetězci.

Zastřešující Princip pro zabezpečení sítě nabídky Microsoft Azure SQL Database je povolit pouze připojení a komunikaci, která je potřeba povolit službu k provozu. Ve výchozím nastavení jsou blokovány všechny ostatní porty, protokoly a připojení. Sítě VLAN a seznamy ACL se používá k omezení síťové komunikace zdrojové a cílové sítě, protokoly a čísla portů.

Zahrnout schválené mechanismy pro implementaci seznamy ACL založené na síti: seznamy řízení přístupu na směrovačích a nástroje pro vyrovnávání zatížení. Tyto jsou spravovány sítí Azure, hosta virtuálního počítače brány firewall a pravidla brány firewall brány Microsoft Azure SQL Database, která jsou nakonfigurované zákazník.

## <a name="data-segregation-and-customer-isolation"></a>Data oddělení a k zákaznickým izolace
Azure produkční sítě strukturovaná tak, aby komponenty veřejně přístupná system jsou oddělené od interním prostředkům. Fyzické a logické hranice existovat mezi webové servery, které poskytuje přístup k portálu Azure veřejné a základní virtuální infrastruktura Azure, kde jsou umístěny zákazníka instancemi aplikace a data zákazníků.

Všechny veřejně dostupné informace o správě v rámci Azure produkční sítě. Produkční sítě podléhá dvoufaktorové ověřování a mechanismy ochrany hranic, používá funkci brány firewall a zabezpečení nastavení popsané v předchozí části a používá funkce izolace dat dole uvedených položek.

### <a name="unauthorized-systems-and-isolation-of-fc"></a>Neoprávněné systémy a izolace FC
Vzhledem k tomu, že FC je centrální orchestrator prostředků infrastruktury Microsoft Azure, jsou významné ovládací prvky zavedené zmírnit hrozby, zejména z potenciálně ohroženými DM v aplikacích zákazníka. FC nerozpoznal žádný hardware, jehož informace o zařízení (například adresy MAC) není v rámci FC předinstalována. Servery DHCP, na FC nakonfigurovali seznamy adres MAC, které jsou ochotni spouštěcí uzly. I v případě, že neoprávněným systémy připojené, jejich nejsou součástí infrastruktury inventáře a proto připojen nebo oprávnění ke komunikaci se všechny systémy v rámci prostředků infrastruktury inventáře. Tím se snižuje riziko neoprávněného systémy komunikaci s FC a získání přístupu k síti VLAN a Azure.

### <a name="vlan-isolation"></a>Izolace sítě VLAN
Produkční sítě Azure je logicky rozdělen na tři primární sítě VLAN:

- Hlavní sítě VLAN – propojení uzlů nedůvěryhodné zákazníka
- Sítě VLAN FC – obsahuje důvěryhodné FCs a podporuje systémy
- Zařízení sítě VLAN – obsahuje důvěryhodné sítě a jiných zařízeních infrastruktury

### <a name="packet-filtering"></a>Filtrování paketů
IPFilter a software brány firewall, implementuje na kořenové operačního systému a hostovaného operačního systému uzlů vynutit omezení připojení a zabránit neoprávněným provoz mezi virtuálními počítači.

### <a name="hypervisor-root-os-and-guest-vms"></a>Hypervisor, kořenové operačního systému a hostovaný virtuální počítače
Izolace kořenové operačního systému z virtuálních počítačů hostovaného a hostovaný virtuální počítače od sebe navzájem spravuje hypervisoru a kořenové operačního systému.

### <a name="types-of-rules-on-firewalls"></a>Typy pravidla brány firewall
Pravidlo je definován jako:

{Security Response Center (Src), Src Port, cílové IP adresy, cílový Port cílový protokol IP, vstupně -výstupnímu Stateful/Stateless, stavová toku vypršení časového limitu}.

SYN pakety jsou povoleny pouze v případě, že umožňuje některého z pravidel příchozí nebo odchozí. Pro TCP Microsoft Azure používá bezstavové pravidla, kde se zásadou je, že pouze umožňuje všechny pakety bez SYN do nebo z virtuálního počítače. Místní zabezpečení je odolný ignorování jiný SYN, pokud nebylo nikdy paket SYN dříve zásobníkem žádné hostitele. Samotný protokol TCP je stavová a v kombinaci s bezstavové SYNbased pravidla, která patří dosahuje celkového chování stavová implementace.

Pro protokol UDP (User Datagram), Microsoft Azure používá stavová pravidla. Pokaždé, když paket UDP odpovídá pravidlu, vytvoří se zpětné toku v opačným směrem. Tento tok má integrovanou vypršení časového limitu.

Je zodpovědností nastavení své vlastní brány firewall v Microsoft Azure poskytuje zákazníků. Zde je možné definovat pravidla pro příchozí a odchozí přenosy zákazníků.

### <a name="production-configuration-management"></a>Produkční Configuration Management.
Standardní konfigurace zabezpečení se spravují týmů příslušné operace ve službě Azure a Microsoft Azure SQL Database. Všechny změny konfigurace produkční systémy jsou zdokumentovaný a sledovaný prostřednictvím centrální sledování systému. Prostřednictvím centrální sledování systému se sledují změny softwaru a hardwaru. Změny v sítích týkající se řízení přístupu jsou sledovány pomocí seznamu ACL Management Service (AMS).

Všechny změny konfigurace Microsoft Azure se vývoji a testování v testovacím prostředí; a následně nasazené v provozním prostředí. Software sestavení jsou kontrolovány v rámci testování. Jako součást kritérií pro položku seznamu úkolů jsou kontrolovány kontroly zabezpečení a ochrany osobních údajů. Změny jsou nasazeny v naplánovaných intervalech tým příslušného nasazení. Verze jsou zkontrolovány a podepisuje pracovníky team příslušných nasazení před jejich nasazením v produkčním prostředí.

Změny jsou monitorovat úspěch. V případě selhání změna je vrácena zpět do původního stavu nebo opravu hotfix je nasazená k vyřešení selhání pomocí schválení určených osob. Zdroj skladu, Git, sady TFS, MDS, závodníky, Azure Security Monitoring (ASM), FC a platformou WinFabric slouží k centrálně spravovat, použití a ověřte nastavení konfigurace v Azure virtuální prostředí.

Podobně mají změny hardwaru a sítě navázat postup ověření vyhodnotit jejich dodržování požadavky na sestavení. Verze jsou zkontrolovány a oprávnění prostřednictvím koordinované změnu poradní Tabule (soubor CAB) příslušných skupin napříč zásobníku.

## <a name="next-steps"></a>Další postup
Další informace o funkci Microsoft pro zabezpečení infrastruktury Azure najdete v tématu:

- [Azure zařízení, místní a fyzické zabezpečení](azure-physical-security.md)
- [Dostupnost infrastruktury Azure](azure-infrastructure-availability.md)
- [Součásti systému Azure informace a hranice](azure-infrastructure-components.md)
- [Architektura sítě Azure](azure-infrastructure-network.md)
- [Produkční sítě Azure](azure-production-network.md)
- [Azure produkční operace a Správa](azure-infrastructure-operations.md)
- [Monitorování infrastruktury Azure](azure-infrastructure-monitoring.md)
- [Integritu infrastruktury Azure](azure-infrastructure-integrity.md)
- [Ochrana dat zákazníka v Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-sql/sql-database-firewall.png
