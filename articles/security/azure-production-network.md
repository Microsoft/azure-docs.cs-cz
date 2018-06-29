---
title: Produkční sítě Azure
description: Tento článek obsahuje obecný popis produkční sítě Microsoft Azure.
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
ms.openlocfilehash: 5c0bfae35464e73278a1efd9c04a03123bb9018a
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102154"
---
# <a name="azure-production-network"></a>Produkční sítě Azure
Uživatelé produkční sítě Azure zahrnují přístup svých vlastních aplikací Microsoft Azure, jakož i interní pracovníky podpory Microsoft Azure, které spravují produkční síti externí zákazníky. V tomto článku jsou uvedeny metody pro přístup k zabezpečení a ochrany mechanismy pro navazování připojení k Azure produkční sítě.

## <a name="internet-routing-and-fault-tolerance"></a>Směrování Internet a odolnost proti chybám
Zadejte globální redundantní infrastruktury Microsoft Azure Domain Name Service (WADNS) interních a externích společně s více primárních a sekundárních clustery serverů služby DNS (Domain Name) pro odolnost proti chybám při další sítě Microsoft Azure ovládací prvky zabezpečení, jako je NetScaler lze zabránit útokům Distributed Denial of Service (DDoS) a chránit integritu služby Microsoft Azure DNS.

Servery WADNS nacházejí ve více zařízeních a datacenter. Implementace WADNS zahrnuje hierarchii serverů DNS, sekundární nebo primární veřejně překládat názvy domény Azure zákazníků. Názvy domén obvykle odkazující na adresu CloudApp.net, která zabalí virtuální IP (VIP) Adresa služby zákazníka. Jedinečné do Azure, virtuální IP adresu odpovídající vnitřní vyhrazené IP (DIP) adresu překlad klienta se provádí vyrovnávání zatížení Microsoft zodpovědná za tento VIP.

Azure je hostována v geograficky distribuované datových centrech Azure v USA a je založená na směrování platformy stavu techniky implementace robustní a škálovatelné architektury standardy. Mezi významné funkce patří:

- Víceprotokolové přepínání popisek (MPLS) na základě provoz inženýrství poskytuje odkaz efektivní využití a řádné snížení výkonu služby, pokud dojde k výpadku
- Sítě jsou implementované v "nutné plus jedna" (N + 1) redundance architektur nebo lepší.
- Externě jsou datová centra obsloužených okruhy vyhrazené, velkou šířku pásma sítě, které redundantně spojte se s více než 1 200 poskytovatelům internetových služeb globálně v několika partnerského vztahu bodech poskytuje více než 2 000 GB za sekundu (Gbps) z vlastnosti hraniční kapacity.

Jako Microsoft vlastní vlastní sítě okruhů mezi datovými centry, tyto atributy pomáhají nabídku Azure dosáhnout 99,9 + % dostupnost sítě bez nutnosti tradiční poskytovatelům internetových služeb třetích stran.

## <a name="connection-to-production-network-and-associated-firewalls"></a>Připojení k produkční sítě a přidružené brány firewall
Síť Azure zásady tok provozu internetového přesměruje přenosy na Azure produkční síti umístěné v datovém centru nejbližší regionální ve Spojených státech amerických. Vzhledem k tomu, že datových centrech Azure produkční udržovat konzistentní síťovou architekturu a hardwaru, pod tok přenosů popis vztahuje konzistentně na všech datových center.

Jakmile internetový provoz pro Azure se směruje na nejbližší datacenter, je k přístupu k směrovačům navázat připojení. Tyto směrovače přístupu slouží k izolaci provozu mezi uzly Azure a virtuálních počítačů vytvořena instance zákazníka. Síťová infrastruktura zařízení v umístěních přístup a hraniční jsou body hranic, kde jsou použity filtry příchozí nebo odchozí. Tyto směrovače prostřednictvím vrstvené seznam ACL k filtrování nežádoucí síťový provoz a použití omezení přenosové rychlosti provoz, v případě potřeby. Provoz, který je povolen na základě seznamu řízení přístupu se směruje na služby Vyrovnávání zatížení. Distribuce směrovače jsou navrženy pro povolit pouze schválení Microsoft IP adresy, poskytují ochranu proti falšování a zavedené připojení TCP pomocí seznamů řízení přístupu.

