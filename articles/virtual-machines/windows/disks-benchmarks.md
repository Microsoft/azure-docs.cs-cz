---
title: Porovnání aplikace v úložišti disků Azure
description: Přečtěte si o procesu benchmarkingu vaší aplikace v Azure.
author: roygara
ms.author: rogarana
ms.date: 01/11/2019
ms.topic: conceptual
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: 77e542e9bff399e58b433286385864478bcb5076
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75719541"
---
# <a name="benchmarking-a-disk"></a>Porovnání disku

Benchmarking je proces simulace různých úloh ve vaší aplikaci a měření výkonu aplikace pro každou úlohu. Pomocí kroků popsaných v [návrhu článku s vysokým výkonem](premium-storage-performance.md)jste shromáždili požadavky na výkon aplikace. Spuštěním nástrojů pro srovnávání na virtuálních počítačích hostujících aplikaci můžete určit úrovně výkonu, které může vaše aplikace dosáhnout pomocí úložiště Premium. V tomto článku vám poskytneme příklady srovnávání standardního virtuálního počítače DS14 zřízeného disky azure úložiště.

Použili jsme společné srovnávací nástroje Iometer a FIO, pro Windows a Linux. Tyto nástroje vytvořit více vláken simulující produkční jako zatížení a měřit výkon systému. Pomocí nástrojů můžete také nakonfigurovat parametry, jako je velikost bloku a hloubka fronty, které obvykle nelze změnit pro aplikaci. To vám dává větší flexibilitu při řízení maximálního výkonu na virtuálním počítači ve vysokém měřítku zřízeného s disky premium pro různé typy úloh aplikací. Chcete-li se dozvědět více o každém srovnávacím nástroji, navštivte [Iometer](http://www.iometer.org/) a [FIO](http://freecode.com/projects/fio).

Chcete-li postupovat podle níže uvedených příkladů, vytvořte standardní virtuální modul DS14 a připojte k virtuálnímu počítače 11 disků úložiště Premium. Z 11 disků nakonfigurujte 10 disků s ukládáním do mezipaměti hostitele jako "Žádný" a proklánějte je do svazku nazvaného NoCacheWrites. Nakonfigurujte ukládání hostitelů do mezipaměti jako "Jen pro čtení" na zbývajícím disku a vytvořte svazek s názvem CacheReads s tímto diskem. Pomocí tohoto nastavení můžete zobrazit maximální výkon čtení a zápisu ze standardního virtuálního počítači DS14. Podrobné kroky k vytvoření virtuálního účtu DS14 s prémiovými ssd disponováním najdete v [návrhu,](premium-storage-performance.md)kde najdete vysoký výkon .

[!INCLUDE [virtual-machines-disks-benchmarking](../../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Další kroky

Přejděte k [našemu](premium-storage-performance.md)článku o navrhování pro vysoký výkon .

V tomto článku vytvoříte kontrolní seznam podobný vaší existující aplikaci pro prototyp. Pomocí nástrojů benchmarkingu můžete simulovat úlohy a měřit výkon v prototypové aplikaci. Tímto způsobem můžete určit, které nabídky disku může odpovídat nebo překonat požadavky na výkon aplikace. Potom můžete implementovat stejné pokyny pro produkční aplikaci.