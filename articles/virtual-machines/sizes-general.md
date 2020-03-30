---
title: Velikosti virtuálních počítačů Azure – obecné účely | Dokumenty společnosti Microsoft
description: Uvádí různé velikosti pro obecné účely, které jsou k dispozici pro virtuální počítače v Azure. Uvádí informace o počtu virtuálních procesorů, datových disků a síťových síťových připojení, jakož i propustnost úložiště a šířku pásma sítě pro velikosti v této řadě.
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/20/2020
ms.author: jonbeck
ms.openlocfilehash: 4b36f456f70eb79cff1f615c7c136b8fe4b1b3d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78226705"
---
# <a name="general-purpose-virtual-machine-sizes"></a>Univerzální velikosti virtuálních strojů

Univerzální velikosti virtuálních zařízení poskytují vyvážený poměr procesoru k paměti. Tato možnost je ideální pro testování a vývoj, malé až střední databáze a webové servery s nízkým až středním provozem. Tento článek obsahuje informace o nabídkách pro práci s běžným imforem.

- Virtuální počítače [řady Av2](av2-series.md) lze nasadit na různých typech hardwaru a procesorů. Virtuální počítače řady A mají konfigurace výkonu procesoru a paměti, které jsou nejvhodnější pro úlohy vstupní úrovně, jako je vývoj a testování. Velikost je omezována v závislosti na použitém hardwaru a nabízí konzistentní výkon procesoru pro spuštěné instance bez ohledu na hardware. Pokud chcete zjistit fyzický hardware, na kterém je virtuální počítač dané velikosti nasazený, zadejte dotaz na virtuální hardware přímo z virtuálního počítače. Příklady případů použití zahrnují vývojové a testovací servery, webové servery s nízkým provozem, malé až střední databáze, proof-of-concepts a úložiště kódu.

  > [!NOTE]
  > Virtuální počítače A8 – A11 jsou plánovány na vyřazení do důchodu na 3/2021. Další informace naleznete v [průvodci migrací HPC](https://azure.microsoft.com/resources/hpc-migration-guide/).

- [Roztržení řady B](sizes-b-series-burstable.md) Virtuální počítače jsou ideální pro úlohy, které nepotřebují plný výkon procesoru nepřetržitě, jako jsou webové servery, malé databáze a vývojová a testovací prostředí. Tyto úlohy mají obvykle sknutelné požadavky na výkon. Řada B poskytuje těmto zákazníkům možnost zakoupit velikost virtuálního počítače s cenovým výkonem, který umožňuje instanci virtuálního počítače vytvářet kredity, když virtuální počítače využívají méně než jeho základní výkon. Když virtuální počítač nahromaděný kredit, virtuální počítač může prasknout nad směrný plán virtuálního počítače pomocí až 100 % procesoru, když vaše aplikace vyžaduje vyšší výkon procesoru.

- [Dav4 a Dasv4-series](dav4-dasv4-series.md) jsou nové velikosti využívající procesor 2.35Ghz EPYC<sup>TM</sup> 7452 společnosti AMD v konfiguraci s více vlákny s mezipamětí L3 o velikosti až 256 MB, která věnuje 8 GB této mezipaměti L3 každému 8 jádrům, což zvyšuje možnosti zákazníků pro spuštění jejich úloh pro obecné účely. Řady Dav4 a Dasv4 mají stejnou konfiguraci paměti a disku jako řada D & Dsv3.

- [Řada DCv2](dcv2-series.md) může pomoci chránit důvěrnost a integritu vašich dat a kódu při jejich zpracování ve veřejném cloudu. Tyto stroje jsou podpořeny nejnovější generací procesoru Intel XEON E-2288G s technologií SGX. S technologií Intel Turbo Boost mohou tyto stroje sjet až na 5,0 GHz. Instance řady DCv2 umožňují zákazníkům vytvářet zabezpečené aplikace založené na enklávě, které chrání jejich kód a data, když jsou používány.

- [Řada Dv2 a Dsv2](dv2-dsv2-series.md) Virtuální počítače, které následují po původní řadě D, jsou vybaveny výkonnějším procesorem a optimální konfigurací cpu do paměti, díky čemuž jsou vhodné pro většinu produkčních úloh. Řada Dv2 je o 35 % rychlejší než řada D. Dv2-série běží na Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), nebo Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) procesory s Intel Turbo Boost Technology 2.0. Řada Dv2-series má stejnou konfiguraci paměti a disku jako řada D.

- [Řada Dv3 a Dsv3](dv3-dsv3-series.md) Virtuální počítače běží na Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell), nebo Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) procesory v hyper-threaded konfiguraci, poskytuje lepší hodnotu pro většinu pracovních vytížepro všechny účely pracovního vytížení. Paměť byla rozšířena (z ~ 3.5 GiB / vCPU na 4 GiB / vCPU), zatímco diskové a síťové limity byly upraveny na základě jádra tak, aby byly v souladu s přechodem na hyperthreading. Řada Dv3 již nemá velikosti virtuálních zařízení s vysokou pamětí řady D/Dv2, ty byly přesunuty do paměti optimalizované [řady Ev3 a Esv3](ev3-esv3-series.md).

Příklad případy použití řady D zahrnují podnikové aplikace, relační databáze, ukládání do mezipaměti v paměti a analýzy.

## <a name="other-sizes"></a>Jiné velikosti

- [Optimalizované z hlediska výpočetních služeb](sizes-compute.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám můžou pomoct porovnat výpočetní výkon napříč virtuálními jednotkami Azure.