Externí zařízení pro vyrovnávání zatížení jsou umístěné za směrovače přístup k překladu adres (NAT) z Internetu směrovatelné IP adresy do Azure interní IP adresy. Také směrovat pakety platný interní IP adresy produkční a porty a fungovat jako ochranný mechanismus pro omezení vystavení interní produkční síť adresní prostor.

Ve výchozím nastavení vynutí Microsoft protokol Secure HTTPS (Hypertext Transfer) pro všechny přenosy přenesená do prohlížeče, včetně přihlášení webová zákazníka a veškerý provoz po tomto datu. Použití protokolu TLS verze 1.2 umožní přes zabezpečené tunelové propojení pro provoz procházet skrz. Seznamy ACL v přístup a základní směrovače Ujistěte se, že zdroj provoz je konzistentní s očekávané.

Zásadní rozdíl v této architektuře ve srovnání s architektura tradiční zabezpečení je, že nejsou žádné brány firewall vyhrazený hardware, specializované narušení detekce nebo zabránění zařízení nebo další zabezpečovací zařízení obvykle měl být před vytváří se připojení do Azure produkčního prostředí. Zákazníci obvykle předpokládají, že tato zařízení brány firewall hardwaru ve síť Azure; ale žádné neexistují pracujících v rámci Azure. Tyto funkce zabezpečení jsou téměř výhradně součástí softwaru spuštěné prostředí Azure zajistit mechanismy robustní víceúrovňová zabezpečení, včetně možnosti brány firewall. Kromě toho je jednodušší je spravovat a inventáře, jak je znázorněno na obrázku výše, protože je spravovaný software spuštěný Azure rozsah hranice a přidružené živelný růst kritické zabezpečení zařízení.

## <a name="core-security-and-firewall-features"></a>Základní funkce zabezpečení a brány firewall
Azure implementuje zabezpečení robustní softwaru a brány firewall funkce na různých úrovních vynutit obvykle očekávají tradičních prostředí k ochraně hranice zabezpečení autorizace základní funkce zabezpečení.

### <a name="azure-security-features"></a>Funkce zabezpečení Azure
Azure implementuje brány firewall založená na hostiteli softwaru uvnitř produkční sítě. Několik zabezpečení jádra a brány firewall funkce jsou umístěny v základní prostředí Azure. Tyto funkce zabezpečení projeví strategie obrany zabezpečení v prostředí Azure. Data zákazníků v Microsoft Azure je chráněn následující brány firewall:

**Brány Firewall hypervisoru (paket filtr)**: Tato brána firewall je implementována v hypervisoru a agentem FC. Tato brána firewall chrání klienta běžících v rámci virtuálního počítače před neoprávněným přístupem. Ve výchozím nastavení při vytvoření virtuálního počítače je blokován veškerý provoz, pak agenta FC přidá pravidla nebo výjimky ve filtru umožňující autorizovaný přenos.

Programují se tady dvě kategorie pravidel:

- Počítač konfigurace nebo pravidla infrastruktury: ve výchozím nastavení, všechna komunikace je zablokovaná. Existují výjimky, které umožňují odesílat a přijímat komunikaci Dynamic Host Configuration Protocol (DHCP), informace o serveru DNS, "veřejná" Internet odchozí do ostatních virtuálních počítačů v rámci clusteru FC a aktivace operačního systému serveru, který odesílá data virtuálního počítače. Vzhledem k tomu, že se virtuální počítače povolená seznam odchozí cíle nezahrnuje podsítě směrovač Microsoft Azure a další vlastnosti Microsoft, pravidla pro jejich fungovat jako vrstva obrany.
- Role konfigurační soubor: Definuje příchozí seznamy ACL založené na modelu služby s klienty. Například pokud klient má front-end webový na portu 80 pro určité virtuálního počítače, port 80 je otevřen pro všechny IP adresy. Pokud virtuální počítač má roli pracovního procesu, který je spuštěn, role pracovního procesu otevřít pouze pro virtuální počítač v rámci stejné klienta.

