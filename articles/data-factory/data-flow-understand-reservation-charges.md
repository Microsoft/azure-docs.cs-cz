---
title: Vysvětlení slevy pro rezervace Azure Data Factory datových toků | Microsoft Docs
description: Přečtěte si, jak se pro spouštění toků dat ADF použije sleva pro rezervaci. Tato sleva se u těchto dat účtuje každou hodinu.
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: makromer
ms.openlocfilehash: 12b640fd97f48e293320593b33ab2fdc54980c0f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101716291"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-data-factory-data-flows"></a>Způsob použití zlevněné slevy pro Azure Data Factory toků dat

Po zakoupení rezervované kapacity toku dat ADF se sleva za rezervaci automaticky použije na toky dat pomocí prostředí Azure Integration runtime, které odpovídá výpočetnímu typu a počtu jader rezervace.

## <a name="how-reservation-discount-is-applied"></a>Jak se uplatňuje sleva za rezervaci

*Nevyužité slevy se nenahrazují*. Takže pokud nemáte odpovídající prostředky Integrace Azure využité pro každou hodinu, ztratíte množství rezervace za tuto hodinu. Nevyužité rezervované hodiny vám nemůžeme převést.

Když zastavíte používání prostředí Integration runtime pro toky dat, sleva rezervace se automaticky použije na jiný shodný prostředek v zadaném oboru. Pokud se v zadaném rozsahu nenajdou žádné vyhovující prostředky, o rezervované hodiny *přijdete*.

## <a name="discount-applied-to-adf-data-flows"></a>Sleva uplatněná na datové toky ADF

Pro provádění prostředí Integration runtime se za hodinu používá zvýhodněná Rezervovaná kapacita toku dat ADF. Rezervace, kterou zakoupíte, odpovídá výpočetnímu typu, který se používá v prostředí Integration runtime pro vaše toky dat. U toků dat, které nespouštějí celou hodinu, se rezervace automaticky použije na jiné toky dat, které odpovídají atributům rezervace. Tato sleva se může vztahovat i na toky dat, které běží souběžně. Pokud nemáte toky dat spouštěné po celou hodinu, které odpovídají atributům rezervace, nezískáte za tuto hodinu celou výhodu slevy za rezervaci.

Následující příklady znázorňují způsob, jakým se v závislosti na počtu zakoupených jader a při jejich spuštění používá zvýhodněná rezerva kapacity toku dat ADF.

- Scénář 1: zakoupíte rezervaci toku dat ADF po dobu 1 hodiny 80 jader optimalizované pro paměť, a to tak, že zadáte 80 jako množství pro paměťově optimalizovaný výpočetní typ. Tok dat s modulem runtime Integrace Azure je nastaven na 144 jader paměti optimalizované po dobu jedné hodiny. Účtuje se vám cena za průběžné platby za 64 jader využití toku dat po dobu jedné hodiny. Slevu za rezervaci získáte jednu hodinu z 80 jader paměti optimalizované pro využití paměti.
- Scénář 2: zakoupíte rezervaci toku dat ADF na 1 hodinu od 32 jader výpočetních prostředků, a to tak, že zadáte 32 jako množství pro výpočetní typ pro obecné účely. Datové toky můžete ladit po dobu 1 hodiny pomocí 32 jader obecného výpočetního prostředí Azure Integration runtime. Slevu za rezervaci získáte pro celou hodinu využití.

Vysvětlení a znázornění uplatňování rezervací Azure v sestavách fakturace využití najdete v tématu [Vysvětlení využití rezervací Azure](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

Další informace o Azure Reservations najdete v následujícím článku:

- [Co jsou rezervace Azure?](../cost-management-billing/reservations/save-compute-costs-reservations.md)