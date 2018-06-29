---
title: Azure zařízení, místní a fyzického zabezpečení | Microsoft Docs
description: Článek popisuje datových centrech Azure, včetně fyzické infrastruktuře, zabezpečení a dodržování předpisů nabídky.
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
ms.openlocfilehash: a6a9b1d6e12dabb09cde684c34481b4b3442c1b8
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102148"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Azure zařízení, místní a fyzické zabezpečení
Společnosti Microsoft cloudu se skládá z [globálně distribuované datacenter infrastruktury](https://azure.microsoft.com/global-infrastructure/) podpora tisíce služeb online services a vysoce pokrývání uzlů více než 100 zabezpečit zařízení po celém světě.

Infrastruktury je určena ke zvýšení blíže aplikace pro uživatele po celém světě, zachování dat sídlo a nabízí rozsáhlé možnosti dodržování předpisů a odolnost pro zákazníky. Azure má 52 oblastech po celém světě a je k dispozici v 140 zemích.

Oblast je sada datových center, které jsou propojeny prostřednictvím sítě masivního a odolný. Síť obsahuje ve výchozím nastavení distribuce obsahu, Vyrovnávání zatížení, redundance a šifrování. Díky většímu počtu globálních oblastí než kterýkoli jiný poskytovatel cloudu Azure zákazníkům zajišťuje flexibilitu při nasazování aplikací tam, kde potřebují.

Oblasti Azure jsou uspořádané do zeměpisných oblastí. Zeměpisná oblast Azure zaručuje, že se v rámci příslušných zeměpisných hranic dodržují požadavky na rezidenci dat, suverenitu, dodržování předpisů a zajištění odolnosti.

Zeměpisné oblasti umožňují zákazníkům se specifickými požadavky na rezidenci dat a dodržování předpisů, aby měli svoje data a aplikace blízko. Zeměpisných jsou odolné proti chybám odolat selhání dokončení oblast prostřednictvím připojení k vyhrazené síťová infrastruktura vysoké kapacity.

Zóny dostupnosti jsou fyzicky oddělená umístění uvnitř oblasti Azure. Každou zónu dostupnosti tvoří jedno nebo několik datových center vybavených nezávislým napájením, chlazením a sítí. Zóny dostupnosti umožňují zákazníkům provozovat důležité aplikace s vysokou dostupností a replikací s nízkou latencí.

Následující obrázek ukazuje, jak infrastrukturu Azure globální páry oblast a dostupnost zóny v rámci stejné hranice sídle dat pro zálohování, vysokou dostupnost a zotavení po havárii.

![Data sídle hranic][1]

Velké zeměpisné distribuované nároků datových Center umožňuje Microsoftu se blíží zákazníkům, aby bylo možné snížit latenci sítě a umožňují geograficky redundantní zálohy a převzetí služeb při selhání.

## <a name="physical-security"></a>Fyzické zabezpečení
Společnost Microsoft návrhy, sestavení a funguje datových centrech způsobem, který řídí výhradně fyzický přístup k oblasti, kde je zákaznická data uložená. Microsoft plně chápe významu ochrany dat zákazníka a se věnuje maximální pozornost zabezpečení datových center, které obsahují data. Máme celý dělení v Microsoftu, které jsou určené pro navrhování, sestavování a provoz fyzického zařízení podporující Azure. Tento tým je investovali do stavu techniky fyzické zabezpečení.

Společnost Microsoft má vrstveného přístupu k fyzickému zabezpečení, aby se snížilo riziko neoprávnění uživatelé získají fyzický přístup k datům a prostředkům datacenter. Spravované společností Microsoft v datových centrech mají rozsáhlé vrstvy ochrany: přístup k schválení na hraniční zařízení, na hraniční do budovy, uvnitř vytvářet a na podlaží datacenter. Vrstvy fyzického zabezpečení jsou:

- Žádost o přístup a schválení – musíte požádat o přístup před přicházejících v datacentru. Jste nutné k zajištění platné obchodního oprávnění pro vaší návštěvě, například dodržování předpisů nebo účely auditování. Všechny žádosti jsou schváleny na základě potřeba přístup zaměstnanců společnosti Microsoft. Základ nutné přístupu pomáhá chránit počet jednotlivce potřebný k dokončení úkolů v datových centrech na úplné minimum. Jakmile je povoleno, konkrétního má přístup pouze pro diskrétní oblast datacenter podle schválené obchodního oprávnění. Oprávnění jsou omezeny na určitou dobu a vyprší po povolené časové období.

- Hraniční zařízení je - až přijedete do datacentru, budete muset projít dobře definovaný přístupový bod. Obvykle výšku ochranné z oceli a konkrétní zahrnovat každý palec hraniční síti. Existují kamery kolem v datacentrech s týmem zabezpečení monitorování jejich videa 24 hodin denně 7 a 365 dnů roku.

- Vytváření vstupu - vstupu datacenter pracovníci s odborníky v oblasti zabezpečení osob, které prošly přísných kontroly školení a pozadí. Tyto osoby zabezpečení také pravidelně patrol datového centra, když je i taky sledování videa kamer uvnitř datového centra 24 hodin denně 7 a 365 dní v roce.

- Uvnitř vytváření - po zadání sestavení, musí projít dvoufaktorové ověřování s biometriku. Chcete-li pokračovat, procházení datového centra. Pokud je ověření vaší identity, můžete zadat část datacentru, které jste schválili přístup k. Abyste mohli zůstat existuje pouze po dobu trvání času schválení.

- Datacenter floor – můžete jsou povoleny pouze do floor, jste schválena k zadání. Je nutné předat prověřování systému detekce úplné body. Aby se snížilo riziko neoprávněného dat zadáním nebo opuštění datového centra bez naše znalostní báze, jenom schválené zařízení provádět jejich představ do datového centra podlaží. Kromě toho videokamer monitorování dopředu a zpět všech serverů do racku. Úplné tělo systému detekce blokování se opakuje při ukončení podlaží datového centra. Do opuštění datového centra, budete vyzváni k předání další bezpečnostní kontrolu.

Návštěvníky vyžadovaných předáním odznaky po opuštění jakékoli zařízení společnosti Microsoft.

## <a name="physical-security-reviews"></a>Zkontroluje fyzické zabezpečení
Recenze fyzického zabezpečení zařízení jsou pravidelně provést k zajištění v datacentrech správně adresu požadavky na zabezpečení Microsoft Azure. Zprostředkovatel pracovníky hostování datacenter neposkytují správy služby Microsoft Azure. Pracovníky nemají přístup k přihlášení k Azure systémům nebo fyzický přístup k Azure kolokaci místnosti a klece.

## <a name="data-bearing-devices"></a>Data vliv zařízení
Společnost Microsoft používá nejlepší postup procedury a wiping řešení, které je [NIST 800-88 kompatibilní](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01). Pro pevné disky, které nelze vymazat se používá odstraňování proces, který zničí ho a vykreslí možné obnovení informací. Proces odstraňování může být rozpadnout skartovat, pulverize nebo spalování. Typ prostředku je dáno vhodné prostředky uvolnění. Záznamy o likvidaci zůstanou zachovány.  

## <a name="equipment-disposal"></a>Vyřazení zařízení
Microsoft Azure implementuje této zásady potřeby jejich zákazníků. Při systému end životnosti provozní personál společnosti Microsoft podle postupů uvolnění hardwaru, aby zajistil, že nedůvěryhodné stranám není k dispozici hardwaru obsahující data zákazníků a manipulaci s daty přísných. Přístup zabezpečené vymazat je následovaný (prostřednictvím firmware pevný disk) pro jednotky, které ji podporují. Pro pevné disky, které nelze vymazat se používá odstraňování proces, který zničí ho a vykreslí možné obnovení informací. Proces odstraňování může být rozpadnout skartovat, pulverize nebo spalování. Typ prostředku je dáno vhodné prostředky uvolnění. Záznamy o likvidaci zůstanou zachovány. Všechny služby Microsoft Azure pomocí služby pro úložiště a uvolnění schválené média.

## <a name="compliance"></a>Dodržování předpisů
Infrastruktura Azure je navržena a spravovat tak, aby splňovaly širokou škálu dodržování předpisů mezinárodní a průmyslové standardy, například ISO 27001, HIPAA, FedRAMP, SOC 1 a SOC 2. Jednotlivé země standardy jsou splněny, včetně IRAP Austrálie, UK G-Cloud a Singapur MTCS. Přísné audity třetích stran, třeba organizace BSI (British Standards Institute), ověřují, že Azure splňuje striktní bezpečnostní opatření vyžadovaná těmito standardy.

Najdete v článku [dodržování předpisů nabídky](https://www.microsoft.com/trustcenter/compliance/complianceofferings) pro úplný seznam standardů dodržování předpisů, kterého se drží Azure.

## <a name="next-steps"></a>Další postup
Další informace o funkci Microsoft pro zabezpečení infrastruktury Azure najdete v tématu:

- [Dostupnost infrastruktury Azure](azure-infrastructure-availability.md)
- [Součásti systému Azure informace a hranice](azure-infrastructure-components.md)
- [Architektura sítě Azure](azure-infrastructure-network.md)
- [Produkční sítě Azure](azure-production-network.md)
- [Zabezpečení funkce Microsoft Azure SQL Database](azure-infrastructure-sql.md)
- [Azure produkční operace a Správa](azure-infrastructure-operations.md)
- [Monitorování infrastruktury Azure](azure-infrastructure-monitoring.md)
- [Integritu infrastruktury Azure](azure-infrastructure-integrity.md)
- [Ochrana dat zákazníka v Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-physical-security/data-residency-boundary.png
