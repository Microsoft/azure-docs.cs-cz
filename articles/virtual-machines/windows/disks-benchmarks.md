---
title: Srovnávací testy aplikace ve službě Azure Disk Storage – spravované disky
description: Další informace o procesu testování vaší aplikace v Azure.
services: virtual-machines-windows,storage
author: roygara
ms.author: rogarana
ms.date: 01/11/2019
ms.topic: article
ms.service: virtual-machines-windows
ms.tgt_pltfrm: windows
ms.subservice: disks
ms.openlocfilehash: 8db1fb3c9b3ed551cd668cf14105eb8bfb486251
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56331182"
---
# <a name="benchmarking-a-disk"></a>Srovnávací testy na disk

Srovnávací testy je proces simulace různých úloh ve své aplikaci a měření výkonu aplikace pro jednotlivá zatížení. Pomocí kroků popsaných v [návrh pro vysoký výkon článku](premium-storage-performance.md), jste shromáždili požadavky na výkon aplikace. Nástrojích pro srovnávací testy běží na virtuálních počítačích, který je hostitelem aplikace, můžete určit úrovně výkonu, které vaše aplikace může dosáhnout díky službě Premium Storage. V tomto článku nabízíme příklady srovnávací testy Standard DS14 VM zřízené disky Azure Premium Storage.

Jsme použili běžných nástrojích pro srovnávací testy Iometer a FIO, pro Windows a Linux v uvedeném pořadí. Tyto nástroje více vláken simulaci výrobní jako úlohu nejde vytvořit podřízený a měřit výkon systému. Pomocí nástrojů, můžete také nakonfigurovat parametry, jako je velikost a fronty bloku hloubka, které obvykle se nedá změnit pro aplikaci. To poskytuje větší flexibilitu umožňující prosazovat maximální výkon ve velkém měřítku virtuálním počítači zřízeném díky diskům premium pro různé typy úloh aplikací. Další informace o jednotlivých nástrojích pro srovnávací testy najdete tady: [Iometer](http://www.iometer.org/) a [FIO](http://freecode.com/projects/fio).

Postupujte podle níže uvedených příkladech, vytvořit virtuální počítače Standard DS14 a připojit 11 disků Premium Storage k virtuálnímu počítači. 11 disků konfigurace 10 disků na pomocí ukládání do mezipaměti jako "None" hostitele a prokládané do svazku volá NoCacheWrites. Konfigurace hostitele ukládání do mezipaměti jako "Jen pro čtení" na zbývající disku a vytvoření svazku názvem čtení z mezipaměti se tento disk. Pomocí tohoto nastavení budete moci zobrazit maximální výkon pro čtení a zápis z Standard DS14 virtuálního počítače. Podrobné informace o postupu vytvoření DS14 virtuálního počítače s disky premium SSD, přejděte na [návrh pro vysoký výkon](premium-storage-performance.md).

[!INCLUDE [virtual-machines-disks-benchmarking](../../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Další postup

Projděte naše návrh pro vysoký výkon článku. V něm vytvoříte kontrolní seznam podobný stávající aplikaci pro prototypu. Pomocí nástroje Benchmarking můžete simulovat zatížení a měření výkonu v aplikaci prototypu. Díky tomu můžete určit, který disk nabídky může odpovídat nebo toto vašim požadavkům na výkon aplikace. Potom můžete implementovat stejné pokyny jako pro aplikace v produkčním prostředí.

> [!div class="nextstepaction"]
> Přečtěte si článek na [návrh pro vysoký výkon](premium-storage-performance.md) začít.