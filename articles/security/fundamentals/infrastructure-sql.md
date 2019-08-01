---
title: Funkce zabezpečení Azure SQL Database
description: Tento článek poskytuje obecný popis způsobu, jakým Azure SQL Database chrání zákaznická data v Azure.
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
ms.openlocfilehash: d883a9672ce8e33a1c13c96bca727b925bfbd84f
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68613299"
---
# <a name="azure-sql-database-security-features"></a>Funkce zabezpečení Azure SQL Database    
Azure SQL Database poskytuje službu relačních databází v Azure. Aby bylo možné chránit zákaznická data a poskytovat silné bezpečnostní funkce, které zákazníci očekávají od služby relační databáze, SQL Database mají vlastní sady možností zabezpečení. Tyto možnosti se sestavují na ovládací prvky, které jsou zděděné z Azure.

## <a name="security-capabilities"></a>Možnosti zabezpečení

### <a name="usage-of-the-tds-protocol"></a>Použití protokolu TDS
Azure SQL Database podporuje jenom protokol TDS (Tabular data Stream), který vyžaduje přístupnost databáze jenom na výchozím portu TCP/1433.

### <a name="azure-sql-database-firewall"></a>Azure SQL Database firewall
Aby bylo možné chránit zákaznická data, Azure SQL Database zahrnuje funkci brány firewall, která standardně zabraňuje všem přístupům k serveru SQL Database, jak je vidět níže.

![Azure SQL Database firewall](./media/infrastructure-sql/sql-database-firewall.png)

Brána firewall brány může omezit adresy, což zákazníkům umožňuje podrobná řízení, aby určovala rozsah přípustných IP adres. Brána firewall uděluje přístup na základě zdrojové IP adresy jednotlivých požadavků.

Zákazníci mohou dosáhnout konfigurace brány firewall pomocí portálu pro správu nebo programově pomocí REST API správy Azure SQL Database. Brána firewall brány Azure SQL Database ve výchozím nastavení zabraňuje všemu přístupu ke službě Azure SQL Database všem zákazníkům. Zákazníci musí nakonfigurovat přístup pomocí seznamů řízení přístupu (ACL) a povolit tak Azure SQL Database připojení pomocí zdrojových a cílových internetových adres, protokolů a čísel portů.

### <a name="dosguard"></a>Dosguardu
Útoky DoS (Denial of Service) se snižují pomocí služby SQL Database Gateway s názvem Dosguardu. Dosguardu aktivně sleduje neúspěšná přihlášení z IP adres. Pokud dojde k několika neúspěšným přihlášením z konkrétní IP adresy v časovém intervalu, IP adresa se zablokuje přístup k žádným prostředkům ve službě za předem definované časové období.

Azure SQL Database brána navíc provádí tyto akce:

- Vyjednávání schopností zabezpečeného kanálu k implementaci šifrovaných připojení TDS FIPS 140-2, když se připojí k databázovým serverům.
- Stavová kontrola paketů TDS, když přijímá připojení od klientů. Brána ověří informace o připojení a předá pakety TDS příslušnému fyzickému serveru na základě názvu databáze zadaného v připojovacím řetězci.

Principem převázání zabezpečení sítě Azure SQL Database nabídky je povolení pouze připojení a komunikace, která je nutná k tomu, aby mohla služba fungovat. Ve výchozím nastavení jsou všechny ostatní porty, protokoly a připojení blokované. Virtuální místní sítě (VLAN) a seznamy řízení přístupu (ACL) slouží k omezení síťové komunikace podle zdrojové a cílové sítě, protokolů a čísel portů.

Mechanismy, které jsou schválené k implementaci seznamů ACL založeného na síti, zahrnují seznamy ACL na směrovačích a nástrojích pro vyrovnávání zatížení Tyto mechanismy spravuje síť Azure, bránu firewall virtuálního počítače hosta a Azure SQL Database pravidla brány firewall brány, která jsou nakonfigurovaná zákazníkem.

## <a name="data-segregation-and-customer-isolation"></a>Oddělení dat a izolace zákazníků
Produkční síť Azure je strukturovaná tak, aby byly veřejně dostupné systémové komponenty oddělené od interních prostředků. Mezi webovými servery, které poskytují přístup k veřejnému Azure Portal a základní virtuální infrastruktuře Azure, kde se nacházejí instance aplikací zákazníka a zákaznická data, existují fyzické a logické hranice.

Všechny veřejně dostupné informace se spravují v produkční síti Azure. Produkční síť se vztahuje na dvojúrovňové ověřování a mechanismy ochrany hranic, používá sadu funkcí firewall a zabezpečení, která je popsaná v předchozí části, a používá funkce izolace dat, jak je uvedeno v následujících částech.

### <a name="unauthorized-systems-and-isolation-of-the-fc"></a>Neautorizované systémy a izolace FC
Vzhledem k tomu, že řadič prostředků infrastruktury (FC) je centrálním nástrojem Orchestrator prostředků infrastruktury Azure, jsou k dismístě významné kontroly, které jim umožní zmírnit jejich hrozby, zejména z potenciálně napadení v rámci zákaznických aplikací. FC nerozpozná žádný hardware, jehož informace o zařízení (například adresa MAC) není předem načten v rámci funkce FC. Servery DHCP v FC mají nakonfigurovány seznamy adres MAC uzlů, které mají být spuštěny. I když jsou připojené neautorizované systémy, nejsou součástí inventáře prostředků infrastruktury, a proto nejsou připojené ani autorizované ke komunikaci s žádným systémem v inventáři prostředků infrastruktury. Tím se snižuje riziko komunikace neautorizovaných systémů s FC a získání přístupu k síti VLAN a Azure.

