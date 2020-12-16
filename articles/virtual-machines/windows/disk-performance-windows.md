---
title: Výkon virtuálního počítače a disku – Windows
description: Přečtěte si další informace o tom, jak virtuální počítače a jejich připojené disky fungují v kombinaci s výkonem ve Windows.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: dec8b0cc33a9fffa7cac1ac9261b3c38ef5a6449
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584115"
---
# <a name="virtual-machine-and-disk-performance-windows"></a>Výkon virtuálního počítače a disku (Windows)
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Virtuální počítač neuložený v mezipaměti a omezení v mezipaměti
 U virtuálních počítačů, které mají povolené úložiště úrovně Premium a povolené ukládání do mezipaměti služby Premium Storage, platí dvě odlišná omezení šířky pásma úložiště. Pojďme pokračovat s prohlížením virtuálního počítače s Standard_D8s_v3 jako příkladem. Tady je dokumentace k [Dsv3-Series](../dv3-dsv3-series.md) a na ní Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Pojďme spustit test srovnávacích testů na tomto virtuálním počítači a kombinaci disků, které vytvoří vstupně-výstupní činnost, a můžete se seznámit s postupem testování v/v [Azure v](disks-benchmarks.md)Azure. V nástroji pro srovnávací testy vidíte, že kombinace virtuálních počítačů a disků je schopná dosáhnout 22 800 IOPS:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]