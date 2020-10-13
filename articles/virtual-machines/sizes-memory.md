---
title: Velikosti virtuálních počítačů Azure – paměť | Microsoft Docs
description: Obsahuje seznam různých velikostí optimalizovaných pro paměť, které jsou dostupné pro virtuální počítače v Azure. Uvádí informace o počtu vCPU, datových discích a síťových adaptérů a propustnosti úložiště a šířce pásma sítě pro velikosti v této sérii.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
keywords: Izolace virtuálních počítačů, izolovaný virtuální počítač, izolace, izolovaný režim
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: ea58eeac5a77f2e1eaf2a7756de6d36a3fc0626b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87825493"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>Paměťově optimalizované velikosti virtuálních počítačů

Paměťově optimalizované velikosti virtuálních počítačů nabízejí vysoký poměr paměti k procesoru, který je skvělý pro servery relačních databází, střední až velké mezipaměti a analýzu v paměti. Tento článek poskytuje informace o počtu vCPU, datových discích a síťových adaptérů i propustnosti úložiště a šířce pásma sítě pro každou velikost v tomto seskupení.

- [Dv2 a DSv2-Series](dv2-dsv2-series-memory.md), následné až do původní řady D-Series, nabízí výkonnější procesor. Dv2-Series má přibližně 35% rychlejší než řada D-Series. Běží na Intel &reg; Xeon &reg; 8171M 2,1 GHz (Skylake) nebo Intel &reg; Xeon &reg; e5-2673 V4 2,3 GHz (Broadwell) nebo procesory Intel &reg; xeon &reg; E5-2673 V3 2,4 ghz (Haswell) a s 2,0 technologií Intel Turbo zvyšovat. Řada Dv2-series má stejnou konfiguraci paměti a disku jako řada D.

    Dv2 a DSv2-Series jsou ideální pro aplikace, které vyžadují rychlejší vCPU, lepší dočasný výkon úložiště nebo mají vyšší nároky na paměť. Nabízejí výkonnou kombinaci pro mnoho podnikových aplikací.

- [Eav4 a Easv4-Series](eav4-easv4-series.md) využívají procesor AMD 2.35 EPYC<sup>TM</sup> 7452 v konfiguraci s více VLÁKNy s až 256Mb mezipamětí L3 a zvyšují možnosti spouštění většiny paměťově optimalizovaných úloh. Řady Eav4-Series a Easv4-Series mají stejnou konfiguraci paměti a disku jako Ev3 & Esv3-Series.

- [Ev3 a Esv3-Series](ev3-esv3-series.md) Intel &reg; Xeon &reg; 8171M 2,1 GHz (Skylake) nebo procesor Intel &reg; Xeon &reg; E5-2673 2,3 v4 (Broadwell) v konfiguraci s více vlákny, které poskytují lepší hodnotu pro většinu úloh pro obecné účely a přinášejí Ev3 do sblížení s virtuálními počítači pro obecné účely většiny ostatních cloudů. Paměť se rozšířila (od 7 GiB/vCPU do 8 GiB/vCPU), zatímco limity disku a sítě se přizpůsobily na základě počtu jader, které je možné přesunout do technologie Hyper-Threading. Ev3 je následná velikost virtuálních počítačů s vysokou pamětí pro řady D/Dv2.

- [Ev4 a Esv4-Series](ev4-esv4-series.md) běží v 2 &reg; . generaci procesorů Intel Xeon &reg; Platinum 8272CL (Cascade Lake) v konfiguraci s více vlákny, jsou ideální pro různé podnikové aplikace náročné na paměť a funkce až 504 GIB paměti RAM. Nabízí [ &reg; technologii Intel Turbo pro zvýšení úrovně 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [ &reg; technologie Intel Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) a [Intel &reg; Advanced Vector Extensions 512 (Intel AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Ev4 a Esv4-Series neobsahují místní dočasný disk. Další informace najdete v tématu  [velikosti virtuálních počítačů Azure bez místního dočasného disku](azure-vms-no-temp-disk.md).

- [Edv4 a Edsv4-Series](edv4-edsv4-series.md) běží v 2. generaci &reg; &reg; procesorů Intel Xeon Platinum 8272CL (Cascade Lake), ideální pro extrémně velké databáze nebo jiné aplikace, které využívají vysoké počty vCPU a velké množství paměti. Kromě toho tyto velikosti virtuálních počítačů zahrnují rychlé a větší místní úložiště SSD pro aplikace, které využívají nízkou latenci a vysoce urychlené místní úložiště. Vyplývají ze všech základních rychlostí Turbo 3,4 GHz, [Intel &reg; Turbo zvyšování technologie 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [ &reg; technologie Intel Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) a [Intel &reg; Advanced Vector Extensions 512 (Intel AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html).

- [Řada M-Series](m-series.md) nabízí vysoký počet vCPU (až 128 vCPU) a velké množství paměti (až 3,8 TIB). Je to také ideální pro extrémně velké databáze nebo jiné aplikace, které využívají vysoké počty vCPU a velké množství paměti.

- [Mv2-Series](mv2-series.md) nabízí nejvyšší počet vCPU (až 416 vCPU) a největší paměť (až 11,4 TIB) libovolného virtuálního počítače v cloudu. Je ideální pro extrémně velké databáze nebo jiné aplikace, které využívají výhody vysokého počtu vCPU a velké množství paměti.

Azure COMPUTE nabízí velikosti virtuálních počítačů, které jsou izolované na konkrétní typ hardwaru a vyhrazené pro jednoho zákazníka. Tyto velikosti virtuálních počítačů jsou nejvhodnější pro úlohy, které vyžadují vysokou úroveň izolace od jiných zákazníků pro úlohy zahrnující prvky, jako jsou dodržování předpisů a zákonné požadavky. Zákazníci si také můžou rozdělit prostředky těchto izolovaných virtuálních počítačů s využitím [podpory Azure pro vnořené virtuální počítače](https://azure.microsoft.com/blog/nested-virtualization-in-azure/). Další informace o možnostech izolovaného virtuálního počítače najdete na stránkách pro rodiny virtuálních počítačů.

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované pro výpočty](sizes-compute.md)
- [Optimalizované pro úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.

Další informace o tom, jak Azure vyjmenovává své virtuální počítače, najdete v tématu zásady [vytváření názvů virtuálních počítačů Azure](./vm-naming-conventions.md).