### <a name="vlan-isolation"></a>Izolace sítě VLAN
Produkční síť Azure je logicky oddělená na tři primární sítě VLAN:

- Hlavní síť VLAN: Propojuje nedůvěryhodné uzly zákazníka.
- SÍŤ VLAN FC: Obsahuje důvěryhodné FCs a podpůrné systémy.
- SÍŤ VLAN zařízení: Obsahuje důvěryhodnou síť a jiná zařízení infrastruktury.

### <a name="packet-filtering"></a>Filtrování paketů
IPFilter a softwarové brány firewall implementované v kořenovém operačním systému a hostovaném operačním systému uzlů vynutila omezení připojení a zabraňují neoprávněnému provozu mezi virtuálními počítači.

### <a name="hypervisor-root-os-and-guest-vms"></a>Hypervisor, kořenový operační systém a virtuální počítače hosta
Izolaci kořenového operačního systému z virtuálních počítačů hosta a virtuálních počítačů hosta od sebe od sebe jsou spravovány hypervisorem a kořenovým operačním systémem.

### <a name="types-of-rules-on-firewalls"></a>Typy pravidel pro brány firewall
Pravidlo je definováno jako:

{Security Response Center (src) IP adresa, zdrojový port, cílová IP adresa, cílový port, cílový protokol, stav/výstup, stavový/stavový časový limit pro stavový tok.

Pakety synchronního nečinného znaku (SYN) jsou povolené nebo odstraněné jenom v případě, že je povolují některá z těchto pravidel. V případě TCP používá Azure Bezstavová pravidla, kde je zásada, že umožňuje do virtuálního počítače nebo z něj pouze všechny pakety, které nepatří do SYN. Místní zabezpečení je, že jakýkoliv zásobník hostitele je odolný proti ignorování nesyn, pokud předtím neviděl paket SYN. Samotný protokol TCP je stavový a v kombinaci s pravidlem založeným na nestavové implementaci dosahuje celkové chování stavové implementace.

Pro protokol UDP (User Datagram Protocol) Azure používá stavové pravidlo. Pokaždé, když paket UDP odpovídá pravidlu, vytvoří se opačný tok v opačném směru. Tento tok má vestavěný časový limit.

Zákazníci zodpovídají za vytváření vlastních bran firewall nad tím, co Azure poskytuje. Zákazníci tak můžou definovat pravidla pro příchozí a odchozí provoz.

### <a name="production-configuration-management"></a>Správa konfigurace výroby
Standardní zabezpečené konfigurace spravuje příslušné provozní týmy v Azure a Azure SQL Database. Všechny změny konfigurace v produkčních systémech jsou zdokumentovány a sledovány prostřednictvím centrálního systému sledování. Změny softwaru a hardwaru jsou sledovány prostřednictvím systému centrálního sledování. Změny sítě, které se vztahují k seznamu ACL, se sledují pomocí služby pro správu ACL.

Všechny změny konfigurace Azure se vyvíjí a testují v přípravném prostředí a následně se nasazují v produkčním prostředí. Sestavení softwaru se přezkoumávají jako součást testování. Kontroly zabezpečení a ochrany osobních údajů jsou přezkoumány jako součást kritérií kontrolního seznamu položek. Změny jsou nasazeny v plánovaných intervalech příslušným týmem nasazení. Verze jsou přezkoumány a podepsány příslušnými zaměstnanci týmu nasazení před jejich nasazením do produkčního prostředí.

Změny se monitorují pro úspěch. V případě selhání se změna vrátí zpátky na předchozí stav nebo se nasadí oprava hotfix, která bude vyřešit selhání se schválením určených zaměstnanců. Zdrojové sklady, Git, TFS, Master Data Services (MDS), spouštěče, Azure Security monitoring, FC a platforma WinFabric slouží k centrální správě, použití a ověření nastavení konfigurace ve virtuálním prostředí Azure.

Podobně platí, že změny hardwaru a sítě vedly k vyhodnocení dodržování požadavků na sestavení. Vydané verze jsou přezkoumány a autorizovány prostřednictvím koordinovaných poradních panelů pro změny (CAB) odpovídajících skupin v rámci zásobníku.

## <a name="next-steps"></a>Další postup
Další informace o tom, co Microsoft dělá k zabezpečení infrastruktury Azure, najdete tady:

- [Zařízení, místní a fyzické zabezpečení Azure](physical-security.md)
- [Dostupnost infrastruktury Azure](infrastructure-availability.md)
- [Komponenty a hranice informačních systémů Azure](infrastructure-components.md)
- [Architektura sítě Azure](infrastructure-network.md)
- [Produkční síť Azure](production-network.md)
- [Provozní provoz a Správa Azure](infrastructure-operations.md)
- [Monitorování infrastruktury Azure](infrastructure-monitoring.md)
- [Integrita infrastruktury Azure](infrastructure-integrity.md)
- [Ochrana zákaznických dat Azure](protection-customer-data.md)


