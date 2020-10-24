---
title: Výkon disků a virtuálních počítačů
description: Další informace o tom, jak virtuální počítače a jejich připojené disky fungují v kombinaci pro výkon
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 092368bb66784a00d5116da0b6be6513f8ebb261
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518049"
---
# <a name="virtual-machine-and-disk-performance"></a>Výkon disků a virtuálních počítačů
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>Virtuální počítač neuložený v mezipaměti a omezení v mezipaměti
Virtuální počítače, které jsou povolené pro úložiště Premium Storage i Storage úrovně Premium, mají dvě odlišná omezení šířky pásma úložiště. Pojďme se podívat na virtuální počítač s Standard_D8s_v3 jako příklad. Tady je dokumentace k [Dsv3-Series](../dv3-dsv3-series.md) a Standard_D8s_v3:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

Pojďme spustit test srovnávacích testů na tomto virtuálním počítači a kombinaci disků, která vytváří vstupně-výstupní činnost. Informace o tom, jak srovnávací službu Storage v Azure, najdete v tématu [srovnávací testy vaší aplikace na Azure Disk Storage](disks-benchmarks.md). V nástroji pro srovnávací testy vidíte, že kombinace virtuálních počítačů a disků může dosahovat 22 800 IOPS:

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]
