---
title: Velikosti virtuálních počítačů Azure – pro obecné účely | Microsoft Docs
description: Uvádí různé velikosti pro obecné účely, které jsou k dispozici pro virtuální počítače v Azure. Uvádí informace o počtu vCPU, datových discích a síťových adaptérů a propustnosti úložiště a šířce pásma sítě pro velikosti v této sérii.
author: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.devlang: na
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: mimckitt
ms.openlocfilehash: 46a9b7e630001161fbf2790cfb9be474379864b6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557620"
---
# <a name="general-purpose-virtual-machine-sizes"></a>Velikosti virtuálních počítačů pro obecné účely

Velikosti virtuálních počítačů pro obecné účely poskytují vyvážený poměr PROCESORů k paměti. Tato možnost je ideální pro testování a vývoj, malé až střední databáze a webové servery s nízkým až středním provozem. Tento článek poskytuje informace o nabídkách pro obecné účely výpočtu.

- Virtuální počítače [řady Av2-Series](av2-series.md) je možné nasadit na nejrůznější typy hardwaru a procesory. Virtuální počítače řady a-Series mají pro úlohy na vstupní úrovni, jako je vývoj a testování, nejvhodnější konfigurace výkonu a paměti procesoru. Velikost je omezována v závislosti na použitém hardwaru a nabízí konzistentní výkon procesoru pro spuštěné instance bez ohledu na hardware. Pokud chcete zjistit fyzický hardware, na kterém je virtuální počítač dané velikosti nasazený, zadejte dotaz na virtuální hardware přímo z virtuálního počítače. Příklady případů použití zahrnují vývojové a testovací servery, webové servery s nízkým provozem, malé až střední databáze, testování konceptů a úložiště kódu.

  > [!NOTE]
  > Virtuální počítače A8, A11 A10 jsou plánovány k vyřazení na 3/2021. Další informace najdete v tématu [Průvodce migrací HPC](https://azure.microsoft.com/resources/hpc-migration-guide/). Tyto velikosti virtuálních počítačů jsou v původní sérii "A_v1", ne "v2".

- [Řady B-Series pro shluky](sizes-b-series-burstable.md) Virtuální počítače jsou ideální pro úlohy, které nepotřebují průběžný výkon procesoru, jako jsou webové servery, malé databáze a vývojové a testovací prostředí. Tyto úlohy mají typicky požadavky na výkon při roztržení. Řada B-Series poskytuje zákazníkům možnost zakoupit si velikost virtuálního počítače pomocí cenového plánu s vědomím cen, který umožňuje instanci virtuálního počítače sestavovat kredity, když virtuální počítač využívá méně než základní výkon. Když se na virtuálním počítači nashromáždil kredit, může virtuální počítač zvýšit kapacitu virtuálního počítače pomocí až 100% procesoru, když vaše aplikace vyžaduje vyšší výkon procesoru.

- [Dav4 a Dasv4-Series](dav4-dasv4-series.md) jsou novými velikostmi, které využívají procesor AMD 2.35 EPYC<sup>TM</sup> 7452 v konfiguraci 256 s více vlákny, která VYnásobí 8 MB mezipaměti L3 do každých 8 jader, které zvyšují možnosti zákazníků při spouštění jejich úloh pro obecné účely. Řady Dav4-Series a Dasv4-Series mají stejnou konfiguraci paměti a disku jako řada D & Dsv3-Series.

- [Dv4 a Dsv4 – Series](dv4-dsv4-series.md) Dv4 a Dsv4-Series běží na procesorech Intel® Xeon® Platinum 8272CL (Cascade Lake) v konfiguraci s vlákny typu Hyper a poskytují lepší polohu pro většinu úloh pro obecné účely. Nabízí veškerou základní rychlost Turbo 3,4 GHz.

- [Ddv4 a Ddsv4 – Series](ddv4-ddsv4-series.md) Ddv4 a Ddsv4-Series běží na &reg; procesorech Intel Xeon &reg; Platinum 8272CL (Cascade Lake) v konfiguraci s vlákny typu Hyper a poskytují lepší polohu pro většinu úloh pro obecné účely. Funguje na všech základních rychlostech Turbo 3,4 GHz, [Intel &reg; Turbo zvyšovat technologii 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [ &reg; technologii Intel Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) a [Intel &reg; Advanced Vector Extensions 512 (Intel &reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). Podporují taky [ &reg; zvýšení úrovně Intel s hloubkovým učením](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Tyto nové velikosti virtuálních počítačů budou mít 50% větší místní úložiště a také lepší počet vstupně-výstupních operací pro čtení i zápis ve srovnání s [Dv3/Dsv3mi](./dv3-dsv3-series.md) [virtuálními počítači Gen2](./generation-2.md).

- [Dv3 a Dsv3 – Series](dv3-dsv3-series.md) Virtuální počítače běží na 2. generaci Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 V4 2,3 GHz (Broadwell), nebo Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell) v konfiguraci s více vlákny, které poskytují lepší polohu pro většinu úloh pro obecné účely. Paměť se rozšířila (od ~ 3,5 GiB/vCPU na 4 GiB/vCPU), zatímco limity disku a sítě se přizpůsobily na základě počtu jader, aby se přerovnaly k přechodu na vlákno. Řady Dv3-Series již nemají virtuální počítače s vysokou velikostí paměti řady D/Dv2-Series byly přesunuty do paměti optimalizované pro [Ev3 a Esv3-Series](ev3-esv3-series.md).

- [Dv2 a Dsv2 – Series](dv2-dsv2-series.md) Virtuální počítače, následná na originální řady D, nabízí výkonnější procesor a optimální konfiguraci procesoru na paměť, která je vhodná pro většinu produkčních úloh. Dv2-Series má přibližně 35% rychlejší než řada D-Series. Dv2-Series se spouští na 2. generaci Intel® Xeon® Platinum 8272CL (Cascade Lake), Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 V4 2,3 GHz (Broadwell), nebo Intel® Xeon® E5-2673 V3 2,4 GHz (Haswell) s technologií Intel Turbo Rozbušk 2,0. Řada Dv2-series má stejnou konfiguraci paměti a disku jako řada D.

- [DCv2-Series](dcv2-series.md) může přispět k ochraně důvěrnosti a integrity vašich dat a kódu při jejich zpracování ve veřejném cloudu. Tyto počítače jsou založené na nejnovější generaci procesorů Intel XEON E-2288G s využitím technologie SGX. Technologie Intel Turbo zvyšuje tyto počítače až do verze 5.0 GHz. Instance řad DCv2 umožňují zákazníkům vytvářet zabezpečené aplikace založené na enklávy pro ochranu kódu a dat, když se používají.

## <a name="other-sizes"></a>Jiné velikosti

- [Optimalizované pro výpočty](sizes-compute.md)
- [Optimalizované pro paměť](sizes-memory.md)
- [Optimalizované pro úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU.](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.

Další informace o tom, jak Azure vyjmenovává své virtuální počítače, najdete v tématu zásady [vytváření názvů virtuálních počítačů Azure](./vm-naming-conventions.md).
