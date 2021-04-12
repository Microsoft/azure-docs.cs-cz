---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: c003e765574d764b6653823e8554d0718ea85e0e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081077"
---
Sdílená složka Azure je uložená v cloudu v účtu úložiště Azure.
Tady je další úroveň problémů s výkonem.

Pokud máte vysoce aktivní sdílené složky (sdílené položky používá mnoho uživatelů nebo aplikací), může se stát, že dvě sdílené složky Azure dosáhnou limitu výkonu účtu úložiště.

Osvědčeným postupem je nasazování účtů úložiště s jednou sdílenou složkou.
Můžete seskupit více sdílených složek Azure do stejného účtu úložiště pro případ, že máte archivované sdílené složky nebo v nich očekáváte nízkou denní aktivitu.

Tyto požadavky se vztahují i na přímý přístup do cloudu (přes virtuální počítač Azure) než Synchronizace souborů Azure. Pokud plánujete použít Synchronizace souborů Azure jenom na těchto sdílených složkách, je v pořádku seskupení několika do jednoho účtu Azure Storage.

Pokud jste vytvořili seznam sdílených složek, měli byste namapovat každou sdílenou složku na účet úložiště, ve kterém se bude nacházet.

V předchozí fázi jste určili odpovídající počet sdílených složek. V tomto kroku máte vytvořenou mapování účtů úložiště ke sdíleným složkám souborů. Nyní nasaďte příslušný počet účtů služby Azure Storage s příslušným počtem sdílených složek Azure v těchto souborech.

Ujistěte se, že je oblast každého účtu úložiště stejná a odpovídá oblasti prostředku služby synchronizace úložiště, který jste už nasadili.

> [!CAUTION]
> Pokud vytvoříte sdílenou složku Azure, která má limit 100-TiB, může tato sdílená složka používat jenom místně redundantní úložiště nebo redundantní možnosti redundance úložiště v zóně. Než začnete používat 100-TiB sdílené složky, zvažte vaše potřeby redundance úložiště.

Ve výchozím nastavení se sdílené složky Azure pořád vytváří s limitem 5 TiB. Vzhledem k tomu, že vytváříte nové účty úložiště, nezapomeňte postupovat podle [pokynů k vytvoření účtů úložiště, které umožňují sdílené složky Azure s omezením 100 – TIB](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Další aspekty při nasazení účtu úložiště je redundance Azure Storage. Informace najdete v tématu [Azure Storage možností redundance](../articles/storage/common/storage-redundancy.md).

Názvy vašich prostředků jsou také důležité. Pokud například seskupete pro personální oddělení více sdílených složek do účtu služby Azure Storage, měli byste účet úložiště pojmenovat správně. Podobně při pojmenovávání sdílených složek Azure byste měli používat názvy podobné těm, které se používají pro místní protějšky.