---
title: Vysvětlení slevy pro rezervace Azure Data Factory datových toků | Microsoft Docs
description: Přečtěte si, jak se pro spouštění toků dat ADF použije sleva pro rezervaci. Tato sleva se u těchto dat účtuje každou hodinu.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: makromer
ms.openlocfilehash: 6936b9344196f8071a6c1859869c62e5bee22924
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98811653"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-data-factory-data-flows"></a>Způsob použití zlevněné slevy pro Azure Data Factory toků dat

Po zakoupení rezervované kapacity toku dat ADF se sleva za rezervaci automaticky použije na toky dat pomocí prostředí Azure Integration runtime, které odpovídá výpočetnímu typu a počtu jader rezervace.

## <a name="how-reservation-discount-is-applied"></a>Jak se uplatňuje sleva za rezervaci

*Nevyužité slevy se nenahrazují*. Takže pokud nemáte odpovídající prostředky Integrace Azure využité pro každou hodinu, ztratíte množství rezervace za tuto hodinu. Nevyužité rezervované hodiny vám nemůžeme převést.

Když zastavíte používání prostředí Integration runtime pro toky dat, sleva rezervace se automaticky použije na jiný shodný prostředek v zadaném oboru. Pokud se v zadaném rozsahu nenajdou žádné vyhovující prostředky, o rezervované hodiny *přijdete*.

## <a name="discount-applied-to-adf-data-flows"></a>Sleva uplatněná na datové toky ADF

Pro provádění prostředí Integration runtime se za hodinu používá zvýhodněná Rezervovaná kapacita toku dat ADF. Rezervace, kterou zakoupíte, se shoduje s výpočetním využitím vydaným využitím prostředí Integration runtime. U toků dat, které nespouštějí celou hodinu, se rezervace automaticky použije na jiné toky dat, které odpovídají atributům rezervace. Tato sleva se může vztahovat na toky dat, které běží souběžně. Pokud nemáte toky dat spouštěné po celou hodinu, které odpovídají atributům rezervace, nezískáte za tuto hodinu celou výhodu slevy za rezervaci.

Následující příklady znázorňují způsob, jakým se v závislosti na počtu zakoupených jader a při jejich spuštění používá zvýhodněná rezerva kapacity toku dat ADF.

- Scénář 1: koupíte rezervovanou kapacitu toku dat ADF pro 80 jader paměťově optimalizovaných výpočtů. Tok dat s modulem runtime Integrace Azure je nastaven na 144 jader paměti optimalizované po dobu jedné hodiny. Účtuje se vám cena za průběžné platby za 64 jader využití toku dat po dobu jedné hodiny. Slevu za rezervaci získáte jednu hodinu z 80 jader paměti optimalizované pro využití paměti.
- Scénář 2: Kupte si rezervovanou kapacitu toku dat ADF pro 32 jader COMPUTE pro obecné účely. Datové toky můžete ladit po dobu 1 hodiny pomocí 32 jader obecného výpočetního prostředí Azure Integration runtime. Slevu za rezervaci získáte pro celou hodinu využití.

Vysvětlení a znázornění uplatňování rezervací Azure v sestavách fakturace využití najdete v tématu [Vysvětlení využití rezervací Azure](https://docs.microsoft.com/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

Další informace o Azure Reservations najdete v následujícím článku:

- [Co jsou rezervace Azure?](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations)
