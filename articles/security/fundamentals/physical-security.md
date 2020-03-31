---
title: Fyzické zabezpečení datových center Azure – Microsoft Azure | Dokumenty společnosti Microsoft
description: Článek popisuje, co Microsoft dělá pro zabezpečení datových center Azure, včetně fyzické infrastruktury, zabezpečení a nabídky dodržování předpisů.
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
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: 634f89b3123902d981ad07dd1404315387104322
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68726728"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Zařízení Azure, prostory a fyzické zabezpečení
Tento článek popisuje, co Microsoft dělá pro zabezpečení infrastruktury Azure.

## <a name="datacenter-infrastructure"></a>Infrastruktura datového centra
Azure se skládá z [globálně distribuované infrastruktury datových center](https://azure.microsoft.com/global-infrastructure/), která podporuje tisíce online služeb a zahrnuje více než 100 vysoce zabezpečených zařízení po celém světě.

Infrastruktura je navržena tak, aby aplikace přibližovala uživatelům po celém světě, zachovala rezidenci dat a nabízela zákazníkům komplexní možnosti dodržování předpisů a odolnosti. Azure má 52 oblastí po celém světě a je k dispozici ve 140 zemích nebo oblastech.

Oblast je sada datových center, která je propojena prostřednictvím masivní a odolné sítě. Síť ve výchozím nastavení zahrnuje distribuci obsahu, vyrovnávání zatížení, redundanci a šifrování. Díky více globálním oblastem než kterýkoli jiný poskytovatel cloudu vám Azure poskytuje flexibilitu při nasazování aplikací tam, kde je potřebujete.

Oblasti Azure jsou uspořádány do zeměpisných oblastí. Azure geografie zajišťuje, že data rezidence, suverenity, dodržování předpisů a odolnost i požadavky odolnosti jsou dodržovány v rámci geografických hranic.

Zeměpisné oblasti umožňují zákazníkům se specifickými požadavky na rezidenci dat a dodržování předpisů, aby měli svoje data a aplikace blízko. Zeměpisné oblasti jsou odolné proti chybám, aby odolaly úplnému selhání oblasti prostřednictvím jejich připojení k vyhrazené, vysokokapacitní síťové infrastruktuře.

Zóny dostupnosti jsou fyzicky samostatná umístění v rámci oblasti Azure. Každou zónu dostupnosti tvoří jedno nebo několik datových center vybavených nezávislým napájením, chlazením a sítí. Zóny dostupnosti umožňují spouštět kritické aplikace s vysokou dostupností a replikací s nízkou latencí.

Následující obrázek ukazuje, jak se oblast globální infrastruktury Azure a zóny dostupnosti v rámci stejné hranice rezidence dat pro vysokou dostupnost, zotavení po havárii a zálohování.

![Diagram znázorňující hranici rezidence dat](./media/physical-security/data-residency-boundary.png)

Geograficky distribuovaná datová centra umožňují společnosti Microsoft být blízko zákazníkům, snížit latenci sítě a umožnit geograficky redundantní zálohování a převzetí služeb při selhání.

## <a name="physical-security"></a>Fyzické zabezpečení
Společnost Microsoft navrhuje, vytváří a provozuje datová centra způsobem, který přísně řídí fyzický přístup k oblastem, kde jsou data uložena. Společnost Microsoft chápe důležitost ochrany vašich dat a zavazuje se pomáhat zabezpečit datová centra, která obsahují vaše data. V Microsoftu máme celou divizi, která se věnuje navrhování, vytváření a provozování fyzických zařízení podporujících Azure. Tento tým je investován do udržování nejmodernější fyzické bezpečnosti.

Společnost Microsoft zaujímá vrstvený přístup k fyzickému zabezpečení, aby se snížilo riziko, že neoprávnění uživatelé získají fyzický přístup k datům a prostředkům datového centra. Datová centra spravovaná společností Microsoft mají rozsáhlé vrstvy ochrany: schválení přístupu na obvodu zařízení, na obvodu budovy, uvnitř budovy a na podlaze datového centra. Úrovně fyzického zabezpečení jsou:

- **Žádost o přístup a schválení.** Před příjezdem do datového centra musíte požádat o přístup. Jste povinni poskytnout platné obchodní odůvodnění vaší návštěvy, jako jsou účely dodržování předpisů nebo auditování. Všechny požadavky jsou schváleny na základě potřeby přístupu zaměstnanci společnosti Microsoft. Základ pro přístup k potřebám pomáhá udržet počet osob potřebných k dokončení úkolu v datových centrech na minimum. Poté, co společnost Microsoft udělí oprávnění, má jednotlivec přístup pouze k samostatné oblasti požadovaného datového centra na základě schváleného obchodního odůvodnění. Oprávnění jsou omezena na určitou dobu a pak vyprší.

- **Perimetr zařízení.** Když dorazíte do datového centra, musíte projít dobře definovaným přístupovým bodem. Typicky, vysoké ploty z oceli a betonu zahrnují každý centimetr obvodu. Kolem datových center jsou kamery, kde bezpečnostní tým neustále sleduje jejich videa.

- **Vchod do budovy.** Vstup do datového centra je vybaven profesionálními bezpečnostními pracovníky, kteří prošli přísným školením a ověřením spolehlivosti. Tito bezpečnostní pracovníci také běžně hlídkují v datovém centru a neustále monitorují videa kamer uvnitř datového centra.

- **Uvnitř budovy.** Po vstupu do budovy musíte projít dvoufaktorovým ověřováním s biometrickými údaji, abyste pokračovali v pohybu přes datové centrum. Pokud je vaše identita ověřena, můžete zadat pouze část datového centra, ke kterému jste schválili přístup. Můžete tam zůstat pouze po dobu schválené.

- **Podlaha datového centra.** Máte povoleno pouze na podlahu, na kterou máte povolení vstoupit. Jste povinni projít celotělové detekce kovů. Aby se snížilo riziko neoprávněného vstupu nebo opuštění datového centra bez našeho vědomí, mohou se do podlahy datového centra dostat pouze schválená zařízení. Kromě toho videokamery monitorují přední a zadní stranu každého serverového racku. Při ukončení podlahy datového centra, musíte znovu projít full body detekce kovů. Chcete-li datové centrum opustit, musíte projít další bezpečnostní prohledávací.

Společnost Microsoft vyžaduje, aby se návštěvníci vzdali odznaky při odjezdu z jakéhokoli zařízení společnosti Microsoft.

## <a name="physical-security-reviews"></a>Kontroly fyzického zabezpečení
Pravidelně provádíme fyzické kontroly zabezpečení zařízení, abychom zajistili, že datová centra budou správně řešit požadavky na zabezpečení Azure. Pracovníci poskytovatele hostingu datového centra neposkytují správu služeb Azure. Personál se nemůže přihlásit k systémům Azure a nemají fyzický přístup k místnosti a klece azure společného umístění.

## <a name="data-bearing-devices"></a>Datová ložisková zařízení
Společnost Microsoft používá postupy osvědčených postupů a řešení stírání, které je [kompatibilní s NIST 800-88](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01). Pro pevné disky, které nelze vymazat, používáme proces ničení, který ji zničí a znemožní obnovení informací. Tento proces ničení může být rozpad, drcení, rozdrcení nebo spálení. Způsob vyřazení určujeme podle typu majetku. Uchováváme záznamy o zničení.  

## <a name="equipment-disposal"></a>Likvidace zařízení
Po skončení životnosti systému se provozní pracovníci společnosti Microsoft řídí přísnými postupy zpracování dat a likvidace hardwaru, aby zajistili, že hardware obsahující vaše data nebude k dispozici nedůvěryhodným stranám. Používáme bezpečný přístup k vymazání pro pevné disky, které jej podporují. U pevných disků, které nelze vymazat, používáme proces zničení, který disk zničí a znemožní obnovení informací. Tento proces ničení může být rozpad, drcení, rozdrcení nebo spálení. Způsob vyřazení určujeme podle typu majetku. Uchováváme záznamy o zničení. Všechny služby Azure využívají schválené služby správy úložiště a likvidace médií.

## <a name="compliance"></a>Dodržování předpisů
Navrhujeme a spravujeme infrastrukturu Azure tak, aby splňovala širokou škálu mezinárodních a oborově specifických standardů dodržování předpisů, jako jsou ISO 27001, HIPAA, FedRAMP, SOC 1 a SOC 2. Splňujeme také standardy specifické pro jednotlivé země nebo oblasti, včetně Austrálie IRAP, UK G-Cloud a Singapur MTCS. Přísné audity třetích stran, jako jsou audity provedené Britským institutem pro normalizaci, ověřují dodržování přísných bezpečnostních kontrol, které tyto standardy vyžadují.

Úplný seznam standardů dodržování předpisů, které Azure dodržuje, najdete v [nabídce dodržování předpisů](https://www.microsoft.com/trustcenter/compliance/complianceofferings).

## <a name="next-steps"></a>Další kroky
Další informace o tom, co Microsoft dělá, aby zabezpečil infrastrukturu Azure, najdete v tématu:

- [Dostupnost infrastruktury Azure](infrastructure-availability.md)
- [Součásti a hranice informačního systému Azure](infrastructure-components.md)
- [Architektura sítě Azure](infrastructure-network.md)
- [Produkční síť Azure](production-network.md)
- [Funkce zabezpečení Azure SQL Database](infrastructure-sql.md)
- [Produkční operace a správa Azure](infrastructure-operations.md)
- [Monitorování infrastruktury Azure](infrastructure-monitoring.md)
- [Integrita infrastruktury Azure](infrastructure-integrity.md)
- [Ochrana zákaznických dat Azure](protection-customer-data.md)


