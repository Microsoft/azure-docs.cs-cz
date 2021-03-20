---
title: Produkční síť Azure
description: Přečtěte si informace o produkční síti Azure. Viz metody přístupu zabezpečení a mechanismy ochrany pro navázání připojení k síti.
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
ms.openlocfilehash: 724c2ee2d74ca9cab11280e92faac71c2cb2218e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87543805"
---
# <a name="the-azure-production-network"></a>Produkční síť Azure
Uživatelé produkční sítě Azure zahrnují externí zákazníky, kteří přistupují k vlastním aplikacím Azure a interním pracovníkům podpory Azure, kteří spravují produkční síť. Tento článek popisuje metody přístupu zabezpečení a mechanismy ochrany pro navázání připojení k produkční síti Azure.

## <a name="internet-routing-and-fault-tolerance"></a>Směrování v Internetu a odolnost proti chybám
V kombinaci s několika primárními a sekundárními clustery serverů DNS zajišťuje odolnost proti chybám globálně redundantní interní a externí infrastruktura služby Azure Domain Name Service (DNS). V současné době se k tomu používají další ovládací prvky zabezpečení sítě Azure, jako je například NetScaler, aby se zabránilo distribuovaným útokům na útoky DDoS (Denial of Service) a chránily celistvost Azure DNS služeb.

Servery Azure DNS jsou umístěné na více zařízeních Datacenter. Implementace Azure DNS zahrnuje hierarchii sekundárních a primárních serverů DNS pro veřejně přeložení názvů domén zákazníků Azure. Názvy domén se obvykle překládají na adresu CloudApp.net, která zabalí virtuální IP adresu (VIP) pro službu zákazníka. V rámci Azure jsou jedinečné virtuální IP adresy, které odpovídají interní vyhrazené IP adrese (DIP) překladu tenanta, vychází ze služeb Microsoft Load Balancer zodpovědných za tuto VIP.

Azure se hostuje v geograficky distribuovaných datových centrech Azure v USA a je postavená na špičkových platformách směrování, které implementují robustní a škálovatelné standardy architektury. Mezi významné funkce patří:

- Technický provoz založený na přepínání MPLS (), který poskytuje efektivní využití propojení a řádné snížení úrovně služeb v případě výpadku.
- Sítě se implementují s "potřebnou a jednu" (N + 1) architekturou redundance nebo lepší.
- Mimo jiné jsou datová centra obsluhována vyhrazenými síťovými okruhy s vysokou šířkou pásma, které redundantním připojením vlastností s více než 1 200 poskytovateli internetových služeb globálně na několika partnerských bodech. Toto připojení zajišťuje vyšší kapacitu hraniční kapacity 2 000 gigabajtů za sekundu (GB/s).

Vzhledem k tomu, že společnost Microsoft vlastní síťové okruhy mezi datovými centry, tyto atributy pomůžou zajistit dostupnost sítě v Azure 99,9 + Percent bez nutnosti tradičních poskytovatelů internetových služeb jiných výrobců.

## <a name="connection-to-production-network-and-associated-firewalls"></a>Připojení k produkční síti a přidruženým branám firewall
Zásady toku internetového provozu sítě Azure směrují provoz do produkční sítě Azure, která je umístěná v nejbližším regionálním datacentru v USA. Vzhledem k tomu, že produkční datacentra Azure udržují konzistentní síťovou architekturu a hardware, následující popis toku přenosů platí konzistentně pro všechna datová centra.

Až bude internetový provoz pro Azure směrován do nejbližšího datového centra, připojení k směrovačům přístupu se naváže. Tyto přístupové směrovače slouží k izolaci provozu mezi uzly Azure a virtuálními počítači vytvořenými zákazníky. Zařízení síťové infrastruktury v oblasti přístupu a hraničních umístění jsou hraniční body, ve kterých se používají příchozí a výstupní filtry. Tyto směrovače se konfigurují prostřednictvím vrstveného seznamu řízení přístupu (ACL) pro filtrování nežádoucích síťových přenosů a v případě potřeby platí omezení přenosové rychlosti. Provoz povolený seznamem ACL je směrován do nástroje pro vyrovnávání zatížení. Distribuční směrovače jsou navržené tak, aby povolovaly jenom IP adresy schválené společností Microsoft, poskytovaly ochranu proti falšování identity a navázaly připojení TCP, která používají seznamy ACL.

