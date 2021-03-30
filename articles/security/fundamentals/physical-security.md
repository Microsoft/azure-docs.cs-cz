---
title: Fyzické zabezpečení datových center Azure – Microsoft Azure | Microsoft Docs
description: Tento článek popisuje, co Microsoft dělá k zabezpečení datových center Azure, včetně fyzické infrastruktury, zabezpečení a nabídek dodržování předpisů.
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
ms.date: 07/10/2020
ms.author: terrylan
ms.openlocfilehash: 3eac3c9abda74f0414fc97512f011d235d3991f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87076327"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Zařízení, místní a fyzické zabezpečení Azure
Tento článek popisuje, co Microsoft dělá k zabezpečení infrastruktury Azure.

## <a name="datacenter-infrastructure"></a>Infrastruktura Datacenter
Azure se skládá z [globálně distribuované infrastruktury Datacenter](https://azure.microsoft.com/global-infrastructure/), podporuje tisíce online služby a pokrývá více než 100 vysoce zabezpečených zařízení po celém světě.

Infrastruktura je navržená tak, aby přiblížila aplikace uživatelům po celém světě, zachovává zaregistrování dat a nabízela zákazníkům komplexní možnosti dodržování předpisů a odolnosti proti chybám. Azure má 58 oblastí po celém světě a je k dispozici ve 140 zemích nebo oblastech.

Oblast je sada datových center, která je propojená pomocí obrovské a odolné sítě. Síť zahrnuje distribuci obsahu, Vyrovnávání zatížení, redundanci a [šifrování linkové vrstvy dat ve výchozím nastavení](encryption-overview.md#encryption-of-data-in-transit) pro všechny přenosy Azure v rámci jedné oblasti nebo při cestách mezi oblastmi. V případě více globálních oblastí než jakýkoli jiný poskytovatel cloudu vám Azure poskytne flexibilitu při nasazování aplikací tam, kde je potřebujete.

Oblasti Azure jsou uspořádané do geografických oblastí. Zeměpisná oblast Azure zajišťuje, že se nároky na data, svrchovanost, dodržování předpisů a odolnost proti chybám uplatňují v rámci zeměpisných hranic.

Zeměpisné oblasti umožňují zákazníkům se specifickými požadavky na rezidenci dat a dodržování předpisů, aby měli svoje data a aplikace blízko. Geografické oblasti jsou odolné proti chybám, aby odolaly kompletní selhání oblasti prostřednictvím připojení k vyhrazené síťové infrastruktuře s vysokou kapacitou.

Zóny dostupnosti jsou fyzicky oddělená umístění v rámci oblasti Azure. Každou zónu dostupnosti tvoří jedno nebo několik datových center vybavených nezávislým napájením, chlazením a sítí. Zóny dostupnosti umožňují spouštět klíčové aplikace s vysokou dostupností a replikací s nízkou latencí.

Následující obrázek ukazuje, jak globální infrastruktura Azure spáruje oblast a zóny dostupnosti v rámci stejné hranice umístění dat pro zajištění vysoké dostupnosti, zotavení po havárii a zálohování.

![Diagram znázorňující hranici zasídlí dat](./media/physical-security/data-residency-boundary.png)

Geograficky distribuovaná datacentra umožňují Microsoftu blízko zákazníkům, aby se snížila latence sítě a umožňovala geograficky redundantní zálohování a převzetí služeb při selhání.

## <a name="physical-security"></a>Fyzické zabezpečení
Microsoft navrhuje, sestavuje a provozuje datacentra způsobem, který přesně řídí fyzický přístup k oblastem, kde jsou uložená vaše data. Společnost Microsoft chápe důležitost ochrany vašich dat a je potvrzena pro pomoc s zabezpečením datových center, která obsahují vaše data. Máme celé oddělení Microsoftu zaměřené na návrh, sestavování a provoz fyzických zařízení podporujících Azure. Tento tým je investoval do zachování nejmodernějšího fyzického zabezpečení.

Microsoft využívá k fyzickému zabezpečení vrstvený přístup, aby snížil riziko neautorizovaných uživatelů, kteří získají fyzický přístup k datům a prostředkům datacentra. Datová centra spravovaná Microsoftem mají rozsáhlou vrstvu ochrany: ke schválení přístupu v hraničním zařízení, na hraničním zařízení, v rámci budovy a v centru Datacenter. Vrstvy fyzického zabezpečení jsou:

- **Žádosti o přístup a schválení.** Před příchodem do datacentra musíte požádat o přístup. Pro vaši návštěvu, jako je třeba dodržování předpisů nebo auditování, potřebujete poskytnout platné obchodní odůvodnění. Všechny požadavky se schvalují pro zaměstnance Microsoftu na základě potřeby přístupu. Základ pro přístup pomáhá udržet počet jednotlivců potřebných k dokončení úkolu v datových centrech až po minimální dobu. Jakmile Microsoft udělí oprávnění, má individuální přístup jenom k diskrétní oblasti požadovaného datacentra na základě schváleného obchodního odůvodnění. Oprávnění jsou omezená na určitou dobu a potom vyprší.

- **Hraniční zařízení.** Když přijdete do datového centra, budete muset projít dobře definovaným přístupovým bodem. Obvykle se vysoké ochranné ploty z oceli a betonu skládají z každé palce obvodu. Existují fotoaparáty v datových centrech, přičemž tým zabezpečení neustále monitoruje svá videa.

- **Sestavování vchodu.** Vstup datového centra je personálem s profesionálními bezpečnostními důstojníky, kteří prošli přísnými školeními a kontrolami na pozadí. Tito bezpečnostníci také rutinním způsobem dohlíží do datového centra a neustále monitorují videa o fotoaparátech v datacentru.

- **Uvnitř budovy.** Po zadání budovy musíte přes biometrika předat dvojúrovňové ověřování, aby bylo možné pokračovat v přesouvání prostřednictvím datového centra. Pokud je vaše identita ověřená, můžete zadat jenom část datacentra, ke které jste schválili přístup. Můžete zůstat až po dobu trvání schváleného času.

- **Datacenter** Povoluje se jenom na podlaze, kterou jste schválili. Je nutné, abyste vyhověli úplnému prověřování detekce základního kovu. Aby se snížilo riziko neautorizovaných dat, která vstupují do datacentra a opouští je, bez vědomí, můžou mít jenom schválená zařízení přístup k podlaze Datacenter. Kromě toho videokamery monitorují přední a zadní stranu každého racku serveru. Až skončíte s podlahou Datacenter, musíte znovu projít úplným prověřováním detekce základního kovu. Chcete-li opustit datové centrum, je nutné projít další kontrolu zabezpečení.

Společnost Microsoft vyžaduje, aby návštěvníci po odchodu z jakéhokoli zařízení Microsoftu přepředali označení.

## <a name="physical-security-reviews"></a>Posouzení fyzického zabezpečení
Pravidelně provádíme kontroly fyzického zabezpečení těchto zařízení, abychom zajistili správné řešení požadavků na zabezpečení Azure v datacentrech. Zaměstnanci poskytovatele hostingu Datacenter neposkytují správu služeb Azure. Zaměstnanci se nemůžou přihlásit k systémům Azure a nemají fyzický přístup k místnosti a kleci Azure.

## <a name="data-bearing-devices"></a>Data nesoucí zařízení
Microsoft používá osvědčené postupy a řešení mazání, které je [kompatibilní s NIST 800-88](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01). Pro pevné disky, které se nedají vymazat, používáme proces zničení, který ho zničí a vykreslí obnovení informací nemožné. Tento proces zničení může být disintegrate, skartovat, pulverize nebo spálen. Způsob vyřazení určíme podle typu assetu. Uchováváme záznamy o zničení.  

## <a name="equipment-disposal"></a>Odstraňování zařízení
Po skončení životnosti systému Microsoft Operational personál dodržuje přísné postupy pro zpracování a odstraňování hardwaru, aby se zajistilo, že hardware obsahující vaše data není dostupný nedůvěryhodným stranám. Pro pevné disky, které to podporují, používáme zabezpečený přístup k vymazání. Pro pevné disky, které se nedají vymazat, používáme proces zničení, který tuto jednotku zničí a vykreslí obnovení informací, které není možné. Tento proces zničení může být disintegrate, skartovat, pulverize nebo spálen. Způsob vyřazení určíme podle typu assetu. Uchováváme záznamy o zničení. Všechny služby Azure používají schválené služby pro správu mediálních úložišť a odstraňování.

## <a name="compliance"></a>Dodržování předpisů
Navrhujeme a spravujeme infrastrukturu Azure pro splnění široké škály mezinárodních a specifických standardů dodržování předpisů, jako jsou ISO 27001, HIPAA, FedRAMP, SOC 1 a SOC 2. Splňujeme také standardy pro konkrétní země nebo oblast, včetně Austrálie IRAP, UK G-Cloud a Singapuru MTCS. Přísné audity třetích stran, jako jsou ty, které provádí British Standards Institute, ověřují dodržování přísných kontrol zabezpečení těchto standardů.

Úplný seznam standardů dodržování předpisů, které Azure dodržuje, najdete v tématu [nabídky dodržování předpisů](https://www.microsoft.com/trustcenter/compliance/complianceofferings).

## <a name="next-steps"></a>Další kroky
Další informace o tom, co Microsoft dělá k zabezpečení infrastruktury Azure, najdete tady:

- [Dostupnost infrastruktury Azure](infrastructure-availability.md)
- [Komponenty a hranice informačních systémů Azure](infrastructure-components.md)
- [Architektura sítě Azure](infrastructure-network.md)
- [Produkční síť Azure](production-network.md)
- [Funkce zabezpečení Azure SQL Database](infrastructure-sql.md)
- [Provozní provoz a Správa Azure](infrastructure-operations.md)
- [Monitorování infrastruktury Azure](infrastructure-monitoring.md)
- [Integrita infrastruktury Azure](infrastructure-integrity.md)
- [Ochrana zákaznických dat Azure](protection-customer-data.md)


