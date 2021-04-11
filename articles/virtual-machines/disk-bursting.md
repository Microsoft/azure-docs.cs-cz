---
title: Shlukování spravovaných disků
description: Přečtěte si informace o shlukování disku pro disky Azure a virtuální počítače Azure.
author: albecker1
ms.author: albecker
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 9758b026ef205e6608f7fc4110219dc5f267369e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568711"
---
# <a name="managed-disk-bursting"></a>Shlukování spravovaných disků
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

Azure [Premium SSD](disks-types.md#premium-ssd) nabízí dva modely shlukování:

- Model vypínání na vyžádání (Preview), kdy se shluky disků pokaždé, když překročí aktuální kapacitu. V tomto modelu se účtují další poplatky, kdykoli dojde k nárůstu disku. Nekreditový shluking je k dispozici pouze na discích větších než 512 GiB velikosti.
- Modelem založeným na kreditech, kde se disk bude rozkládat jenom v případě, že se v jeho kreditním intervalu nashromáždily shluky s kredity. Tento model při nárůstu disku neúčtuje žádné další poplatky. Shlukování na základě kreditů je dostupné jenom na discích 512 GiB a menších.

Kromě toho je [možné změnit úroveň výkonu spravovaných disků](disks-change-performance.md), což může být ideální, pokud by vaše úlohy jinak běžely v shluku.

|  |Shlukování na základě kreditu  |Shlukování na vyžádání  |Změna úrovně výkonu  |
|---------|---------|---------|---------|
| **Scénáře**|Ideální pro krátkodobé škálování (30 minut nebo i méně).|Ideální pro krátkodobé škálování (neomezený čas).|Ideální, pokud by se vaše úlohy jinak nepřetržitě spouštěly v shluku.|
|**Náklady**     |Free         |Náklady jsou proměnné, podrobnosti najdete v části [fakturace](#billing) .        |Náklady na jednotlivé úrovně výkonu jsou opravené. Další informace najdete v tématu [Managed disks ceny](https://azure.microsoft.com/pricing/details/managed-disks/) .         |
|**Dostupnost**     |K dispozici pouze pro Premium SSD 512 GiB a menší.         |K dispozici pouze pro prémiové SSD větší než 512 GiB.         |Dostupné pro všechny velikosti SSD úrovně Premium.         |
|**Povolení**     |Ve výchozím nastavení povoleno na vhodných discích.         |Musí být povolené uživatelem.         |Uživatel musí ručně změnit jejich úroveň.         |

## <a name="common-scenarios"></a>Obvyklé scénáře
Při navýšení zátěže můžou významně těžit z následujících scénářů:
- **Zlepšení časů spuštění**  – s využitím shlukování se vaše instance bude spouštět s výrazně vyšší rychlostí. Například výchozí disk s operačním systémem pro virtuální počítače s podporou Premium je disk P4, což je zřízený výkon až 120 vstupně-výstupních operací a 25 MB/s. V případě shlukování může P4 přejít až na 3500 IOPS a 170 MB/s, což umožňuje spuštění zrychlit až 6X.
- **Zpracování dávkových úloh** – některé úlohy aplikací jsou cyklické v podstatě. Vyžadují pro většinu času základní výkon a vyšší výkon pro krátkou dobu. Příkladem je účetní program, který zpracovává každodenní transakce, které vyžadují malé množství provozu na disku. Na konci měsíce by tento program dokončil sjednocování sestav, které potřebují mnohem větší objem provozu na disku.
- **Špičky provozu** – webové servery a jejich aplikace mohou kdykoli docházet k nárůstu provozu. Pokud je váš webový server zálohovaný virtuálními počítači nebo disky, které využívají shlukování, servery budou lépe vybavené pro zpracování špičky provozu. 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]

## <a name="next-steps"></a>Další kroky

Pokud chcete povolit shlukování na vyžádání, přečtěte si téma [Povolení shlukování na](disks-enable-bursting.md)vyžádání.
Informace o tom, jak získat přehled o prostředcích shlukování, najdete v tématu [metriky shlukování disku](disks-metrics.md).
