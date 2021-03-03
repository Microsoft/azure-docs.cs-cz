---
title: Škálování aplikací HPC – Azure Virtual Machines | Microsoft Docs
description: Naučte se škálovat aplikace HPC na virtuálních počítačích Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: d560b261e058d01040616f3c59ede60e5986c672
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101666975"
---
# <a name="scaling-hpc-applications"></a>Škálování aplikací HPC

Optimální výkon a škálování aplikací HPC v Azure v rámci škálování vyžaduje vyladění výkonu a experimenty optimalizace pro konkrétní zatížení. Tato část a stránky specifické pro řady virtuálních počítačů nabízejí obecné pokyny pro škálování aplikací.

## <a name="compiling-applications"></a>Kompilování aplikací

I když není nutné, kompilování aplikací s příslušnými příznaky optimalizace poskytuje nejlepší výkon při škálování na virtuálních počítačích s více instancemi a pro řady HC.

### <a name="amd-optimizing-cc-compiler"></a>Kompilátor C/C++ optimalizuje procesory AMD

Systém pro kompilátor C/C++ s podporou technologie AMD (AOCC) nabízí vysokou úroveň pokročilých optimalizací, multithreading a podpora procesoru, která zahrnuje globální optimalizaci, rozstupnování, analýzu smyček a vytváření kódu. Binární soubory kompilátoru AOCC jsou vhodné pro systémy Linux s knihovnou GNU C (glibc) verze 2,17 a vyšší. Sada kompilátoru se skládá z kompilátoru C/C++ (Clang), kompilátoru FORTRAN (FLANG) a front-endu FORTRAN až po Clang (vejce drak).

### <a name="clang"></a>Clang

Clang je kompilátor jazyka C, C++ a objektiv-C, který zpracovává předzpracování, analýzu, optimalizaci, generování kódu, sestavení a propojení. Clang podporuje  `-march=znver1` příznak pro zajištění nejlepšího generování kódu a optimalizaci pro architekturu x86 založenou na platformě AMD Zen.

### <a name="flang"></a>FLANG

Kompilátor FLANG je nedávno přidaný do sady AOCC (přidáno v dubnu 2018) a je aktuálně v předběžném vydání pro vývojáře ke stažení a testování. V závislosti na programu Fortran 2008 rozšiřuje verze GitHubu verzi FLANG ( https://github.com/flang-compiler/flang) . Kompilátor FLANG podporuje všechny možnosti kompilátoru Clang a další počet možností kompilátoru specifických pro FLANG.

### <a name="dragonegg"></a>DragonEgg

DragonEgg je modul plug-in pro RSZ, který nahrazuje služby a generátory kódu v RSZ pomocí těch z projektu LLVM. DragonEgg, který se dodává s AOCC, pracuje s RSZ-4.8. x, byl testován pro cíle x86-32/X86-64 a úspěšně se použil na různých platformách Linux.

GFortran je skutečný front-end pro programy FORTRAN zodpovědné za předzpracování, analýzu a sémantickou analýzu generující mezilehlé reprezentace GIMPLE RSZ (IR). DragonEgg je modul plug-in GNU, který se zapojuje do toku kompilace GFortran. Implementuje rozhraní API modulu plug-in GNU. V rámci architektury modulu plug-in se DragonEgg stal ovladačem kompilátoru, který řídí různé fáze kompilace.  Po stažení a pokyny k instalaci je možné vyvolávat vaječných drak pomocí těchto kroků: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>CHZO – kompilátor
Verze CHZO Community Edition 17 se potvrdí, že pracuje s AMD EPYC. Pochzoná verze STREAMu zajišťuje celou šířku pásma platformy. Novější verze komunity 18,10 (listopadu 2018) by měla dobře fungovat i dobře. Níže je příklad rozhraní příkazového řádku pro optimální kompilátor s kompilátorem Intel:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Kompilátor Intel
Společnost Intel Compiler ver 18 pro práci s AMD EPYC se potvrdí. Níže je příklad rozhraní příkazového řádku pro optimální kompilátor s kompilátorem Intel.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>Kompilátor RSZ 
V případě HPC doporučuje AMD kompilátor RSZ 7,3 nebo novější. Starší verze, například 4.8.5, které jsou součástí RHEL/CentOS 7,4, se nedoporučují. RSZ 7,3 a novější přináší významně vyšší výkon na HPL, HPCG a DGEMM testy.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>Škálování aplikací 

Následující návrhy se vztahují na optimální efektivitu škálování aplikace, výkon a konzistenci:

* Připněte procesy k jádrům 0-59 pomocí přístupu sekvenčního připnutí (na rozdíl od přístupu k automatickým zůstatkům). 
* Vazba pomocí technologie NUMA/Core/HwThread je lepší než výchozí vazba.
* U hybridních paralelních aplikací (OpenMP + MPI) použijte 4 vlákna a 1 MPI pořadí na CCX.
* Pro zajištění optimálního výkonu pro čistě MPI aplikace Experimentujte s 1-4 MPI pořadím na CCX.
* Některé aplikace s extrémní citlivostí na šířku pásma můžou využívat omezený počet jader na CCX. Pro tyto aplikace může použití 3 nebo 2 jader na CCX snížit obsah šířky pásma paměti a dosáhnout vyššího reálného výkonu nebo jednotnější škálovatelnosti. MPI Allreduce může využít zejména tento.
* Pro významně větší běhy se doporučuje používat UD nebo Hybrid RC + UD Transports. K tomuto internímu účelu patří spousta knihoven MPI/knihoven za běhu (například UCX nebo MVAPICH2). Ověřte konfiguraci přenosu pro rozsáhlá spuštění.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [HPC](/azure/architecture/topics/high-performance-computing/) v Azure.
