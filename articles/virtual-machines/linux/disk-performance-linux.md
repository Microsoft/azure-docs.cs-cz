---
title: Výkon virtuálního počítače a disku – Linux
description: Přečtěte si další informace o tom, jak virtuální počítače a jejich připojené disky fungují v kombinaci pro výkon systému Linux.
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 2c48672bcfd8c552b36e3ae0807135924669c1d9
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591850"
---
# <a name="virtual-machine-and-disk-performance-linux"></a>Výkon virtuálního počítače a disku (Linux)
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Virtuální počítač neuložený v mezipaměti a omezení v mezipaměti
Virtuální počítače, které jsou povolené pro úložiště Premium Storage i Storage úrovně Premium, mají dvě odlišná omezení šířky pásma úložiště. Pojďme se podívat na virtuální počítač s Standard_D8s_v3 jako příklad. Tady je dokumentace k [Dsv3-Series](../dv3-dsv3-series.md) a Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Pojďme spustit test srovnávacích testů na tomto virtuálním počítači a kombinaci disků, která vytváří vstupně-výstupní činnost. Informace o tom, jak srovnávací službu Storage v Azure, najdete v tématu [srovnávací testy vaší aplikace na Azure Disk Storage](disks-benchmarks.md). V nástroji pro srovnávací testy vidíte, že kombinace virtuálních počítačů a disků může dosahovat 22 800 IOPS:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]
