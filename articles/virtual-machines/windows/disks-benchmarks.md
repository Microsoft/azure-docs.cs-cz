---
title: Srovnávací testy vaší aplikace na Azure Disk Storage spravovaných discích
description: Přečtěte si o procesu srovnávacích testů vaší aplikace v Azure.
author: roygara
ms.author: rogarana
ms.date: 01/11/2019
ms.topic: conceptual
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: 600b456cd77d866f1365b7dadfa9ea2473db0fa4
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698761"
---
# <a name="benchmarking-a-disk"></a>Srovnávací testování disku

Srovnávací testy je proces simulace různých úloh ve vaší aplikaci a měření výkonu aplikace pro jednotlivé úlohy. Pomocí kroků popsaných v [článku návrh pro vysoký výkon](premium-storage-performance.md)jste shromáždili požadavky na výkon aplikace. Spuštěním nástrojů srovnávacích testů na virtuálních počítačích, které hostují aplikaci, můžete určit úrovně výkonu, které může vaše aplikace dosáhnout, pomocí Premium Storage. V tomto článku poskytujeme příklady srovnávacích testů standardního virtuálního počítače s DS14, které jsou zřízené pomocí disků Azure Premium Storage.

V uvedeném pořadí jsme používali běžné nástroje pro srovnávací testy IOMeter a FIO pro systémy Windows a Linux. Tyto nástroje sestaví více vláken simulujch produkční prostředí, jako je úloha, a změřte výkon systému. Pomocí nástrojů můžete také nakonfigurovat parametry jako velikost bloku a hloubku fronty, které obvykle nelze pro aplikaci změnit. Díky tomu máte větší flexibilitu pro zajištění maximálního výkonu pro virtuální počítač s vysokým rozsahem zřízeného pomocí prémiových disků pro různé typy úloh aplikací. Další informace o nástroji pro srovnávací testy najdete v [IOMeter](http://www.iometer.org/) a [FIO](http://freecode.com/projects/fio).

Pokud chcete postupovat podle níže uvedených příkladů, vytvořte standardní virtuální počítač s DS14 a připojte 11 Premium Storage disků k virtuálnímu počítači. Z 11 disků nakonfigurujte 10 disků s ukládáním do mezipaměti hostitele jako "none" a je do svazku s názvem NoCacheWrites. Nakonfigurujte ukládání do mezipaměti hostitele jako "ReadOnly" na zbývajícím disku a vytvořte svazek s názvem CacheReads s tímto diskem. Pomocí tohoto nastavení můžete zobrazit maximální výkon čtení a zápisu ze standardního virtuálního počítače DS14. Podrobné pokyny k vytvoření virtuálního počítače s DS14 pomocí Premium SSD najdete v [návrhu pro vysoký výkon](premium-storage-performance.md).

[!INCLUDE [virtual-machines-disks-benchmarking](../../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Další kroky

Projděte si náš návrh pro vysoce výkonný článek. V tomto případě vytvoříte kontrolní seznam podobný vašemu existující aplikaci pro prototyp. Pomocí nástrojů pro srovnávací testy můžete simulovat úlohy a měřit výkon v aplikaci prototypu. Díky tomu můžete určit, která disková nabídka může odpovídat nebo překračovat požadavky na výkon vaší aplikace. Pak můžete pro svou produkční aplikaci implementovat stejné pokyny.

> [!div class="nextstepaction"]
> Pokud chcete začít s [vysokým výkonem](premium-storage-performance.md) , přečtěte si článek o návrhu.