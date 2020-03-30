---
title: Ingestování dat mezipaměti Azure HPC – msrsync
description: Použití msrsync k přesunutí dat do cíle úložiště objektů Blob v mezipaměti Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: 4f8863d706d623d613ac156cf202c3b7b12f2ae0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168425"
---
# <a name="azure-hpc-cache-data-ingest---msrsync-method"></a>Ingestování dat mezipaměti Azure HPC – metoda msrsync

Tento článek obsahuje podrobné ``msrsync`` pokyny pro použití nástroje ke kopírování dat do kontejneru úložiště Objektů blob Azure pro použití s Azure HPC Cache.

Další informace o přesunu dat do úložiště objektů Blob pro azure hpc mezipaměť najdete v článek [Přesun dat do úložiště objektů blob Azure](hpc-cache-ingest.md).

Nástroj ``msrsync`` lze použít k přesunutí dat do cíle back-endového úložiště pro mezipaměť Azure HPC. Tento nástroj je navržen tak, aby ``rsync`` optimalizoval využití šířky pásma spuštěním více paralelních procesů. Je k dispozici na https://github.com/jbd/msrsyncGitHubu na adrese .

``msrsync``rozdělí zdrojový adresář na samostatné "kbelíky" ``rsync`` a pak spustí jednotlivé procesy na každém bloku.

Předběžné testování pomocí čtyřjádrového virtuálního virtuálního uživatele ukázalo nejlepší efektivitu při použití 64 procesů. Pomocí ``msrsync`` této ``-p`` možnosti můžete nastavit počet procesů na 64.

Všimněte ``msrsync`` si, že lze zapisovat pouze do a z místních svazků. Zdroj a cíl musí být přístupné jako místní připojení na pracovní stanici, která slouží k vydání příkazu.

Podle těchto pokynů ``msrsync`` použijte k naplnění úložiště objektů blob Azure pomocí mezipaměti Azure HPC:

1. Instalace ``msrsync`` a její``rsync`` předpoklady ( a Python 2.6 nebo novější)
1. Určete celkový počet souborů a adresářů, které mají být zkopírovány.

   Použijte například nástroj ``prime.py`` s ```prime.py --directory /path/to/some/directory``` argumenty (k <https://github.com/Azure/Avere/blob/master/src/clientapps/dataingestor/prime.py>dispozici stažením).

   Pokud nepoužíváte ``prime.py``, můžete vypočítat počet položek ``find`` pomocí nástroje GNU následujícím způsobem:

   ```bash
   find <path> -type f |wc -l         # (counts files)
   find <path> -type d |wc -l         # (counts directories)
   find <path> |wc -l                 # (counts both)
   ```

1. Vydělte počet položek číslem 64 a určete počet položek na proces. Toto číslo ``-f`` s možností můžete nastavit velikost bloků při spuštění příkazu.

1. Vydat ``msrsync`` příkaz pro kopírování souborů:

   ```bash
   msrsync -P --stats -p64 -f<ITEMS_DIV_64> --rsync "-ahv --inplace" <SOURCE_PATH> <DESTINATION_PATH>
   ```

   Tento příkaz je například navržen tak, aby přesunul 11 000 souborů v 64 procesech z /test/source-repository na /mnt/hpccache/repository:

   ``mrsync -P --stats -p64 -f170 --rsync "-ahv --inplace" /test/source-repository/ /mnt/hpccache/repository``
