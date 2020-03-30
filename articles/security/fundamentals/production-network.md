---
title: Produkční síť Azure
description: Tento článek obsahuje obecný popis produkční sítě Azure.
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 7c0748e4ff1531649274834cb1e602c228f102e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726694"
---
# <a name="the-azure-production-network"></a>Produkční síť Azure
Mezi uživatele produkční sítě Azure patří jak externí zákazníci, kteří mají přístup k vlastním aplikacím Azure, tak interní pracovníci podpory Azure, kteří spravují produkční síť. Tento článek popisuje metody přístupu k zabezpečení a mechanismy ochrany pro navazování připojení k produkční síti Azure.

## <a name="internet-routing-and-fault-tolerance"></a>Internet směrování a odolnost proti chybám
Globálně redundantní interní a externí infrastruktura DNS (Azure Domain Name Service) v kombinaci s více primárními a sekundárními serverovými clustery DNS poskytuje odolnost proti chybám. Současně další ovládací prvky zabezpečení sítě Azure, jako je například NetScaler, se používají k prevenci útoků distribuovaného odmítnutí služby (DDoS) a ochraně integrity služeb Azure DNS.

Servery Azure DNS jsou umístěné ve více zařízeních datového centra. Implementace Azure DNS zahrnuje hierarchii sekundárních a primárních serverů DNS k veřejnému překladu názvů domén zákazníků Azure. Názvy domén se obvykle přemisťují na CloudApp.net adresu, která zalomí adresu virtuální IP adresy (VIP) pro službu zákazníka. Jedinečný pro Azure, VIP, který odpovídá interní vyhrazené IP (DIP) adresu překladu klienta se provádí vykladače zatížení Microsoft udatné za tuto vip.

Azure je hostovaný v geograficky distribuovaných datových centrech Azure v USA a je založený na nejmodernějších platformách směrování, které implementují robustní škálovatelné architektonické standardy. Mezi pozoruhodné rysy patří:

- Víceprotokolové přepínání štítků (MPLS) založené na provozu, které poskytuje efektivní využití propojení a bezproblémové zhoršení služby v případě výpadku.
- Sítě jsou implementovány s "potřeba plus jedna" (N + 1) redundance architektury nebo lepší.
- Datacentra jsou externě obsluhována vyhrazenými síťovými okruhy s velkou šířkou pásma, které redundantně spojují vlastnosti s více než 1 200 poskytovateli internetových služeb po celém světě ve více partnerských bodech. Toto připojení poskytuje více než 2 000 gigabajtů za sekundu (GB/s) kapacity hrany.

Vzhledem k tomu, že Microsoft vlastní vlastní síťové okruhy mezi datovými centry, tyto atributy pomáhají nabídce Azure dosáhnout 99,9% dostupnosti sítě bez nutnosti tradičních poskytovatelů internetových služeb třetích stran.

## <a name="connection-to-production-network-and-associated-firewalls"></a>Připojení k produkční síti a přidruženým firewallům
Zásady toku toku síťového provozu Azure směruje provoz do produkční sítě Azure, která se nachází v nejbližším regionálním datovém centru v USA. Vzhledem k tomu, že produkční datová centra Azure udržují konzistentní síťovou architekturu a hardware, popis toku provozu, který následuje, platí konzistentně pro všechna datová centra.

Po směrování internetového provozu pro Azure do nejbližšího datového centra se nastavuje připojení k přístupovým směrovačům. Tyto přístupové směrovače slouží k izolování provozu mezi uzly Azure a virtuálními počítači instancemi zákazníka. Zařízení síťové infrastruktury v umístění přístupu a hrany jsou hraniční body, kde jsou použity filtry příchozího a odchozího přenosu dat. Tyto směrovače jsou konfigurovány prostřednictvím vrstveného seznamu řízení přístupu (ACL) pro filtrování nežádoucího síťového provozu a v případě potřeby použití omezení rychlosti provozu. Provoz, který je povolen acl je směrován do vykladačů zatížení. Distribuční směrovače jsou navrženy tak, aby umožňovaly pouze adresy IP schválené společností Microsoft, poskytovaly krytí proti falšování a navazovaly připojení TCP, která používají protokoly ACL.

