---
title: Vysvětlení uplatňování slevy za rezervaci ve službě Azure Storage | Microsoft Docs
description: Zjistěte, jak se uplatňuje sleva za rezervovanou kapacitu služby Azure Storage na objekty blob bloku a prostředky Azure Data Lake Storage Gen2.
author: tamram
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: tamram
ms.openlocfilehash: fc1110c124f5e4e6d30c27de0911f86c186b67c2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88681630"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-storage"></a>Vysvětlení uplatňování slevy za rezervaci ve službě Azure Storage

Když si zakoupíte rezervovanou kapacitu služby Azure Storage, na objekty blob bloku a prostředky Azure Data Lake Storage Gen2, které vyhovují podmínkám rezervace, se automaticky uplatňuje sleva za rezervaci. Sleva za rezervaci se vztahuje pouze na kapacitu úložiště. Šířka pásma a frekvence požadavků se účtují podle tarifů průběžných plateb.

Další informace o rezervované kapacitě služby Azure Storage najdete v tématu [Optimalizace nákladů na službu Blob Storage s využitím rezervované kapacity](../../storage/blobs/storage-blob-reserved-capacity.md).

Informace o cenách rezervací služby Azure Storage najdete v tématech [Ceny objektů blob bloku](https://azure.microsoft.com/pricing/details/storage/blobs/) a [Ceny služby Azure Data Lake Storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="how-the-reservation-discount-is-applied"></a>Způsob uplatňování slevy za rezervaci

Sleva za rezervovanou kapacitu služby Azure Storage se na objekty blob bloku a prostředky Azure Data Lake Storage Gen2 uplatňuje po hodinách.

Nevyužitá sleva za rezervovanou kapacitu služby Azure Storage se nenahrazuje. Pokud v určitou hodinu nemáte žádné objekty blob bloku ani prostředky Azure Data Lake Storage Gen2, které splňují podmínky rezervace, o rezervované množství za danou hodinu přijdete. Nevyužité rezervované hodiny vám nemůžeme převést.

Když určitý prostředek odstraníte, sleva za rezervaci se automaticky uplatní na další vyhovující prostředek v zadaném rozsahu. Pokud se v zadaném rozsahu nenajdou žádné vyhovující prostředky, o rezervované hodiny přijdete.

## <a name="discount-examples"></a>Příklady slev

Následující příklady ukazují, jak se uplatňuje sleva za rezervovanou kapacitu služby Azure Storage v závislosti na druhu nasazení.

Předpokládejme, že jste si na 1 rok zakoupili 100 TB rezervované kapacity v oblasti USA – západ 2. Vaše rezervace je určená pro místně redundantní úložiště (LRS) na horké úrovni přístupu.

Předpokládejme, že náklady na tuto ukázkovou rezervaci jsou 18 540 USD. Můžete se rozhodnout buď zaplatit celou částku předem, nebo dalších 12 měsíců platit paušální měsíční splátky ve výši 1 545 měsíčně.

U těchto příkladů předpokládejme, že jste si zaregistrovali plán měsíčních plateb za rezervace. Následující scénáře popisují, co se stane, když rezervovanou kapacitu zcela nevyužijete, nebo ji naopak využijete nadměrně.

### <a name="underusing-your-capacity"></a>Nevyužití celé kapacity

Předpokládejme, že jste v určitou hodinu rezervačního období využili pouze 80 TB ze 100 TB vaší rezervované kapacity. Zbývajících 20 TB se pro danou hodinu nepoužije a nepřevede se.

### <a name="overusing-your-capacity"></a>Nadměrné využití kapacity

Předpokládejme, že jste v určitou hodinu rezervačního období využili 101 TB kapacity úložiště. Sleva za rezervaci se vztahuje na 100 TB dat a zbývající 1 TB se za danou hodinu účtuje podle tarifů průběžných plateb. Pokud se další hodinu vaše využití změní na 100 TB, rezervace pokryje veškeré využití.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás

Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky

- [Optimalizace nákladů na službu Blob Storage s využitím rezervované kapacity](../../storage/blobs/storage-blob-reserved-capacity.md)
- [Co jsou rezervace Azure?](save-compute-costs-reservations.md)