Externí zařízení pro vyrovnávání zatížení se nacházejí za směrovači přístupu, aby bylo možné provést překlad síťových adres (NAT) z IP adres pro směrování přes Internet do interních IP adres Azure. Zařízení také směrují pakety k platným interním IP adresám a portům a slouží jako mechanismus ochrany pro omezení vystavení interního adresního prostoru produkční sítě.

Ve výchozím nastavení společnost Microsoft vynutila protokol HTTPS (Hypertext Transfer Protocol Secure) pro veškerý provoz, který se přenáší do webových prohlížečů zákazníků, včetně přihlášení a následného provozu. Použití protokolu TLS v 1.2 umožňuje zabezpečené tunelové propojení pro přenos přes. Seznamy ACL pro přístup a základní směrovače zajišťují, že zdroj provozu je konzistentní s tím, co se očekává.

Důležité rozlišení v této architektuře, pokud je v porovnání s tradiční architekturou zabezpečení, je, že neexistují žádné vyhrazené hardwarové brány firewall, speciální zařízení pro detekci vniknutí nebo prevence nebo jiná zařízení zabezpečení, která se obvykle očekávají před tím, než se provedou připojení k produkčnímu prostředí Azure. Zákazníci obvykle očekávají tato hardwarová zařízení brány firewall v síti Azure. v rámci Azure se ale nic nepoužívá. Prakticky výhradně tyto funkce zabezpečení jsou součástí softwaru, který spouští prostředí Azure pro zajištění robustních vícevrstvých mechanismů zabezpečení, včetně možností brány firewall. Kromě toho je možné zjednodušit správu a inventarizaci rozsahu hranic a přidružených neustálému zvětšování zařízení zabezpečení, jak je znázorněno na předchozím obrázku, protože je spravovaný softwarem, na kterém běží Azure.

## <a name="core-security-and-firewall-features"></a>Základní funkce zabezpečení a brány firewall
Azure implementuje robustní zabezpečení softwaru a funkce brány firewall na různých úrovních, aby vynutila funkce zabezpečení, které se obvykle očekávají v tradičním prostředí, aby se chránila základní hranice autorizace zabezpečení.

### <a name="azure-security-features"></a>Funkce zabezpečení Azure
Azure implementuje v produkční síti brány firewall na základě hostitelských softwaru. V základním prostředí Azure se nachází několik základních funkcí zabezpečení a brány firewall. Tyto bezpečnostní funkce odrážejí strategii pro důkladnou hloubku v rámci prostředí Azure. Zákaznická data v Azure jsou chráněná následujícími branami firewall:

**Firewall hypervisoru (filtr paketů)**: Tato brána firewall je implementovaná v hypervisoru a nakonfigurovaná agentem technologie Fabric (FC). Tato brána firewall chrání klienta, který běží ve virtuálním počítači, před neoprávněným přístupem. Ve výchozím nastavení platí, že když se vytvoří virtuální počítač, zablokuje se veškerý provoz a Agent FC přidá do filtru pravidla a výjimky, které umožní autorizovaný provoz.

Tady jsou naprogramované dvě kategorie pravidel:

- **Konfigurace počítače nebo pravidla infrastruktury**: ve výchozím nastavení se zablokuje veškerá komunikace. Existují výjimky, které umožňují virtuálnímu počítači odesílat a přijímat informace o komunikaci protokolu DHCP (Dynamic Host Configuration Protocol) a DNS a odesílat provoz do odchozího internetového výstupu do dalších virtuálních počítačů v clusteru FC a na aktivačním serveru operačního systému. Vzhledem k tomu, že seznam povolených odchozích umístění virtuálních počítačů neobsahuje podsítě směrovačů Azure a další vlastnosti Microsoftu, pravidla slouží jako vrstva obrany pro ně.
- **Pravidla konfiguračního souboru role**: definuje příchozí seznamy ACL na základě modelu služby klientů. Například pokud má tenant webový front-end na portu 80 na určitém virtuálním počítači, otevře se port 80 na všech IP adresách. Pokud má virtuální počítač spuštěnou roli pracovního procesu, je role pracovního procesu otevřená jenom pro virtuální počítač v rámci stejného tenanta.

