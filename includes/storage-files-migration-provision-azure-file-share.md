---
title: Pokyny pro zřizování sdílených složek Azure
description: Zřídí sdílené složky Azure pro použití s Azure File Sync. Společný textový blok sdílený v rámci migračních dokumentů.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: d48baba5ee60a2bf5a4cb5e4d1ce840fce8eec43
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82143579"
---
Sdílená složka Azure je uložená v cloudu v účtu úložiště Azure.
Tady je další úroveň problémů s výkonem.

Pokud máte vysoce aktivní sdílené složky (sdílené položky používá mnoho uživatelů nebo aplikací), může se stát, že dvě sdílené složky Azure dosáhnou limitu výkonu účtu úložiště.

Osvědčeným postupem je nasazování účtů úložiště s jednou sdílenou složkou.
Můžete seskupit více sdílených složek Azure do stejného účtu úložiště pro případ, že máte archivované sdílené složky nebo v nich očekáváte nízkou denní aktivitu.

Tyto požadavky se vztahují i na přímý přístup do cloudu (přes virtuální počítač Azure) než Azure File Sync. Pokud plánujete použít Azure File Sync jenom na těchto sdílených složkách, je v pořádku seskupení několika do jednoho účtu Azure Storage.

Pokud jste vytvořili seznam sdílených složek, měli byste namapovat každou sdílenou složku na účet úložiště, ve kterém se bude nacházet.

V předchozí fázi jste určili odpovídající počet sdílených složek. V tomto kroku máte vytvořenou mapování účtů úložiště ke sdíleným složkám souborů. Nyní nasaďte příslušný počet účtů služby Azure Storage s příslušným počtem sdílených složek Azure v těchto souborech.

Ujistěte se, že je oblast každého účtu úložiště stejná a odpovídá oblasti prostředku služby synchronizace úložiště, který jste už nasadili.

> [!CAUTION]
> Pokud vytvoříte sdílenou složku Azure, která má limit 100-TiB, může tato sdílená složka používat jenom místně redundantní úložiště nebo redundantní možnosti redundance úložiště v zóně. Než začnete používat 100-TiB sdílené složky, zvažte vaše potřeby redundance úložiště.

Ve výchozím nastavení se sdílené složky Azure pořád vytváří s limitem 5 TiB. Vzhledem k tomu, že vytváříte nové účty úložiště, nezapomeňte postupovat podle [pokynů k vytvoření účtů úložiště, které umožňují sdílené složky Azure s omezením 100 – TIB](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Další aspekty při nasazení účtu úložiště je redundance Azure Storage. Informace najdete v tématu [Azure Storage možností redundance](../articles/storage/common/storage-redundancy.md).

Názvy vašich prostředků jsou také důležité. Pokud například seskupete pro personální oddělení více sdílených složek do účtu služby Azure Storage, měli byste účet úložiště pojmenovat správně. Podobně při pojmenovávání sdílených složek Azure byste měli používat názvy podobné těm, které se používají pro místní protějšky.