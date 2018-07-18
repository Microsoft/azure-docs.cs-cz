---
title: Produkční sítě Azure
description: Tento článek obsahuje obecný popis Azure produkční sítě.
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
ms.openlocfilehash: 710792c890c3e48fc54507f93eeaee529ca839f8
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114024"
---
# <a name="the-azure-production-network"></a>Produkční sítě Azure
Uživatelé produkční sítě Azure zahrnují i externím zákazníkům, kteří přistupují k vlastní aplikace na platformě Azure a pracovníci podpory Azure, kteří spravují produkční sítě. Tento článek popisuje metody zabezpečení přístupu a ochrany mechanismy pro navázání připojení k Azure produkční sítě.

## <a name="internet-routing-and-fault-tolerance"></a>Internet směrování a odolnost proti chybám
Globálně redundantní interních a externích Azure služby DNS (Domain Name) infrastrukturu, v kombinaci s více primárního a sekundárního DNS server clusterů, poskytuje odolnost proti chybám. Ve stejnou dobu ovládací prvky zabezpečení další síť Azure, jako je například NetScaler, umožňují zabránit distribuované útoky na dostupnost služeb (DDoS) a chrání integritu služby Azure DNS.

Servery Azure DNS se nacházejí ve více datových center zařízení. Implementace Azure DNS zahrnuje hierarchii primární a sekundární servery DNS a veřejně překládat názvy domén Azure zákazníka. Názvy domén obvykle vyřešit CloudApp.net adresu, která zabalí virtuální IP (VIP) Adresa služby zákazníka. Jedinečné pro Azure, virtuální IP adresu, která odpovídá vnitřní vyhrazené IP (DIP) adresu tenanta překladu se provádí vyvažovači zátěže Microsoft za tuto virtuální IP adresy.

Azure je hostované v geograficky distribuovaných datových centrech Azure v rámci USA a orchard je založen na stavu nejmodernější směrování platformy, které implementují robustní, škálovatelnou architekturu standardy. Mezi důležité funkce patří:

- Engineering více protokolů přenosu na základě MPLS přepínání popisku, který poskytuje efektivní využití a bezproblémové snížení úrovně služby, pokud dojde k výpadku.
- Sítě jsou implementovány v "potřebu plus jeden" (N + 1) redundance architektury nebo vyšší.
- Externě datacentry obsluhuje vyhrazené a velkou šířku pásma sítě okruhů, které vlastnosti redundantně spojte se s víc než 1 200 poskytovatelů internetových služeb globálně v několika bodech partnerského vztahu. Toto připojení obsahuje přesahující 2 000 GB za sekundu (GBps) edge kapacity.

Protože Microsoft vlastní okruhy svou vlastní síť mezi datacentry, pomůže tyto atributy nabídku Azure dosáhnout + 99,9 % dostupnost sítě bez nutnosti pro tradiční poskytovatele internetových služeb třetích stran.

## <a name="connection-to-production-network-and-associated-firewalls"></a>Připojení k produkční sítě a přidružené brány firewall
Zásady toku provozu internetového síť Azure směruje provoz produkční sítě Azure, který se nachází v nejbližší regionální datacentra v rámci USA. Protože datových centrech Azure produkčního prostředí udržovat konzistentní síťové architektury a hardwaru, popis toku provozu, který následuje konzistentně platí pro všechna datová centra.

Po internetový provoz pro Azure se směruje na nejbližší datové centrum, se vytvoří připojení k přístupu k směrovačům. Tyto směrovače přístupu slouží k izolaci provozu mezi uzly Azure a odběratele instance virtuálních počítačů. Síťová infrastruktura zařízení v umístěních přístup a hraničními zařízeními jsou hranice body, ve kterém jsou použity filtry příchozí a odchozí. Tyto směrovače, jestliže jsou nakonfigurované v seznamu vrstvené řízení přístupu (ACL) filtrování nežádoucí síťový provoz a použití omezení přenosové rychlosti přenosu, v případě potřeby. Provoz, který je povolen na základě seznamu ACL se směruje do nástroje pro vyrovnávání zatížení. Distribuce směrovače jsou určené pro povolit pouze schválení Microsoft IP adresy, poskytuje ochranu proti falšování identity a navázání připojení TCP, které používají seznamy ACL.

