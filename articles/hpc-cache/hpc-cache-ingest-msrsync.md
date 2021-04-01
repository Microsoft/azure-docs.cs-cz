---
title: Ingestování dat mezipaměti HPC Azure – msrsync
description: Jak používat msrsync k přesunu dat do cíle úložiště objektů BLOB v mezipaměti HPC Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: 323ecd6a2dd001c3c8df1b2ec15f0ae8402ec70c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87092401"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Azure HPC cache data pro ingestování – metoda msrsync

Tento článek obsahuje podrobné pokyny pro použití ``msrsync`` nástroje ke kopírování dat do kontejneru úložiště objektů BLOB v Azure pro použití s mezipamětí Azure HPC.

Další informace o přesouvání dat do úložiště objektů BLOB pro mezipaměť HPC Azure najdete v tématu [přesun dat do úložiště objektů BLOB v Azure](hpc-cache-ingest.md).

``msrsync``Nástroj lze použít k přesunu dat do cíle úložiště back-endu pro mezipaměť prostředí Azure HPC. Tento nástroj je určený k optimalizaci využití šířky pásma spuštěním několika paralelních ``rsync`` procesů. Je k dispozici z GitHubu na adrese https://github.com/jbd/msrsync .

``msrsync`` rozdělí zdrojový adresář do samostatných "intervalů" a potom spustí jednotlivé ``rsync`` procesy v jednotlivých intervalech.

Předběžné testování pomocí virtuálního počítače se čtyřmi jádry ukázalo při použití procesů 64 nejlepší efektivitu. Pomocí ``msrsync`` možnosti ``-p`` nastavte počet procesů na 64.

Všimněte si, že ``msrsync`` může zapisovat jenom do místních svazků a z nich. Zdroj a cíl musí být přístupný jako místní připojení na pracovní stanici, která se používá k vystavení příkazu.

Podle těchto pokynů použijte ``msrsync`` k naplnění úložiště objektů BLOB v Azure pomocí Azure HPC cache:

1. Nainstalovat ``msrsync`` a jeho požadavky ( ``rsync`` a Python 2,6 nebo novější)
1. Určete celkový počet souborů a adresářů, které mají být zkopírovány.

   Použijte například nástroj ``prime.py`` s argumenty ```prime.py --directory /path/to/some/directory``` (k dispozici stažením <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py> ).

   Pokud nepoužíváte ``prime.py`` , můžete vypočítat počet položek pomocí nástroje GNU následujícím ``find`` způsobem:

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
