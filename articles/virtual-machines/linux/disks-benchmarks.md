---
title: Srovnávací testování vaší aplikace v Azure Disk Storage
description: Přečtěte si tyto příklady srovnávacích testů pro standardní virtuální počítač DS14 zřízený pomocí disků Azure Premium Storage.
author: roygara
ms.author: rogarana
ms.date: 01/11/2019
ms.topic: how-to
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: cc17ce49517a7af22dcc357fa5f050d28a8ed551
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91279684"
---
# <a name="benchmark-your-application-on-azure-disk-storage"></a>Srovnávací test aplikace v Azure Disk Storage

Srovnávací testy je proces simulace různých úloh ve vaší aplikaci a měření výkonu aplikace pro jednotlivé úlohy. Použijte postup popsaný v [článku návrh pro vysoký výkon](../premium-storage-performance.md). Spuštěním nástrojů srovnávacích testů na virtuálních počítačích, které hostují aplikaci, můžete určit úrovně výkonu, které může vaše aplikace dosáhnout, pomocí Premium Storage. V tomto článku poskytujeme příklady srovnávacích testů standardního virtuálního počítače s DS14, které jsou zřízené pomocí disků Azure Premium Storage.

V uvedeném pořadí jsme používali běžné nástroje pro srovnávací testy IOMeter a FIO pro systémy Windows a Linux. Tyto nástroje sestaví více vláken simulujch produkční prostředí, jako je úloha, a změřte výkon systému. Pomocí nástrojů můžete také nakonfigurovat parametry jako velikost bloku a hloubku fronty, které obvykle nelze pro aplikaci změnit. Díky tomu máte větší flexibilitu pro zajištění maximálního výkonu pro virtuální počítač s vysokým rozsahem zřízeného pomocí prémiových disků pro různé typy úloh aplikací. Další informace o nástroji pro srovnávací testy najdete v [IOMeter](http://www.iometer.org/) a [FIO](http://freecode.com/projects/fio).

Pokud chcete postupovat podle níže uvedených příkladů, vytvořte standardní virtuální počítač s DS14 a připojte 11 Premium Storage disků k virtuálnímu počítači. Z 11 disků nakonfigurujte 10 disků s ukládáním do mezipaměti hostitele jako "none" a je do svazku s názvem NoCacheWrites. Nakonfigurujte ukládání do mezipaměti hostitele jako "ReadOnly" na zbývajícím disku a vytvořte svazek s názvem CacheReads s tímto diskem. Pomocí tohoto nastavení můžete zobrazit maximální výkon čtení a zápisu ze standardního virtuálního počítače DS14. Podrobné pokyny k vytvoření virtuálního počítače s DS14 pomocí prémiových disků najdete v [návrhu pro vysoký výkon](../premium-storage-performance.md).

[!INCLUDE [virtual-machines-disks-benchmarking](../../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Další kroky

Pokud chcete mít [vysoký výkon](../premium-storage-performance.md), přejděte k našemu článku o návrhu.

V tomto článku vytvoříte kontrolní seznam podobný vašemu existující aplikaci pro prototyp. Pomocí nástrojů pro srovnávací testy můžete simulovat úlohy a měřit výkon v aplikaci prototypu. Díky tomu můžete určit, která disková nabídka může odpovídat nebo překračovat požadavky na výkon vaší aplikace. Pak můžete pro svou produkční aplikaci implementovat stejné pokyny.