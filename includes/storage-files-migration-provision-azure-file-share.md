---
title: Pokyny pro zřizování sdílených složek Azure.
description: Zřídí sdílené složky Azure pro použití s Azure File Sync. Společný textový blok, který je sdílen mezi dokumenty migrace.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 8cb398d1b1ec14f52d9c5fa5c122dc2e4ba4376d
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209425"
---
Sdílená složka Azure je uložená v cloudu v účtu úložiště Azure.
Tady je další úroveň problémů s výkonem.

Pokud máte vysoce aktivní sdílené složky a sdílené složky používané mnoha uživateli nebo aplikacemi, můžou dvě sdílené složky Azure dosáhnout limitu výkonu účtu úložiště.

Osvědčeným postupem je nasazování účtů úložiště s jednou sdílenou složkou.
Můžete seskupit více sdílených složek Azure do stejného účtu úložiště pro případ, že máte archivované sdílené složky nebo v nich očekáváte nízkou denní aktivitu.

Tyto požadavky se vztahují i na přímý přístup do cloudu (přes virtuální počítač Azure), než se vztahují na Azure File Sync. Pokud máte v úmyslu používat Azure File Sync jenom na těchto sdílených složkách, je v pořádku seskupení několika do jednoho účtu Azure Storage.

Pokud jste vytvořili seznam sdílených složek, pak byste měli namapovat každou sdílenou složku na účet úložiště, ve kterém se budou nacházet.

V předchozí fázi jste určili odpovídající počet sdílených složek. V tomto kroku máte vytvořenou mapování účtů úložiště ke sdíleným složkám souborů. Nasaďte nyní odpovídající počet účtů služby Azure Storage s příslušným počtem sdílených složek Azure.

Ujistěte se, že je oblast každého účtu úložiště stejná a odpovídá oblasti prostředku služby synchronizace úložiště, který jste už nasadili.

> [!CAUTION]
> Pokud 100 vytvoříte TiBou sdílenou složku Azure, tato sdílená složka může používat jenom místně redundantní možnosti redundance úložiště nebo redundantního úložiště zóny. Před použitím sdílených složek TiB 100 se podíváme na požadavky na redundanci úložiště.

Ve výchozím nastavení se sdílené složky Azure pořád vytváří s limitem 5 TiB. Vzhledem k tomu, že vytváříte nové účty úložiště, nezapomeňte postupovat podle [pokynů k vytvoření účtů úložiště, které umožní sdíleným složkám Azure s 100 TIB limity](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Další úvahou při nasazování účtu úložiště je redundance úložiště Azure. Viz: [Azure Storage možnosti redundance](../articles/storage/common/storage-redundancy.md).

Názvy vašich prostředků jsou také důležité. Pokud například seskupete pro personální oddělení více sdílených složek do účtu služby Azure Storage, měli byste účet úložiště pojmenovat správně. Podobně při pojmenovávání sdílených složek Azure byste měli používat názvy podobné těm, které se používají pro místní protějšky.