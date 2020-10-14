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
ms.openlocfilehash: 14e74bfbcd087ccc1d8c5f2f10a8e44ed37cce84
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016452"
---
![Nabídka metriky](media/vm-disk-performance/utilization-metrics-example/fio-output.jpg)

Standard_D8s_v3 ale může získat celkový počet 28 600 IOPs, a to pomocí metriky umožňuje prozkoumat, co se prochází, a identifikovat naše kritické body v/v úložiště. Nejprve najděte nabídku metriky na levé straně a vyberte ji:

![Nabídka metriky](media/vm-disk-performance/utilization-metrics-example/metrics-menu.jpg)

Nejdřív se podíváme na naši procentuální metriku **využité procentní hodnoty IOPS virtuálního počítače v mezipaměti** :

![Procento využitého procenta IOPS v mezipaměti virtuálních počítačů](media/vm-disk-performance/utilization-metrics-example/vm-cached.jpg)

Tato metrika oznamuje z 16 000 vstupně-výstupních operací, které jsou přiděleny do mezipaměti IOPs na virtuálním počítači, se používá 61%. To znamená, že kritická místa v/v úložiště nejsou s disky uloženými do mezipaměti, protože nejsou v 100%. Pojďme se teď podívat na naše procento metriky **neuloženého počtu vstupně** -výstupních operací za sekundu na virtuálním počítači:

![Procento využitých vstupně-výstupních operací IOPS pro virtuální počítače](media/vm-disk-performance/utilization-metrics-example/vm-uncached.jpg)

Tato metrika je na 100%, oznamuje, že se používají všechny 12 800 IOPs přidělené necache IOPs na virtuálním počítači. Jedním ze způsobů, jak to napravit, je změna velikosti našeho virtuálního počítače na větší velikost, která může zpracovávat další vstupně-výstupní operace. Než to provedeme, Podívejme se na disk připojený a podívejte se, kolik IOPs se zobrazuje. Nejdřív se podíváme na disk s operačním systémem tím, že prohlížíte **spotřebované procento IOPS disku s operačním systémem**:

![Procento využitého počtu IOPS disku operačního systému](media/vm-disk-performance/utilization-metrics-example/os-disk.jpg)

Tato metrika oznamuje, že pro tento disk s operačním systémem P30 byl zřízen tento 5 000 počet vstupně-výstupních operací za sekundu, přibližně 90% z něj. To znamená, že na disku s operačním systémem tady není žádné místo. Teď se podíváme na datové disky, které jsou připojené k virtuálnímu počítači, a Prohlédněte si **spotřebované procento počtu vstupně**-výstupních operací datového disku:

![Procento spotřebovaného počtu vstupně-výstupních operací datového disku](media/vm-disk-performance/utilization-metrics-example/data-disks-no-splitting.jpg)

Tato metrika oznamuje, že průměrně spotřebované procento vstupně-výstupních operací na všech připojených discích je okolo 42%. Toto procento se počítá na základě IOPs, které disky používají a které se nezpracovávají z mezipaměti hostitele. Podívejme se do hlubšího zobrazení této metriky, abyste viděli použití **rozdělení** na tyto metriky a rozdělení hodnotou logické jednotky (LUN):

![Procento využitého počtu vstupně-výstupních operací datového disku s dělením](media/vm-disk-performance/utilization-metrics-example/data-disks-splitting.jpg)

Tato metrika oznamuje, že datové disky připojené na logických jednotkách 3 a 2 využívají přibližně 85% jejich zřízené IOPs. Tady je diagram toho, co v vstupně-výstupních operacích od architektury virtuálních počítačů a disků vypadá:

![Příklad diagramu pro metriky v/v úložiště](media/vm-disk-performance/utilization-metrics-example/metrics-diagram.jpg)
