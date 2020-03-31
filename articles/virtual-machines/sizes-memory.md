---
title: Velikosti virtuálních zařízení Azure – paměť | Dokumenty společnosti Microsoft
description: Uvádí různé velikosti optimalizované pro paměť, které jsou k dispozici pro virtuální počítače v Azure. Obsahuje informace o počtu virtuálních procesorů, datových disků a síťových síťových karty, jakož i propustnost úložiště a šířku pásma sítě pro velikosti v této řadě.
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
keywords: Izolace virtuálních virtuálních bylv,izolovaný virtuální montovna,izolace,izolovaná
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 26a44cdf3e57508cebb070669a8c1f473a59df8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77493524"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>Velikosti virtuálních strojů optimalizované pro paměť

Velikosti virtuálních počítače optimalizované pro paměť nabízejí vysoký poměr paměti k procesoru, který je skvělý pro relační databázové servery, střední až velké mezipaměti a analýzy v paměti. Tento článek obsahuje informace o počtu virtuálních procesorů, datových disků a síťových připojení, jakož i propustnost úložiště a šířku pásma sítě pro každou velikost v tomto seskupení.

- [Dv2 a DSv2-series](dv2-dsv2-series-memory.md), pokračování původní řady D, je vybaveno výkonnějším procesorem. Řada Dv2 je o 35 % rychlejší než řada D. Běží na Intel® Xeon® 8171M 2.1 GHz (Skylake) nebo Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) nebo Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) procesory, a s Intel Turbo Boost Technology 2.0. Řada Dv2-series má stejnou konfiguraci paměti a disku jako řada D.

    Řady Dv2 a DSv2 jsou ideální pro aplikace, které vyžadují rychlejší virtuální procesory, lepší výkon dočasného úložiště nebo mají vyšší nároky na paměť. Nabízejí výkonnou kombinaci pro mnoho podnikových aplikací.

- [Řada Eav4 a Easv4](eav4-easv4-series.md) využívá procesor 2.35Ghz EPYC<sup>TM</sup> 7452 společnosti AMD v konfiguraci s více vlákny s mezipamětí L3 až 256 MB, což zvyšuje možnosti pro spuštění většiny úloh optimalizovaných pro paměť. Řady Eav4 a Easv4 mají stejnou konfiguraci paměti a disku jako řada Ev3 & Esv3.

- Procesor Intel® Xeon [řady Ev3 a Esv3®](ev3-esv3-series.md) 8171M 2.1 GHz (Skylake) nebo Procesor Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) v konfiguraci s hypervlákny, poskytuje lepší hodnotu pro většinu úloh pro obecné účely a přináší Ev3 do souladu s obecnými virtuálními počítači většiny ostatních cloudů. Paměť byla rozšířena (ze 7 GiB/vCPU na 8 GiB/vCPU), zatímco diskové a síťové limity byly upraveny na základě jádra tak, aby odpovídaly přechodu na hyper-threading. Ev3 je sledovat na velikosti virtuálních zařízení s vysokou pamětí rodiny D/Dv2.

- [Řada M](m-series.md) nabízí vysoký počet virtuálních procesorů (až 128 virtuálních procesorů) a velké množství paměti (až 3,8 TiB). Je také ideální pro extrémně velké databáze nebo jiné aplikace, které těží z vysokého počtu virtuálních procesorů a velkého množství paměti.

- [Řada Mv2](mv2-series.md) nabízí nejvyšší počet virtuálních procesorů (až 416 virtuálních procesorů) a největší paměť (až 8,19 TiB) libovolného virtuálního počítače v cloudu. Je ideální pro extrémně velké databáze nebo další aplikace, kterým přináší výhody vysoký počet virtuálních procesorů a velké množství paměti.

Azure Compute nabízí velikosti virtuálních počítačů, které jsou izolované pro konkrétní typ hardwaru a vyhrazené pro jednoho zákazníka. Tyto velikosti virtuálních počítačů jsou nejvhodnější pro úlohy, které vyžadují vysoký stupeň izolace od ostatních zákazníků pro úlohy zahrnující prvky, jako je dodržování předpisů a regulační požadavky. Zákazníci se taky můžou rozhodnout dále rozdělit prostředky těchto izolovaných virtuálních počítačů pomocí [podpory Azure pro vnořené virtuální počítače](https://azure.microsoft.com/blog/nested-virtualization-in-azure/). Podívejte se na stránky pro rodiny virtuálních strojů níže pro izolované možnosti virtuálních počítačů.

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám můžou pomoct porovnat výpočetní výkon napříč virtuálními jednotkami Azure.