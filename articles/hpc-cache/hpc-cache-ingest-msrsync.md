---
title: Ingestování dat ve verzi Preview mezipaměti prostředí Azure HPC – msrsync
description: Jak používat msrsync k přesunu dat do cíle úložiště objektů BLOB v mezipaměti HPC Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: rohogue
ms.openlocfilehash: 6eac6c367be42021a4654f85c8f4ec980c9f6925
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255277"
---
# <a name="azure-hpc-cache-preview-data-ingest---msrsync-method"></a>Azure HPC cache (Preview) data ingestování – metoda msrsync

Tento článek obsahuje podrobné pokyny k použití nástroje ``msrsync`` ke kopírování dat do kontejneru úložiště objektů BLOB v Azure pro použití s mezipamětí Azure HPC.

Další informace o přesouvání dat do úložiště objektů BLOB pro mezipaměť prostředí Azure HPC najdete v tématu [přesun dat do Azure Blob Storage pro Azure HPC cache](hpc-cache-ingest.md).

Nástroj ``msrsync`` se dá použít k přesunu dat do cílového úložiště back-endu pro mezipaměť prostředí Azure HPC. Tento nástroj je určený k optimalizaci využití šířky pásma spuštěním několika paralelních procesů @no__t 0. Je k dispozici z GitHubu na adrese https://github.com/jbd/msrsync.

``msrsync`` rozdělí zdrojový adresář do samostatných "intervalů" a potom spustí jednotlivé procesy ``rsync`` v každém intervalu.

Předběžné testování pomocí virtuálního počítače se čtyřmi jádry ukázalo při použití procesů 64 nejlepší efektivitu. Pomocí možnosti ``msrsync`` ``-p`` Nastavte počet procesů na 64.

Všimněte si, že ``msrsync`` může zapisovat pouze do místních svazků a z nich. Zdroj a cíl musí být přístupný jako místní připojení na pracovní stanici, která se používá k vystavení příkazu.

Podle těchto pokynů použijte ``msrsync`` k naplnění úložiště objektů BLOB v Azure pomocí Azure HPC cache:

1. Nainstalovat ``msrsync`` a její požadavky (``rsync`` a Python 2,6 nebo novější)
1. Určete celkový počet souborů a adresářů, které mají být zkopírovány.

   Například použijte nástroj ``prime.py`` s argumenty ```prime.py --directory /path/to/some/directory``` (k dispozici stažením <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>).

   Pokud nepoužíváte ``prime.py``, můžete vypočítat počet položek pomocí nástroje GNU ``find`` následujícím způsobem:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Rozdělte počet položek podle 64 k určení počtu položek na proces. Pomocí tohoto čísla s možností ``-f`` nastavíte velikost kontejnerů při spuštění příkazu.

1. Vydejte příkaz ``msrsync`` ke kopírování souborů:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Například tento příkaz je navržen pro přesun 11 000 souborů v 64 Process z/test/source-repository na/mnt/hpccache/Repository:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