**Nativní hostitelská brána firewall**: Azure Service Fabric a Azure Storage běžet na NATIVNÍM operačním systému, který nemá žádný hypervisor a proto je brána Windows Firewall nakonfigurovaná s předchozími dvěma sadami pravidel.

**Hostitelská brána firewall**: hostitelská brána firewall chrání oddíl hostitele, který spouští hypervisor. Tato pravidla jsou naprogramována tak, aby umožňovala komunikaci s hostitelským oddílem na určitém portu pouze v polích FC a skok. Další výjimkou je povolení odpovědí DHCP a odpovědí DNS. Azure používá konfigurační soubor počítače, který obsahuje šablonu pravidel brány firewall pro oddíl hostitele. Existuje taky výjimka brány firewall hostitele, která umožňuje virtuálním počítačům komunikovat s komponentami hostitele, komunikačním serverem a serverem metadat prostřednictvím konkrétního protokolu nebo portů.

**Brána firewall hosta**: součást brány Windows Firewall HOSTOVANÉHO operačního systému, kterou zákazníci můžou nakonfigurovat na virtuálním počítači a úložišti zákazníků.

Mezi další funkce zabezpečení, které jsou součástí možností Azure, patří:

- Součásti infrastruktury, kterým jsou přiřazeny IP adresy, které jsou z DIP. Útočník na Internetu nemůže adresovat provoz na tyto adresy, protože by nedorazil na společnost Microsoft. Směrovače internetové brány filtrují pakety, které jsou adresovány výhradně na interní adresy, takže by nemuseli vstoupit do produkční sítě. Jedinými součástmi, které přijímají přenosy směrované na VIP, jsou nástroje pro vyrovnávání zatížení.
- Brány firewall implementované na všech interních uzlech mají tři primární požadavky na architekturu zabezpečení pro libovolný daný scénář:

   - Brány firewall jsou umístěné za nástrojem pro vyrovnávání zatížení a přijímají pakety odkudkoli. Tyto pakety jsou určené k externímu zpřístupnění a by odpovídaly otevřeným portům v tradiční hraniční bráně firewall.
   - Brány firewall přijímají pakety jenom z omezené sady adres. Tato úvaha je součástí obrannou linií hloubkové strategie proti útokům DDoS. Taková připojení se kryptograficky ověřují.
   - K bránám firewall se dá dostat jenom z vybraných interních uzlů. Přijímají pakety jenom z výčtového seznamu zdrojových IP adres, z nichž všechny jsou v síti Azure vyhrazené. Například útok na podnikovou síť může směrovat požadavky na tyto adresy, ale útoky by se zablokovaly, pokud by nebyla zdrojová adresa paketu v seznamu výčtu v rámci sítě Azure.
     - Směrovač přístupu na hraničních blocích odchozí pakety, které jsou adresovány na adresu, která je v síti Azure, z důvodu konfigurovaných statických tras.

## <a name="next-steps"></a>Další kroky
Další informace o tom, co Microsoft dělá k zabezpečení infrastruktury Azure, najdete tady:

- [Zařízení, místní a fyzické zabezpečení Azure](physical-security.md)
- [Dostupnost infrastruktury Azure](infrastructure-availability.md)
- [Komponenty a hranice informačních systémů Azure](infrastructure-components.md)
- [Architektura sítě Azure](infrastructure-network.md)
- [Funkce zabezpečení Azure SQL Database](infrastructure-sql.md)
- [Provozní provoz a Správa Azure](infrastructure-operations.md)
- [Monitorování infrastruktury Azure](infrastructure-monitoring.md)
- [Integrita infrastruktury Azure](infrastructure-integrity.md)
- [Ochrana zákaznických dat Azure](protection-customer-data.md)
