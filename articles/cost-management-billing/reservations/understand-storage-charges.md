---
title: Vysvětlení způsobu použití slev pro rezervace v Azure Storage Services | Microsoft Docs
description: Přečtěte si, jak se v Azure Blob Storage, souborech Azure a Azure Data Lake Storage Gen2ch prostředcích používají rezervované slevy na kapacitu.
author: tamram
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: tamram
ms.openlocfilehash: f18097f0da658b3a485b79f5279e9ecc54a41ea0
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024026"
---
# <a name="understand-how-reservation-discounts-are-applied-to-azure-storage-services"></a>Vysvětlení způsobu použití slev pro rezervace u služeb Azure Storage 
Služba Azure Storage vám umožní ušetřit peníze za náklady na úložiště tím, že rezervuje kapacitu. Instance rezerv služby Azure Blob Storage, soubory Azure a úložiště Azure Data Lake Gen 2 podporují. Po zakoupení rezervované kapacity se sleva rezervace automaticky použije u prostředků úložiště, které odpovídají podmínkám rezervace. Sleva za rezervaci se vztahuje pouze na kapacitu úložiště. Šířka pásma a frekvence požadavků se účtují podle tarifů průběžných plateb.

Další informace o rezervované kapacitě služby Azure Blob Storage a Azure Data Lake Storage najdete v tématu [optimalizace nákladů na úložiště objektů BLOB s rezervovanou kapacitou](../../storage/blobs/storage-blob-reserved-capacity.md). Další informace o rezervované kapacitě souborů Azure najdete v tématu [optimalizace nákladů na soubory Azure s rezervovanou kapacitou](../../storage/files/files-reserve-capacity.md).

Informace o cenách rezervací služby Azure Blob Storage najdete v tématu ceny za objekty [blob bloku](https://azure.microsoft.com/pricing/details/storage/blobs/) a [ceny Azure Data Lake Storage Gen 2](https://azure.microsoft.com/pricing/details/storage/data-lake/). Informace o cenách rezervací úložiště Azure Files najdete v tématu [ceny za Azure Files](https://azure.microsoft.com/pricing/details/storage/files).

## <a name="how-the-reservation-discount-is-applied"></a>Způsob uplatňování slevy za rezervaci
Rezervovaná zvýhodněná kapacita se u podporovaných prostředků úložiště aplikuje na každou hodinu.

Rezervovaná zvýhodněná kapacita je sleva za použití nebo ke ztrátě. Pokud nemáte žádné objekty blob bloku, sdílené složky Azure nebo Azure Data Lake Storage Gen2 prostředky, které splňují podmínky rezervace za danou hodinu, ztratíte množství rezervace za tuto hodinu. Nevyužité rezervované hodiny vám nemůžeme převést.

Když určitý prostředek odstraníte, sleva za rezervaci se automaticky uplatní na další vyhovující prostředek v zadaném rozsahu. Pokud se v zadaném rozsahu nenajdou žádné vyhovující prostředky, o rezervované hodiny přijdete.

## <a name="discount-examples"></a>Příklady slev
Následující příklady ukazují, jak se zvýhodněná Rezervovaná kapacita vztahuje v závislosti na nasazeních.

Předpokládejme, že jste si zakoupili 100 TiB rezervovaných kapacit v oblasti v USA – západ 2 v oblasti po dobu 1 roku. Vaše rezervace je pro úložiště objektů BLOB místně redundantního úložiště (LRS) ve vrstvě Hot Access.

Předpokládejme, že náklady na tuto ukázkovou rezervaci jsou 18 540 USD. Můžete se rozhodnout buď zaplatit celou částku předem, nebo dalších 12 měsíců platit paušální měsíční splátky ve výši 1 545 měsíčně.

U těchto příkladů předpokládejme, že jste si zaregistrovali plán měsíčních plateb za rezervace. Následující scénáře popisují, co se stane, když rezervovanou kapacitu zcela nevyužijete, nebo ji naopak využijete nadměrně.

### <a name="underusing-your-capacity"></a>Nevyužití celé kapacity
Předpokládejme, že v dané hodině v rámci období rezervace jste používali jenom 80 TiB vaší rezervované kapacity 100 TiB. Zbývající 20 TiB se pro tuto hodinu nepoužije a neprovádí se.

### <a name="overusing-your-capacity"></a>Nadměrné využití kapacity
Předpokládejme, že v dané hodině v rámci období rezervace jste použili 101 TiB kapacity úložiště. Zlevněná sleva se vztahuje na 100 TiB vašich dat a zbývající 1 TiB se účtují podle tarifů průběžných plateb za tuto hodinu. Pokud během příští hodiny se použití změní na 100 TiB, pak se na všechna využití vztahují rezervace.

## <a name="need-help-contact-us"></a>Potřebujete pomoc? Kontaktujte nás
Pokud máte dotazy nebo potřebujete pomoc, [vytvořte žádost o podporu](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Další kroky
- [Optimalizace nákladů na službu Blob Storage s využitím rezervované kapacity](../../storage/blobs/storage-blob-reserved-capacity.md)
- [Optimalizace nákladů na soubory Azure s rezervovanou kapacitou](../../storage/files/files-reserve-capacity.md)
- [Co jsou rezervace Azure?](save-compute-costs-reservations.md)
