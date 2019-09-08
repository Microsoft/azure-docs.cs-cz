---
title: Ingestování dat mezipaměti HPC Azure – msrsync
description: Jak používat msrsync k přesunu dat do cíle úložiště objektů BLOB v mezipaměti HPC Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: v-erkell
ms.openlocfilehash: d49c2ba45b125f8e42ea5d10dcf3dcd68558a52c
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70775634"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Azure HPC cache data pro ingestování – metoda msrsync

Tento článek obsahuje podrobné pokyny pro použití ``msrsync`` nástroje ke kopírování dat do kontejneru úložiště objektů BLOB v Azure pro použití s mezipamětí Azure HPC.

Další informace o přesouvání dat do úložiště objektů BLOB pro mezipaměť prostředí Azure HPC najdete v tématu [přesun dat do Azure Blob Storage pro Azure HPC cache](hpc-cache-ingest.md).

``msrsync`` Nástroj lze použít k přesunu dat do cílového úložiště back-end pro mezipaměť prostředí Azure HPC. Tento nástroj je určený k optimalizaci využití šířky pásma spuštěním několika paralelních ``rsync`` procesů. Je k dispozici z GitHubu na adrese https://github.com/jbd/msrsync.

``msrsync``rozdělí zdrojový adresář do samostatných "intervalů" a potom spustí jednotlivé ``rsync`` procesy v jednotlivých intervalech.

Předběžné testování pomocí virtuálního počítače se čtyřmi jádry ukázalo při použití procesů 64 nejlepší efektivitu. ``msrsync`` Pomocí možnosti ``-p`` nastavte počet procesů na 64.

Všimněte si ``msrsync`` , že může zapisovat jenom do místních svazků a z nich. Zdroj a cíl musí být přístupný jako místní připojení na pracovní stanici, která se používá k vystavení příkazu.

Podle těchto pokynů použijte ``msrsync`` k naplnění úložiště objektů BLOB v Azure pomocí Azure HPC cache:

1. Nainstalovat ``msrsync`` a jeho požadavky (``rsync`` a Python 2,6 nebo novější)
1. Určete celkový počet souborů a adresářů, které mají být zkopírovány.

   Použijte například nástroj ``prime.py`` s argumenty ```prime.py --directory /path/to/some/directory``` (k dispozici stažením <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>).

   Pokud nepoužíváte ``prime.py``, můžete vypočítat počet položek pomocí nástroje GNU ``find`` následujícím způsobem:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Rozdělte počet položek podle 64 k určení počtu položek na proces. Toto číslo použijte s ``-f`` možností nastavení velikosti intervalů při spuštění příkazu.

1. Vydejte ``msrsync`` příkaz ke kopírování souborů:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Například tento příkaz je navržen pro přesun 11 000 souborů v 64 Process z/test/source-repository na/mnt/hpccache/Repository:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
