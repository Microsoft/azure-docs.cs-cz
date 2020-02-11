---
title: Principy uplatnění slevy za rezervaci ve službě Azure Disk Storage
description: Přečtěte si, jak se uplatňuje sleva za rezervované disky Azure u spravovaných disků SSD Premium.
author: roygara
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2020
ms.author: rogarana
ms.openlocfilehash: 7f23aaebc20b562768fb04ae988e4aff1b62fb21
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902143"
---
# <a name="understand-how-your-reservation-discount-is-applied-to-azure-disk-storage"></a>Principy uplatnění slevy za rezervaci ve službě Azure Disk Storage

Po zakoupení rezervované kapacity disku Azure se sleva za rezervaci automaticky uplatní na prostředky disku, které odpovídají podmínkám vaší rezervace. Tato sleva za rezervaci se vztahuje jenom na kapacitu disku, snímky disků se účtují sazbami průběžných plateb.

Další informace o rezervaci disků Azure najdete v tématu [Úspory nákladů s využitím rezervací disků Azure](../../virtual-machines/linux/disks-reserved-capacity.md).
Informace o cenách rezervací disků Azure najdete v tématu [Ceny disků Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="how-the-reservation-discount-is-applied"></a>Způsob uplatňování slevy za rezervaci

Sleva za rezervaci disků Azure je v případě nevyužití bez náhrady a uplatňuje se u spravovaných diskových prostředků po hodinách. Pokud v dané hodině nemáte žádné spravované diskové prostředky, které splňují podmínky vaší rezervace, ztrácíte rezervované množství za tuto hodinu. Nevyužité hodiny se nepřenášejí.

Když určitý prostředek odstraníte, sleva za rezervaci se automaticky uplatní na další vyhovující prostředek v zadaném rozsahu. Pokud se v zadaném rozsahu nenajdou žádné vyhovující prostředky, o rezervované hodiny přijdete.

## <a name="discount-examples"></a>Příklady slev

Následující příklady ukazují, jak se uplatňuje sleva za rezervaci disků Azure v závislosti na druhu nasazení:

Předpokládejme, že jste si na jeden rok zakoupili 100 disků P30 rezervované kapacity (přibližně 1 TiB na disk) v oblasti USA – západ 2. Řekněme, že náklady na tuto ukázkovou rezervaci jsou 140 100 dolarů. Můžete se rozhodnout buď zaplatit celou částku předem, nebo dalších 12 měsíců platit pevné splátky ve výši 11 675 dolarů měsíčně.
U těchto příkladů předpokládejme, že jste si zaregistrovali plán měsíčních plateb za rezervace. Následující scénáře popisují, co se stane, když rezervovanou kapacitu zcela nevyužijete, překročíte ji nebo ji rozvrstvíte.

### <a name="underusing-your-capacity"></a>Nevyužití celé kapacity

Předpokládejme, že v určité hodině v rámci období rezervace jste nasadili jenom 99 disků P30 Premium SSD z rezervovaných 100 disků P30. Zbývající 1 disk P30 se v dané hodině nevyužije a využití je nepřenosné.

### <a name="overusing-your-capacity"></a>Nadměrné využití kapacity

Předpokládejme, že jste v určitou hodinu rezervačního období využili 101 disků P30 Premium SSD. Sleva za rezervaci se vztahuje na 100 disků P30 a zbývající 1 disk P30 se za danou hodinu účtuje podle tarifů průběžných plateb. Pokud se v další hodině vaše využití změní na 100 disků P30, rezervace pokryje veškeré využití.

### <a name="tiering-your-capacity"></a>Rozvrstvení kapacity

Předpokládejme, že v určité hodině v rámci období rezervace chcete použít celkem 200 disků P30 Premium SSD. Prvních 30 minut budete používat jenom 100. Během této doby se využití plně pokryje, protože jste provedli rezervaci pro 100 disků P30. Pokud pak přestanete používat prvních 100 disků (takže nevyužíváte žádné) a začnete používat jiných 100 disků po dobu zbývajících 30 minut, pokryje vaše rezervace i toto využití.

![Znázornění příkladů nevyužití, překročení a rozvrstvení kapacity](media/understand-disk-reservations/reserved-disks-example-scenarios.png)

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

- [Snížení nákladů pomocí rezervací disků Azure (Linux)](../../virtual-machines/linux/disks-reserved-capacity.md)
- [Snížení nákladů pomocí rezervací disků Azure (Windows)](../../virtual-machines/windows/disks-reserved-capacity.md)
- [Co jsou rezervace Azure?](save-compute-costs-reservations.md)