Externí služby Vyrovnávání zatížení zařízení jsou umístěné za směrovače přístupu provádět překlad síťových adres (NAT) z IP adres směrovatelných v Internetu na Azure interní IP adresy. Zařízení také směrovat pakety do platné produkčního prostředí, které interní IP adresy a porty a že fungují jako ochranný mechanismus omezit vystavení adresní prostor interní produkční sítě.

Ve výchozím nastavení Microsoft vynucuje přenos protokolu HTTPS (Hypertext Secure) pro veškeré přenosy, které se přenášejí do webových prohlížečů zákazníků, včetně přihlášení a veškerý provoz po tomto datu. Použití protokolu TLS verze 1.2 umožní pro provoz probíhá přes zabezpečené tunelové propojení. Seznamy ACL na směrovačích přístup a základní zajistěte, aby byl zdrojový provoz konzistentní s očekávání.

V této architektuře o důležité odlišení srovnání s architektury zabezpečení, je, že neexistují žádná brána firewall vyhrazený hardware, zjišťování neoprávněných vniknutí specializované nebo zařízení ochrany před únikem informací nebo jiných zařízení zabezpečení, které jsou obvykle byl očekáván dřív, než připojení k Azure produkčního prostředí. Zákazníci obvykle předpokládají tyto hardwarových zařízení brány firewall v Azure network; ale žádný se použijí v rámci Azure. Tyto funkce zabezpečení jsou téměř výhradně součástí softwaru, na kterém běží Azure prostředí tak, aby poskytují mechanismy robustní, vícevrstvého zabezpečení, včetně schopnosti brány firewall. Rozsah hranice a přidružené zvětšování důležité zabezpečení zařízení je navíc jednodušší Správa a inventáře, jak je znázorněno na předchozím obrázku, protože ten je spravovaný pomocí softwaru, na kterém běží Azure.

## <a name="core-security-and-firewall-features"></a>Základní funkce zabezpečení a brány firewall
Implementace zabezpečení robustní softwaru a brány firewall na funkce na různých úrovních k vynucení funkce zabezpečení, které jsou obvykle má v prostředí tradiční ochrany hranic základní ověření zabezpečení Azure.

### <a name="azure-security-features"></a>Funkce zabezpečení Azure
Azure implementuje brány firewall založená na hostiteli software do produkční sítě. Několik základních zabezpečení a funkce brány se nacházejí v rámci jádra prostředí Azure. Tyto funkce zabezpečení zahrnují strategii obrany v prostředí Azure. Zákaznická data v Azure je chráněn následující brány firewall:

**Firewall hypervisoru (filtr paketů)**: Tato brána firewall je implementovaná v hypervisoru a nakonfigurovaná agentem kontroleru (FC) prostředků infrastruktury. Tato brána firewall chrání tenanta, na kterém běží ve virtuálním počítači před neoprávněným přístupem. Ve výchozím nastavení po vytvoření virtuálního počítače blokován veškerý provoz, a pak agenta FC přidá pravidel a výjimek ve filtru byl povolen autorizovaný provoz.

Dvě kategorie pravidel se naprogramovat tady:

- **Počítač config nebo infrastrukturu pravidel**: ve výchozím nastavení je veškerá komunikace blokovaná. Existují výjimky, které umožňují virtuálnímu počítači odesílat a přijímat komunikaci Dynamic Host Configuration Protocol (DHCP) a informace o DNS a odesílat provoz do "veřejného" Internetu odchozí do jiných virtuálních počítačů v rámci clusteru FC a aktivace operačního systému serveru. Vzhledem k tomu, že se virtuální počítače povolený seznam odchozí cíle neobsahuje podsítě směrovačů Azure a dalších vlastností Microsoft a pravidla sloužit jako vrstev obrany pro ně.
- **Role konfigurační soubor pravidel**: definuje vstupní seznamy řízení přístupu na základě modelu služby klientů. Například pokud klient má webového front-endu na portu 80 na určitém virtuálním počítači, se otevře port 80 pro všechny IP adresy. Pokud má role pracovního procesu, která běží virtuální počítač, role pracovního procesu je otevřít pouze k virtuálnímu počítači ve stejném tenantovi.

