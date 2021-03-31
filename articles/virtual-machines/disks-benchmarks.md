---
title: Srovnávací test aplikace v Azure Disk Storage
description: Přečtěte si o procesu srovnávacích testů vaší aplikace v Azure.
author: roygara
ms.author: rogarana
ms.date: 01/29/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: bfda14acc2e50005e25faafa3037805af871c1df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99094587"
---
# <a name="benchmark-a-disk"></a>Srovnávací test disku

Srovnávací testy je proces simulace různých úloh ve vaší aplikaci a měření výkonu aplikace pro jednotlivé úlohy. Pomocí kroků popsaných v [článku návrh pro vysoký výkon](premium-storage-performance.md)jste shromáždili požadavky na výkon aplikace. Spuštěním nástrojů srovnávacích testů na virtuálních počítačích, které hostují aplikaci, můžete určit úrovně výkonu, které aplikace může dosáhnout pomocí Premium SSD. V tomto článku vám poskytneme příklady srovnávacích testů pro virtuální počítač s Standard_D8ds_v4em zřízeným pomocí Azure Premium SSD.

V uvedeném pořadí jsme používali běžné nástroje pro srovnávací testy DiskSpd a FIO pro systémy Windows a Linux. Tyto nástroje sestaví více vláken simulujch produkční prostředí, jako je úloha, a změřte výkon systému. Pomocí nástrojů můžete také nakonfigurovat parametry jako velikost bloku a hloubku fronty, které obvykle nelze pro aplikaci změnit. Díky tomu máte větší flexibilitu pro zajištění maximálního výkonu na vysoce škálovatelném virtuálním počítači s SSD Premium pro různé typy úloh aplikací. Další informace o nástroji pro srovnávací testy najdete v [DiskSpd](https://github.com/Microsoft/diskspd/wiki/) a [FIO](http://freecode.com/projects/fio).

Pokud chcete postupovat podle níže uvedených příkladů, vytvořte Standard_D8ds_v4 a připojte k virtuálnímu počítači čtyři SSD úrovně Premium. Na čtyřech discích nakonfigurujte tři s ukládáním do mezipaměti hostitele jako "none" a propruhujte je na svazek s názvem NoCacheWrites. Nakonfigurujte ukládání do mezipaměti hostitele jako "ReadOnly" na zbývajícím disku a vytvořte svazek s názvem CacheReads s tímto diskem. Pomocí tohoto nastavení můžete zobrazit maximální výkon čtení a zápisu z Standard_D8ds_v4 virtuálního počítače. Podrobné pokyny k vytvoření Standard_D8ds_v4 s využitím Premium SSD najdete v tématu [navrhování pro vysoký výkon](premium-storage-performance.md).

[!INCLUDE [virtual-machines-disks-benchmarking](../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Další kroky

Pokud chcete mít [vysoký výkon](premium-storage-performance.md), přejděte k našemu článku o návrhu.

V tomto článku vytvoříte kontrolní seznam podobný vašemu existující aplikaci pro prototyp. Pomocí nástrojů pro srovnávací testy můžete simulovat úlohy a měřit výkon v aplikaci prototypu. Díky tomu můžete určit, která disková nabídka může odpovídat nebo překračovat požadavky na výkon vaší aplikace. Pak můžete pro svou produkční aplikaci implementovat stejné pokyny.
