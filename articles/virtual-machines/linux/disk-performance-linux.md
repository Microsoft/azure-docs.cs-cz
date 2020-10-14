---
title: Výkon disků a virtuálních počítačů
description: Další informace o tom, jak virtuální počítače a jejich připojené disky fungují v kombinaci pro výkon
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 3b849640bc7adb38fe51c3c7a9dda2d1d14f35fa
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92017246"
---
# <a name="virtual-machine-and-disk-performance"></a>Výkon disků a virtuálních počítačů
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Virtuální počítač neuložený v mezipaměti a omezení v mezipaměti
U virtuálních počítačů, které mají povolené úložiště úrovně Premium, i úložiště Premium Storage je povolené dvě odlišná omezení šířky pásma úložiště. Pojďme pokračovat s prohlížením virtuálního počítače s Standard_D8s_v3 jako příkladem. Tady je dokumentace k [Dsv3-Series](../dv3-dsv3-series.md) a na ní Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Pojďme spustit test srovnávacích testů na tomto virtuálním počítači a kombinaci disků, které vytvoří vstupně-výstupní činnost, a můžete se seznámit s postupem testování v/v [Azure v](disks-benchmarks.md)Azure. V nástroji pro srovnávací testy vidíte, že kombinace virtuálních počítačů a disků je schopná dosáhnout 22 800 IOPS:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]