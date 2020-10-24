---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 10/12/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 8eff9da82fdfa5749fd1c2bc04652d5c8ce8dfd2
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518047"
---
![Snímek obrazovky s výstupem f i o zobrazuje zvýrazněný r = 22.8 k.](media/vm-disk-performance/utilization-metrics-example/fio-output.jpg)

Standard_D8s_v3 může dosáhnout celkem 28 600 IOPS. Pomocí metriky si podívejme na to, co se prochází, a Identifikujte naše kritické body v/v úložiště. V levém podokně vyberte **metriky**:

![Snímek obrazovky znázorňující metriky zvýrazněné v levém podokně](media/vm-disk-performance/utilization-metrics-example/metrics-menu.jpg)

Nejdřív se podíváme na naši procentuální metriku **využité procentní hodnoty IOPS virtuálního počítače v mezipaměti** :

![Snímek obrazovky zobrazující procento spotřebované V M v/v v mezipaměti](media/vm-disk-performance/utilization-metrics-example/vm-cached.jpg)

Tato metrika oznamuje, že se používá 61% z 16 000 IOPS přidělených do mezipaměti IOPS na virtuálním počítači. Toto procento znamená, že Nekritická místa v/v úložiště nejsou s disky, které jsou uložené v mezipaměti, protože nejsou v 100%. Teď se podíváme na naše procento metriky **neuloženého počtu vstupně** -výstupních operací IOPS:

![Snímek obrazovky zobrazující procento využité V M neukládání do vyrovnávací paměti V/v](media/vm-disk-performance/utilization-metrics-example/vm-uncached.jpg)

Tato metrika je 100%. Oznamujeme, že se používají všechny 12 800 vstupně-výstupních operací přidělených pro neuložené IOPS virtuálních počítačů na virtuálním počítači. Jedním ze způsobů, jak tento problém vyřešit, je změnit velikost našeho virtuálního počítače na větší velikost, která může zpracovávat další vstupně-výstupní operace. Než to ale uděláte, Podívejme se na připojený disk, kde zjistíte, kolik IOPS se zobrazuje. Podívejte se na disk s operačním systémem. Prohlédněte si **spotřebované procento IOPS disku s operačním systémem**:

![Snímek obrazovky ukazující procento spotřebovaného disku v/v](media/vm-disk-performance/utilization-metrics-example/os-disk.jpg)

Tato metrika oznamuje, že se používá přibližně 90% za 5 000 IOPS zřízené pro tento disk s P30 operačním systémem. Toto procento znamená, že na disku s operačním systémem není žádné kritické body. Teď Prohlédněte data, která jsou připojená k virtuálnímu počítači, a Prohlédněte si **spotřebované procento počtu vstupně**-výstupních operací pro datový disk:

![Snímek obrazovky zobrazující procento spotřebovaného datového disku v hodnotě P S](media/vm-disk-performance/utilization-metrics-example/data-disks-no-splitting.jpg)

Tato metrika nám oznamuje, že procento spotřebovaného počtu vstupně-výstupních operací na všech připojených discích je okolo 42%. Toto procento se počítá na základě IOPS, které disky používají, a které nejsou obsluhovány z mezipaměti hostitele. Pojďme do této metriky přiblížit použitím *rozdělení* na tyto metriky a rozdělením podle hodnoty logické jednotky (LUN):

![Snímek obrazovky znázorňující spotřebované procento datových disků v hodnotě P s dělením](media/vm-disk-performance/utilization-metrics-example/data-disks-splitting.jpg)

Tato metrika nám oznamuje, že datové disky připojené k logickým jednotkám 3 a 2 využívají přibližně 85% jejich zřízené IOPS. Tady je diagram toho, co v/v funguje z architektury virtuálních počítačů a disků:

![Příklad metriky úložiště – metriky v systému](media/vm-disk-performance/utilization-metrics-example/metrics-diagram.jpg)
