---
title: Škálování aplikací HPC – virtuální počítače Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak škálovat aplikace HPC na virtuálních počítačích Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 00d5b86c8cae01d342d55b7ad20ec59c3f7530bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707840"
---
# <a name="scaling-hpc-applications"></a>Škálování aplikací HPC

Optimální vertikálně navýšit a škálovat výkon hpc aplikací v Azure vyžaduje experimenty optimalizace výkonu a optimalizace pro konkrétní úlohy. Tato část a stránky specifické pro řadu virtuálních počítače nabízejí obecné pokyny pro škálování aplikací.

## <a name="compiling-applications"></a>Kompilace aplikací

I když to není nutné, kompilace aplikací s příslušnými příznaky optimalizace poskytuje nejlepší vertikálně navýšit výkon na virtuálních počítačích řady HB a HC.

### <a name="amd-optimizing-cc-compiler"></a>Optimalizace kompilátoru C/C++ amd

Kompilátor amd optimalizace C/C++ (AOCC) kompilátor nabízí vysokou úroveň pokročilých optimalizací, multi-threadinga a podpory procesoru, která zahrnuje globální optimalizaci, vektorizaci, meziprocedurální analýzy, transformace smyček a generování kódu. Binární soubory kompilátoru AOCC jsou vhodné pro linuxové systémy s GNU C Library (glibc) verze 2.17 a vyšší. Kompilátor suite se skládá z C/C++ kompilátor (clang), kompilátor Fortran (FLANG) a Fortran front-end na Clang (Dragon Egg).

### <a name="clang"></a>Clang

Clang je kompilátor C, C++ a Objective-C, který zpracovává předběžné zpracování, analýzu, optimalizaci, generování kódu, sestavení a propojení. Clang podporuje `-march=znver1` příznak, který umožňuje nejlepší generování kódu a ladění pro architekturu x86 založenou na Zenové amd.

### <a name="flang"></a>Příděný

Kompilátor FLANG je nedávným přírůstkem do sady AOCC (přidáno v dubnu 2018) a je v současné době v předběžné verzi pro vývojáře ke stažení a testování. Na základě fortranu 2008 rozšiřuje AMD verzi FLANG ( GitHub (https://github.com/flangcompiler/flang). Kompilátor FLANG podporuje všechny možnosti kompilátoru Clang a další počet možností kompilátoru specifických pro FLANG.

### <a name="dragonegg"></a>DragonEgg

DragonEgg je gcc plugin, který nahrazuje GCC optimalizátory a generátory kódu s těmi z projektu LLVM. DragonEgg, který je dodáván s AOCC pracuje s gcc-4.8.x, byl testován na x86-32/x86-64 cíle a byl úspěšně použit na různých platformách Linux.

GFortran je skutečný frontend pro programy Fortran zodpovědný za předběžné zpracování, analýzu a sémantickou analýzu generující mezilehlé reprezentace GCC GIMPLE (IR). DragonEgg je GNU plugin, který se zapojuje do toku kompilace GFortran. Implementuje GNU plugin API. S architekturou pluginů se DragonEgg stává ovladačem kompilátoru, který řídí různé fáze kompilace.  Po provedení pokynů ke stažení a instalace lze Dragon Egg vyvolat pomocí: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>Kompilátor CHZO
CHZO Společenství Edition ver. 17 je potvrzeno pro práci s AMD EPYC. ChZP-kompilovaná verze STREAM poskytuje plnou paměť ovou šířku pásma platformy. Podobně by mělo dobře fungovat i novější vydání Community Edition 18.10 (listopad 2018). Níže je ukázka CLI pro kompilátor optimálně s kompilátorem Intel:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Kompilátor Intel
Intel Kompilátor ver. 18 je potvrzeno pro práci s AMD EPYC. Níže je ukázka CLI pro kompilátor optimálně s kompilátorem Intel.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>Kompilátor GCC 
Pro HPC AMD doporučuje GCC kompilátor 7.3 nebo novější. Starší verze, například 4.8.5, které jsou součástí RHEL/CentOS 7.4, se nedoporučují. GCC 7.3, a novější, přinese výrazně vyšší výkon na HPL, HPCG a DGEMM testy.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>Škálování aplikací 

Následující návrhy platí pro optimální efektivitu škálování aplikací, výkon a konzistenci:

* Připnutí procesů do jader 0-59 pomocí sekvenčního připnutí (na rozdíl od přístupu automatického vyvážení). 
* Vazba numa/core/hwthread je lepší než výchozí vazba.
* Pro hybridní paralelní aplikace (OpenMP+MPI) použijte 4 vlákna a 1 mpi pořadí na CCX.
* Pro čistě mpi aplikace experimentujte s 1-4 pozicemi MPI na CCX pro optimální výkon.
* Některé aplikace s extrémní citlivostí na šířku pásma paměti mohou mít prospěch z použití sníženého počtu jader na CCX. U těchto aplikací může použití 3 nebo 2 jader na CCX snížit konflikty šířky pásma paměti a zvýšit výkon v reálném světě nebo konzistentnější škálovatelnost. Z toho může mít prospěch zejména společnost MPI Allreduce.
* Pro výrazně větší škálování se doporučuje použít přenosy UD nebo hybridní RC+UD. Mnoho knihoven MPI/runtime knihoven to interně (například UCX nebo MVAPICH2). Zkontrolujte konfigurace přenosu pro rozsáhlé spuštění.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [HPC v](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) Azure.