**Nativní hostitelské brány firewall**: Azure Service Fabric a Azure Storage běží v nativním operačním systému, který nemá žádné hypervisoru, a proto je brána Windows Firewall nakonfigurovaný s předchozí dvě sady pravidel.

**Hostitelské brány firewall**: hostitelské brány firewall chrání hostitelskému oddílu, který běží hypervisor. Pravidla jsou naprogramovaný tak, aby povolit pouze FC a aby komunikaci k hostitelskému oddílu na určitém portu. Další výjimkou je povolení odpovědi DHCP a odpovědí DNS. Azure používá konfigurační soubor počítače, který obsahuje šablonu pravidla brány firewall pro oddíl hostitele. Výjimka brány firewall hostitele také existuje, která umožňuje virtuálním počítačům komunikovat s komponentami, při přenosu server a server metadat prostřednictvím určité protokol a porty.

**Brána firewall hosta**: brány Windows Firewall jako součást hostovaného operačního systému, která je konfigurovatelná zákazníky na virtuálních počítačích zákazníků a úložiště.

Další funkce zabezpečení, které jsou součástí možnosti Azure patří:

- Součásti infrastruktury, které jsou přiřazeny IP adresy, které jsou z vyhrazené IP adresy. Útočník v síti internet nemohou vyřešit provoz na tyto adresy, protože to by kontaktovat Microsoft. Směrovače brány Internet filtrování paketů, které se tak vyřeší, výhradně pro vnitřní adresy, takže by vstupem produkční sítě. Jenom komponenty, které přijímají provoz, který směřuje na virtuální IP adresy jsou nástroje pro vyrovnávání zatížení.
- Brány firewall, které jsou implementovány ve všech uzlech interní mají tři primární architektura důležité informace o zabezpečení pro jakékoli situaci:

   - Brány firewall jsou umístěny za nástrojem pro vyrovnávání zatížení a příjem paketů z libovolného místa. Tyto pakety jsou určeny externě zpřístupní a otevřené porty v bráně firewall hraniční tradiční odpovídá.
   - Brány firewall příjem paketů pouze z omezenou sadu adres. Tento faktor je součástí obranné strategii podrobné před útoky DDoS. Tato připojení jsou kryptograficky ověřit.
   - Brány firewall můžete přistupovat pouze z interní vyberte uzly. Přijetí pakety pouze z výčtový seznam zdrojové IP adresy, které jsou vyhrazené IP adresy v síti Azure. Například útoku v podnikové síti může směrovat požadavky na tyto adresy, ale útoky by se zablokovaly, pokud zdrojová adresa z paketu nebyl v seznamu výčtu v rámci sítě Azure.
     - Směrovač přístupu v hraniční síti blokuje odchozí pakety, které jsou určeny pro adresu, která se nachází uvnitř sítě Azure z důvodu jeho nakonfigurované statické trasy.

## <a name="next-steps"></a>Další postup
Další informace o Microsoft nemá pro zabezpečení infrastruktury Azure, najdete v tématech:

- [Zařízení Azure, místním prostředí a fyzické zabezpečení](azure-physical-security.md)
- [Dostupnost infrastruktury Azure](azure-infrastructure-availability.md)
- [Součásti systému Azure informace a hranice](azure-infrastructure-components.md)
- [Architektura sítě Azure](azure-infrastructure-network.md)
- [Funkce zabezpečení Azure SQL Database](azure-infrastructure-sql.md)
- [Operace Azure výroby a správu](azure-infrastructure-operations.md)
- [Monitorování infrastruktury Azure](azure-infrastructure-monitoring.md)
- [Integrity infrastruktury Azure](azure-infrastructure-integrity.md)
- [Ochrana dat zákazníků Azure](azure-protection-of-customer-data.md)
