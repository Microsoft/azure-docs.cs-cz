---
title: Ingestování dat mezipaměti HPC Azure – msrsync
description: Jak používat msrsync k přesunu dat do cíle úložiště objektů BLOB v mezipaměti HPC Azure
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 4f8863d706d623d613ac156cf202c3b7b12f2ae0
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168425"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Azure HPC cache data pro ingestování – metoda msrsync

Tento článek obsahuje podrobné pokyny k použití nástroje ``msrsync`` ke kopírování dat do kontejneru úložiště objektů BLOB v Azure pro použití s mezipamětí Azure HPC.

Další informace o přesouvání dat do úložiště objektů BLOB pro mezipaměť HPC Azure najdete v tématu [přesun dat do úložiště objektů BLOB v Azure](hpc-cache-ingest.md).

Nástroj ``msrsync`` lze použít k přesunu dat do cílového úložiště back-end pro mezipaměť prostředí Azure HPC. Tento nástroj je určený k optimalizaci využití šířky pásma spuštěním několika paralelních procesů ``rsync``. Je k dispozici z GitHubu na https://github.com/jbd/msrsync.

``msrsync`` rozdělí zdrojový adresář do samostatných "intervalů" a potom spustí jednotlivé procesy ``rsync`` v každém intervalu.

Předběžné testování pomocí virtuálního počítače se čtyřmi jádry ukázalo při použití procesů 64 nejlepší efektivitu. Pomocí možnosti ``msrsync`` ``-p`` nastavte počet procesů na 64.

Všimněte si, že ``msrsync`` můžou zapisovat jenom do místních svazků a z nich. Zdroj a cíl musí být přístupný jako místní připojení na pracovní stanici, která se používá k vystavení příkazu.

Podle těchto pokynů použijte ``msrsync`` k naplnění služby Azure Blob Storage pomocí Azure HPC cache:

1. Nainstalovat ``msrsync`` a její požadavky (``rsync`` a Python 2,6 nebo novější)
1. Určete celkový počet souborů a adresářů, které mají být zkopírovány.

   Použijte například ``prime.py`` nástrojů s argumenty ```prime.py --directory /path/to/some/directory``` (k dispozici stažením <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>).

   Pokud nepoužíváte ``prime.py``, můžete vypočítat počet položek pomocí nástroje ``find`` GNU následujícím způsobem:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Rozdělte počet položek podle 64 k určení počtu položek na proces. Toto číslo použijte s možností ``-f`` pro nastavení velikosti kontejnerů při spuštění příkazu.

1. Vydejte příkaz ``msrsync`` ke kopírování souborů:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Například tento příkaz je navržen pro přesun 11 000 souborů v 64 Process z/test/source-repository na/mnt/hpccache/Repository:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