Externí zařízení pro vyrovnávání zatížení jsou umístěna za přístupovými směrovači, aby bylo účelem provádění překladu síťových adres (NAT) z internetových adres IP na interní IP adresy Azure. Zařízení také směrují pakety do platných produkčních interních IP adres a portů a fungují jako ochranný mechanismus pro omezení vystavení adresního prostoru interní produkční sítě.

Ve výchozím nastavení společnost Microsoft vynucuje protokol HTTPS (HTTP) pro veškerý provoz přenášený do webových prohlížečů zákazníků, včetně přihlášení a veškerého provozu poté. Použití TLS v1.2 umožňuje bezpečné tunelové propojení pro protékat. Počet AVL na přístupových a základních směrovačích zajišťují, že zdroj provozu je konzistentní s očekávaným.

Důležitým rozdílem v této architektuře, když je ve srovnání s tradiční architekturou zabezpečení, je, že neexistují žádné vyhrazené hardwarové brány firewall, specializovaná zařízení pro detekci nebo prevenci vniknutí nebo jiná bezpečnostní zařízení, která jsou obvykle očekává, že před připojení k produkčnímu prostředí Azure. Zákazníci obvykle očekávají tato hardwarová zařízení brány firewall v síti Azure; však žádné jsou zaměstnáni v rámci Azure. Téměř výhradně jsou tyto funkce zabezpečení integrovány do softwaru, který spouští prostředí Azure, aby poskytoval robustní vícevrstvé mechanismy zabezpečení, včetně možností brány firewall. Kromě toho rozsah hranice a přidružené rozrůstání kritických zabezpečovacích zařízení je jednodušší spravovat a inventarizovat, jak je znázorněno na předchozím obrázku, protože je spravován softwarem, který běží Azure.

## <a name="core-security-and-firewall-features"></a>Základní funkce zabezpečení a brány firewall
Azure implementuje robustní funkce zabezpečení softwaru a brány firewall na různých úrovních k vynucení funkcí zabezpečení, které se obvykle očekávají v tradičním prostředí k ochraně základní hranice autorizace zabezpečení.

### <a name="azure-security-features"></a>Funkce zabezpečení Azure
Azure implementuje hostitelské softwarové brány firewall uvnitř produkční sítě. V základním prostředí Azure se nachází několik základních funkcí zabezpečení a brány firewall. Tyto funkce zabezpečení odrážejí strategii hloubkové ochrany v prostředí Azure. Zákaznická data v Azure jsou chráněna následujícími firewally:

**Brána firewall hypervisoru (filtr paketů)**: Tato brána firewall je implementována v hypervisoru a konfigurována agentem řadiče prostředků (FABRIC). Tato brána firewall chrání klienta, který běží uvnitř virtuálního počítače před neoprávněným přístupem. Ve výchozím nastavení při vytvoření virtuálního aplikace je veškerý provoz blokován a pak agent FC přidá pravidla a výjimky ve filtru povolit autorizovaný provoz.

Zde jsou naprogramovány dvě kategorie pravidel:

- **Pravidla konfigurace počítače nebo infrastruktury**: Ve výchozím nastavení je blokována veškerá komunikace. Existují výjimky, které umožňují virtuálnímu virtuálnímu počítače odesílat a přijímat komunikace a informace o SLUŽBĚ DNS (DHCP) protokolu DHCP (Dhcp) a odesílat přenosy do "veřejného" odchozího internetu do jiných virtuálních počítačů v rámci clusteru FC a aktivačního serveru operačního systému. Vzhledem k tomu, že seznam povolených odchozích cílů virtuálních počítačů neobsahuje podsítě směrovače Azure a další vlastnosti Microsoftu, pravidla pro ně fungují jako vrstva ochrany.
- **Pravidla konfiguračního souboru role**: Definuje příchozí seznamy AC na základě modelu služby klientů. Například pokud má klient webový front-end na portu 80 na určitém virtuálním počítači, port 80 se otevře pro všechny IP adresy. Pokud má virtuální_ přitým virtuálním msa spuštěná role pracovního procesu, role pracovního procesu se otevře jenom pro virtuální ho v rámci stejného klienta.

