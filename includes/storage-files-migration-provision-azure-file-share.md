---
title: Důležité informace pro zřizování sdílených složek Azure.
description: Zřizování sdílených složek Azure pro použití s Azure File Sync. Běžný textový blok sdílený mezi dokumenty migrace.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 8cb398d1b1ec14f52d9c5fa5c122dc2e4ba4376d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209425"
---
Sdílená složka Azure se ukládá v cloudu v účtu úložiště Azure.
Je zde další úroveň výkonu aspekty zde.

Pokud máte vysoce aktivní sdílené složky – sdílené složky používané mnoha uživateli nebo aplikacemi, pak dvě sdílené složky Azure mohou dosáhnout limitu výkonu účtu úložiště.

Osvědčeným postupem je nasazení účtů úložiště s jedním souborem sdílené složky.
Do stejného účtu úložiště můžete sdružovat více sdílených složek Azure v případě, že máte archivní sdílené složky nebo očekáváte v nich nízkou každodenní aktivitu.

Tyto aspekty platí více pro přímý přístup ke cloudu (prostřednictvím virtuálního počítače Azure) než platí pro Azure File Sync. Pokud máte v plánu používat azure file sync jenom na těchto sdílených složek, pak seskupení několik do jednoho účtu úložiště Azure je v pořádku.

Pokud jste vytvořili seznam sdílených složek, měli byste každou sdílenou složku namapovat na účet úložiště, ve které budou umístěny.

V předchozí fázi jste určili příslušný počet akcií. V tomto kroku jste vytvořili mapování účtů úložiště pro sdílení souborů. Nasaďte nyní příslušný počet účtů úložiště Azure s příslušným počtem sdílených složek Azure v nich.

Ujistěte se, že oblast každého z vašich účtů úložiště je stejná a odpovídá oblasti prostředku služby Synchronizace úložiště, kterou jste už nasadili.

> [!CAUTION]
> Pokud vytvoříte 100 TiB limit sdílené složky Azure, tato sdílená složka můžete použít pouze místně redundantní úložiště nebo zóny redundantní možnosti redundance úložiště. Před použitím 100 sdílených složek TiB zvažte potřeby redundance úložiště.

Sdílené složky Azure se stále vytvářejí s limitem 5 TiB ve výchozím nastavení. Vzhledem k tomu, že vytváříte nové účty úložiště, nezapomeňte podle [pokynů vytvořit účty úložiště, které umožňují sdílené složky Azure s omezeními 100 TiB](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Dalším aspektem při nasazování účtu úložiště je redundance úložiště Azure. Viz: [Možnosti redundance azure úložiště](../articles/storage/common/storage-redundancy.md).

Názvy prostředků jsou také důležité. Pokud například seskupíte více sdílených složek pro oddělení lidských zdrojů do účtu úložiště Azure, měli byste účet úložiště odpovídajícím způsobem pojmenovat. Podobně při pojmenování sdílených složek Azure byste měli používat názvy podobné těm, které se používají pro jejich místní protějšky.