---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2303d36e93cecfca03894a8b0e55458c03b13d78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73412954"
---
**Odchozí datové přenosy:** [Odchozí datové přenosy](https://azure.microsoft.com/pricing/details/bandwidth/) (data z datových center Azure) se účtují z využití šířky pásma.

**Transakce**: Bude se vám účtovat počet transakcí, které provádíte na standardním spravovaném disku. Pro standardní SSD je každá vstupně-vaoperace menší nebo rovna 256 KiB propustností považována za jednu vstupně-va. Vstupně-v operace větší než 256 KiB propustnost jsou považovány za více V/O velikosti 256 KiB. U standardních pevných disků je každá operace vstupně-operace považována za jednu transakci bez ohledu na velikost vstupně-va.

Podrobné informace o cenách pro spravované disky, včetně transakčních nákladů, naleznete v [tématu Ceny spravovaných disků](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Poplatek za rezervaci virtuálního počítače ultra disku

Virtuální počítače Azure mají možnost označit, pokud jsou kompatibilní s ultra disky. Virtuální počítač kompatibilní s ultra diskem přiděluje vyhrazenou kapacitu šířky pásma mezi výpočetní instanci virtuálního počítače a jednotkou škálování blokového úložiště, aby optimalizoval výkon a snížil latenci. Přidání této funkce na virtuální ms má za následek poplatek za rezervaci, který je pouze vynucený, pokud jste povolili funkci ultra disku na virtuálním počítači bez připojení ultra disk k němu. Pokud je ultra disk připojen k virtuálnímu počítači kompatibilnímu s ultra diskem, nebude tento poplatek použit. Tento poplatek se účtuje za virtuální procesor zřízený na virtuálním počítači. 

> [!Note]
> U [omezených velikostí virtuálních virtuálních počítačů jádra](../articles/virtual-machines/linux/constrained-vcpu.md)je rezervační poplatek založen na skutečném počtu virtuálních procesorů a nikoli na omezených jádrech. Za Standard_E32-8s_v3 bude rezervační poplatek založen na 32 jádrech. 

Podrobnosti o cenách ultra disků najdete na [stránce s cenami azure disků.](https://azure.microsoft.com/pricing/details/managed-disks/)

### <a name="azure-disk-reservation"></a>Rezervace disku Azure

Rezervace disku je možnost zakoupit jeden rok diskového úložiště předem se slevou, což snižuje celkové náklady. Při nákupu rezervace disku vyberete konkrétní skladovou položku disku v cílové oblasti, například 10 disků SSD P30 (1TiB) v oblasti Východní USA 2 po dobu jednoho roku. Prostředí rezervace je podobné jako instance rezervovaného virtuálního počítače (VM). Můžete svázat rezervace virtuálních počítačů a disků a maximalizovat tak své úspory. Prozatím nabízí rezervace disků Azure plán ročního závazku pro prémiové ssd skutážky od P30 (1TiB) do P80 (32 TiB) ve všech produkčních oblastech. Další podrobnosti o cenách vyhrazených disků najdete na [stránce s cenami disků Azure Disks](https://azure.microsoft.com/pricing/details/managed-disks/).