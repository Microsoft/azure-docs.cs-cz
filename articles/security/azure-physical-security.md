---
title: Zařízení Azure, místním prostředí a fyzické zabezpečení | Dokumentace Microsoftu
description: Tento článek popisuje datacentra Azure, včetně fyzické infrastruktury, zabezpečení a dodržování předpisů nabídky.
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
ms.openlocfilehash: 5f8378de18796fd679354c88880942df83399ad5
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112040"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Zařízení Azure, místním prostředí a fyzické zabezpečení
Azure se skládá z [infrastruktury globálně distribuovaných datacenter](https://azure.microsoft.com/global-infrastructure/), podporuje tisíce služeb online services a pokrývající více než 100 vysoce zabezpečenými zařízeními po celém světě.

Infrastruktura slouží k používání dostaly blíž k uživatelům po celém světě, zachovává rezidenci dat a nabízí komplexní možnosti dodržování předpisů a odolnost proti chybám pro zákazníky. Azure má 52 oblastí po celém světě a je dostupná ve 140 zemích.

Oblast je sada datových center, která je propojena prostřednictvím sítě masivní a odolná proti selháním. Síť obsahuje ve výchozím nastavení distribuce obsahu, Vyrovnávání zatížení, redundance a šifrování. S víc globálních oblastí než jakýkoli jiný poskytovatel cloudu Azure poskytuje flexibilitu při nasazování aplikací kdy je potřebujete.

Oblasti Azure jsou uspořádané do zeměpisných oblastí. Zeměpisná oblast Azure zaručuje, že se v rámci příslušných zeměpisných hranic dodržují požadavky na rezidenci dat, suverenitu, dodržování předpisů a zajištění odolnosti.

Zeměpisné oblasti umožňují zákazníkům se specifickými požadavky na rezidenci dat a dodržování předpisů, aby měli svoje data a aplikace blízko. Zeměpisné oblasti jsou odolné proti chybám a odolat kompletní selhání oblasti, připojení k vyhrazené, vysokokapacitní síťové infrastruktury.

Zóny dostupnosti jsou fyzicky oddělená umístění uvnitř oblasti Azure. Každá zóna dostupnosti se skládá z jednoho nebo více datových Center vybavených nezávislým napájením, chlazením a sítí. Zóny dostupnosti umožňují provozovat důležité aplikace s vysokou dostupností a replikací s nízkou latencí.

Následující obrázek ukazuje, jak globální infrastruktura Azure párů oblasti a dostupnost zóny v rámci stejnou hranicí rezidence dat pro vysokou dostupnost, zotavení po havárii a zálohování.

![Hranicí rezidence dat zajistíte zobrazení diagramu][1]

Geograficky distribuovaná datacentra umožňuje společnosti Microsoft bude blízko zákazníkům snížit latenci sítě a povolit pro geograficky redundantní zálohy a převzetí služeb při selhání.

## <a name="physical-security"></a>Fyzické zabezpečení
Microsoft navrhuje, sestavení a funguje datových center tak, aby výhradně řídí fyzický přístup k oblasti, ve kterém je vaše data uložená. Microsoft rozumí důležitost ochrany vašich dat a klade velký důraz na zdokonalovat zabezpečení datových center, které obsahují data. Máme celou divizi společnosti Microsoft, které jsou určené pro navrhování, sestavování a provoz fyzické zařízení podporující Azure. Tento tým se investovalo stavu nejmodernější fyzické zabezpečení.

Společnost Microsoft má vícevrstvý přístup k fyzické zabezpečení, aby se snížilo riziko neoprávnění uživatelé získají fyzický přístup k datům a prostředkům datového centra. Datacentra spravovaná microsoftem budou mít rozsáhlé vrstvy ochrany: přístup ke schválení v hraniční síti zařízení, na hraniční vytváření, uvnitř budovy a na dolní mez datového centra. Vrstvy fyzického zabezpečení jsou:

- **Žádost o přístup a schválení.** Musíte požádat o přístup, než dorazí v datovém centru. Je nutné zadat platného obchodního odůvodnění pro vaší návštěvě, například dodržování předpisů a auditování. Všechny požadavky, jsou schvalováni na základě potřebu přístupu zaměstnanci společnosti Microsoft. Základ potřebu přístupu udržet počet jednotlivců, které jsou potřebné k dokončení úkolu v datových centrech úplné minimum. Po Microsoft udělí oprávnění, individuální uživatel má přístup pouze diskrétní oblast datového centra, vyžaduje, založený na schválené obchodní odůvodnění. Oprávnění jsou omezené na určitou dobu a pak vyprší.

- **Hraniční zařízení společnosti.** Až přijedete datového centra, budete vyzváni k procházení jasně definovaných přístupový bod. Vysoký ohrazení provedených od oceli, betonu obvykle zahrnovat každý palec hraniční síti. Existují kamery kolem datových center s týmem zabezpečení sledování jejich videi za všech okolností.

- **Vytváření vstupu.** Vstup datového centra je prověřený s profesionální pracovníci, kteří prošly přísnými kontroly školení a na pozadí. Tyto vedoucí pracovníci pověření ochranou zabezpečení také pravidelně patrol datového centra a sledovat videa kamery zóny datového centra za všech okolností.

- **V sestavení.** Po zadání budovy, musíte předat dvojúrovňového ověřování pomocí biometrika pokračovat prostřednictvím datového centra. Pokud je ověřit vaši identitu, můžete zadat jenom část datové centrum, které jste schválili přístup k. Abyste mohli zůstat existuje pouze po dobu trvání doby schválení.

- **Dolní mez datového centra.** Můžete se povolují jenom na dolní mez, že jsou schválena k zadání. Je nutné předat prověřování systému detekce úplný text. Snížit riziko neoprávněného data zadáním nebo opuštění datového centra bez naši znalostní báze, jenom schválené zařízení nastavit cestě do datacentra podlaží. Kromě toho videokamer monitorování přední a zadní každého serveru racku. Při ukončení datacenter floor můžete znovu musí projít prověřování systému detekce úplný text. K opuštění datového centra, je nutné předávat další bezpečnostní kontrolu.

Microsoft vyžaduje od návštěvníků předáním oznámení při odeslání z jakékoli zařízení společnosti Microsoft.

## <a name="physical-security-reviews"></a>Revize fyzické zabezpečení
Pravidelně provádíme revize fyzického zabezpečení zařízení, aby datová centra správně požadavky zabezpečení Azure adres. Pracovníci poskytovatele hostování datového centra se nevztahuje Smlouva pro správu služeb Azure. Nemůžete se přihlásit k Azure systémy a nemáte fyzický přístup k Azure kolokaci místa a klece.

## <a name="data-bearing-devices"></a>Data zařízení vliv
Společnost Microsoft používá nejlepší postup postupy a wiping řešení, které je [NIST 800-88 kompatibilní](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01). Pevné disky, které není možné vymazat používáme zničení proces, který se odstraní a vykreslí obnovení informace nemožné. Tento proces odstranění může být rozpadnout, odstranění, pulverize nebo spalování. Určíme znamená vyřazení podle typu prostředku. Budeme uchovávat záznamy zničení.  

## <a name="equipment-disposal"></a>Vyřazení zařízení
Při systému ukončení životnosti technologie provozní pracovníci společnosti Microsoft podle přísné daty a postupy vyřazení hardwaru, aby zajistil, že hardware, který obsahuje vaše data není k dispozici nedůvěryhodní. Přístup založený na zabezpečené vymazání používáme pro pevné disky, které ho podporují. Pevné disky, které není možné vymazat používáme zničení proces, který zničí jednotky a vykreslí obnovení informace nemožné. Tento proces odstranění může být rozpadnout, odstranění, pulverize nebo spalování. Určíme znamená vyřazení podle typu prostředku. Budeme uchovávat záznamy zničení. Všechny služby Azure pomocí služby správy úložiště a odstraňování schválené média.

## <a name="compliance"></a>Dodržování předpisů
Jsme návrhu a správě infrastruktury Azure pro splnění širokou škálu dodržování předpisů mezinárodní a oborové standardy, jako jsou ISO 27001, HIPAA, FedRAMP, SOC 1 a SOC 2. Také plníme standardy specifické pro zemi, třeba irap (Austrálie), hodnocení G-Cloud a Singapur MTCS. Přísné audity třetích stran, jako je například bsi British Standards Institute ověřte splňuje striktní bezpečnostní opatření vyžadovaná těmito standardy.

Úplný seznam, které Azure dodržuje standardy pro dodržování předpisů, najdete v článku [nabídek dodržování předpisů](https://www.microsoft.com/trustcenter/compliance/complianceofferings). 

## <a name="next-steps"></a>Další postup
Další informace o co společnost Microsoft k zajištění infrastruktury Azure, najdete v tématech:

- [Dostupnost infrastruktury Azure](azure-infrastructure-availability.md)
- [Součásti systému Azure informace a hranice](azure-infrastructure-components.md)
- [Architektura sítě Azure](azure-infrastructure-network.md)
- [Produkční sítě Azure](azure-production-network.md)
- [Funkce zabezpečení Azure SQL Database](azure-infrastructure-sql.md)
- [Operace Azure výroby a správu](azure-infrastructure-operations.md)
- [Monitorování infrastruktury Azure](azure-infrastructure-monitoring.md)
- [Integrity infrastruktury Azure](azure-infrastructure-integrity.md)
- [Ochrana dat zákazníků Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-physical-security/data-residency-boundary.png