**Nativní brána firewall hostitele:** Azure Service Fabric a Azure Storage běží na nativním osu, který nemá žádný hypervisor, a proto je brána Windows Firewall nakonfigurovaná s předchozími dvěma sadami pravidel.

**Brána firewall hostitele**: Brána firewall hostitele chrání oddíl hostitele, který spouští hypervisor. Pravidla jsou naprogramována tak, aby umožňovala pouze FC a jump boxy mluvit s hostitelským oddílem na konkrétním portu. Dalšími výjimkami jsou povolení odpovědí DHCP a odpovědí DNS. Azure používá konfigurační soubor počítače, který obsahuje šablonu pravidel brány firewall pro oddíl hostitele. Existuje také výjimka brány firewall hostitele, která umožňuje virtuálním mům komunikovat s hostitelskými součástmi, drátovým serverem a serverem metadat prostřednictvím konkrétního protokolu/portů.

**Brána firewall pro hosta**: Část počítače S bránou Windows firewall hostovaného operačního systému, kterou zákazníci konfigurovat na virtuálních počítačích a úložišti zákazníků.

Mezi další funkce zabezpečení, které jsou integrované do funkcí Azure, patří:

- Součásti infrastruktury, kterým jsou přiřazeny adresy IP, které pocházejí z programů DIPs. Útočník na internetu nemůže na tyto adresy adresovat provoz, protože by se nedostal k společnosti Microsoft. Směrovače internetových bran filtrují pakety, které jsou adresovány výhradně interním adresám, takže by nevstoupily do produkční sítě. Jediné součásti, které přijímají provoz, který je směrován na VIP jsou vykladače zatížení.
- Brány firewall, které jsou implementovány na všech interních uzlech, mají pro daný scénář tři důležité informace o primární architektuře zabezpečení:

   - Brány firewall jsou umístěny za vyrovnáváním zatížení a přijímají pakety odkudkoli. Tyto pakety jsou určeny k externě vystaveny a by odpovídaly otevřené porty v tradiční obvodové brány firewall.
   - Brány firewall přijímají pakety pouze z omezené sady adres. Tato úvaha je součástí obranné hloubkové strategie proti Útokům DDoS. Taková připojení jsou kryptograficky ověřena.
   - Brány firewall lze přistupovat pouze z vybraných interních uzlů. Přijímají pakety pouze ze seznamu zdrojových IP adres, které jsou všechny dips v rámci sítě Azure. Útok na podnikovou síť může například směrovat požadavky na tyto adresy, ale útoky by byly blokovány, pokud by zdrojová adresa paketu nebyla jedna ve seznamu ve výčtu v síti Azure.
     - Přístupový směrovač na obvodu blokuje odchozí pakety, které jsou adresovány na adresu, která je uvnitř sítě Azure z důvodu nakonfigurovaných statických tras.

## <a name="next-steps"></a>Další kroky
Další informace o tom, co Microsoft dělá pro zabezpečení infrastruktury Azure, najdete v tématu:

- [Zařízení Azure, prostory a fyzické zabezpečení](physical-security.md)
- [Dostupnost infrastruktury Azure](infrastructure-availability.md)
- [Součásti a hranice informačního systému Azure](infrastructure-components.md)
- [Architektura sítě Azure](infrastructure-network.md)
- [Funkce zabezpečení Azure SQL Database](infrastructure-sql.md)
- [Produkční operace a správa Azure](infrastructure-operations.md)
- [Monitorování infrastruktury Azure](infrastructure-monitoring.md)
- [Integrita infrastruktury Azure](infrastructure-integrity.md)
- [Ochrana zákaznických dat Azure](protection-customer-data.md)