**Nativní brány Firewall hostitele**: Microsoft Azure Fabric a úložiště spustit na nativní operačního systému, který má žádné hypervisoru a proto je brána Windows Firewall nakonfigurované výše uvedené dvě sady pravidel.

**Brány Firewall hostitele**: bránu firewall hostitele chrání hostitelskému oddílu, který se spouští hypervisoru. Pravidla jsou naprogramovaný tak, aby povolit pouze FC a přejít políčka komunikovat k hostitelskému oddílu na určitém portu. Další výjimkou je povolení odpovědi DHCP a odpovědí DNS. Azure používá soubor na konfigurace počítače, který má šablonu pravidla brány firewall pro oddíl hostitele. Je také výjimka brány firewall hostitele, která umožňuje virtuální počítače komunikovat s hostiteli součásti, serveru přenosová & metadata serveru prostřednictvím určité protokol nebo porty.

**Brány Firewall hosta**: brány Windows Firewall část hostovaného operačního systému, který je možné konfigurovat zákazník na virtuální počítače zákazníka a úložiště.

Další funkce zabezpečení zabudovaná možnosti Azure:

- Součásti infrastruktury jsou přiřazeny IP adresy, které jsou z vyhrazené IP adresy (vyhrazené). Útočník na Internetu nelze adres provoz na tyto adresy, protože by nedostanou společnosti Microsoft. Internetová brána směrovače filtrovat pakety určené výhradně pro interní adresy, tak nebude vstupem produkční sítě. Nástroje pro vyrovnávání zatížení se pouze součásti, které přijímají data na virtuální IP adresy.
- Brány firewall implementována na všech uzlech, interní mít tři primární architektura důležité informace o zabezpečení pro jakékoli této situaci:

   - Tyto jsou umístěny za nástroj pro vyrovnávání zatížení (LB) a příjem paketů z libovolného místa. Tyto jsou určeny k externě zpřístupnit a by odpovídat otevřené porty v bráně firewall hraniční tradiční.
   - Pouze příjem paketů z omezenou sadu adresy. Toto je součástí strategie podrobný obrana proti útoku DOS. Tato připojení jsou kryptograficky ověřit.
   - Brány firewall můžete přistupovat pouze z interní vyberte uzly, v takovém případě přijmou pakety pouze z výčtových seznam zdrojové IP adresy, které jsou vyhrazené IP adresy v síti Azure. Například útoku v podnikové síti může směrovat své žádosti na tyto adresy, ale by se zablokovaly Pokud zdrojové adresy paketu byla v seznamu výčtové v rámci síť Azure.
   - Směrovač přístup v hraniční síti blokuje odchozí pakety odeslané na adresu, která je uvnitř síť Azure z důvodu jeho nakonfigurované statické trasy.

## <a name="next-steps"></a>Další postup
Další informace o funkci Microsoft pro zabezpečení infrastruktury Azure najdete v tématu:

- [Azure zařízení, místní a fyzické zabezpečení](azure-physical-security.md)
- [Dostupnost infrastruktury Azure](azure-infrastructure-availability.md)
- [Součásti systému Azure informace a hranice](azure-infrastructure-components.md)
- [Architektura sítě Azure](azure-infrastructure-network.md)
- [Zabezpečení funkce Microsoft Azure SQL Database](azure-infrastructure-sql.md)
- [Azure produkční operace a Správa](azure-infrastructure-operations.md)
- [Monitorování infrastruktury Azure](azure-infrastructure-monitoring.md)
- [Integritu infrastruktury Azure](azure-infrastructure-integrity.md)
- [Ochrana dat zákazníka v Azure](azure-protection-of-customer-data.md)
