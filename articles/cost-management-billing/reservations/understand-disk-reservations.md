---
title: Principy slev za rezervaci pro Azure Disk Storage
description: Přečtěte si, jak se uplatňuje sleva za rezervované disky Azure u spravovaných disků Azure SSD úrovně Premium.
author: roygara
ms.author: rogarana
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: cd89c3ddc3c58de02f1104109ce7f243c4d1e6fd
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682446"
---
# <a name="understand-how-your-reservation-discount-is-applied-to-azure-disk-storage"></a>Principy uplatnění slevy za rezervaci ve službě Azure Disk Storage

Po zakoupení rezervované kapacity disku Azure se sleva za rezervaci automaticky uplatní pro prostředky disku, které odpovídají podmínkám vaší rezervace. Sleva za rezervaci se vztahuje jenom na diskové jednotky SKU. Snímky disků účtují podle tarifů průběžných plateb.

Další informace o rezervaci disků Azure najdete v tématu věnovaném [úsporám nákladů při využití rezervací disků Azure](../../virtual-machines/disks-reserved-capacity.md). Informace o cenách rezervací disků Azure najdete v tématu věnovaném [cenám spravovaných disků Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="how-the-reservation-discount-is-applied"></a>Způsob uplatňování slevy za rezervaci

Nevyužitá sleva za rezervovanou kapacitu služby Azure Storage se nenahrazuje. Sleva za rezervaci se pro prostředky spravovaných využívá po hodinách. Pokud pro konkrétní hodinu nemáte žádné prostředky spravovaného disku, které splňují podmínky rezervace, rezervaci za tuto hodinu ztratíte. Nevyužité hodiny se nepřenášejí.

Když určitý prostředek odstraníte, sleva za rezervaci se automaticky uplatní na další vyhovující prostředek v zadaném rozsahu. Pokud se nenajde žádný vyhovující prostředek, o rezervované hodiny přijdete.

## <a name="discount-examples"></a>Příklady slev

Následující příklady ukazují, jak se uplatňuje sleva za rezervaci disků Azure v závislosti na typu nasazení.

Předpokládejme, že si koupíte a rezervujete 100 disků P30 v oblasti USA – západ 2 na dobu jednoho roku. Každý disk má přibližně 1TiB úložiště. Předpokládejme, že náklady na tuto ukázkovou rezervaci jsou 140 100 USD. Můžete se rozhodnout buď zaplatit celou částku předem, nebo dalších 12 měsíců platit pevné splátky ve výši 11 675 dolarů.

Následující scénáře popisují, co se stane, když rezervovanou kapacitu zcela nevyužijete, překročíte ji nebo ji rozvrstvíte. U těchto příkladů předpokládejme, že jste si zaregistrovali plán měsíčních plateb za rezervace.

### <a name="underusing-your-capacity"></a>Nevyužití celé kapacity

Předpokládejme, že během období rezervace po dobu jedné hodiny nasadíte jenom 99 z vašich 100 rezervovaných disků Azure SSD (Solid-State Drive) úrovně Premium P30. Zbývající disk P30 se během této hodiny nepoužije. Ani tady se nic nepřevádí.

### <a name="overusing-your-capacity"></a>Nadměrné využití kapacity

Předpokládejme, že jste v rezervačním období do dobu jedné hodiny využili 101 disků SSD úrovně Premium P30. Sleva za rezervaci se vztahuje jenom na 100 disků P30. Zbývající disk P30 se za tuto hodinu účtuje podle tarifů průběžných plateb. Pokud se v další hodině vaše využití změní na 100 disků P30, rezervace pokryje veškeré využití.

### <a name="tiering-your-capacity"></a>Rozvrstvení kapacity

Předpokládejme, že v určité hodině v rámci období rezervace chcete použít celkem 200 disků SSD úrovně Premium P30. Předpokládejme také, že po dobu prvních 30 minut použijete jenom 100 z nich. Během této doby se využití plně pokryje, protože jste provedli rezervaci pro 100 disků P30. Pokud pak přestanete používat prvních 100 disků (takže nevyužíváte žádné) a začnete používat jiných 100 disků po dobu zbývajících 30 minut, pokryje vaše rezervace i toto využití.

![Příklady nevyužití, překročení a rozvrstvení kapacity](media/understand-disk-reservations/reserved-disks-example-scenarios.png)

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

- [Snížení nákladů pomocí rezervací disků Azure](../../virtual-machines/disks-reserved-capacity.md)
- [Co jsou rezervace Azure?](save-compute-costs-reservations.md)
