---
title: Škálování aplikací HPC – Azure Virtual Machines | Microsoft Docs
description: Naučte se škálovat aplikace HPC na virtuálních počítačích Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 65a06a60b502b0e189ebe8a5e203553494f5d128
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721295"
---
# <a name="scaling-hpc-applications"></a>Škálování aplikací HPC

Optimální výkon a škálování aplikací HPC v Azure v rámci škálování vyžaduje vyladění výkonu a experimenty optimalizace pro konkrétní zatížení. Tato část a stránky specifické pro řady virtuálních počítačů nabízejí obecné pokyny pro škálování aplikací.

## <a name="application-setup"></a>Nastavení aplikace
[Úložiště azurehpc](https://github.com/Azure/azurehpc) obsahuje mnoho příkladů:
- Nastavování a spouštění [aplikací](https://github.com/Azure/azurehpc/tree/master/apps) optimálně.
- Konfigurace [systémů souborů a clusterů](https://github.com/Azure/azurehpc/tree/master/examples).
- [Kurzy](https://github.com/Azure/azurehpc/tree/master/tutorials) , jak snadno začít s některými běžnými pracovními postupy aplikací.

## <a name="optimally-scaling-mpi"></a>Optimální škálování MPI 

Následující návrhy se vztahují na optimální efektivitu škálování aplikace, výkon a konzistenci:

- Pro menší úlohy škálování (tj. < připojení 256) použijte možnost:
   ```bash
   UCX_TLS=rc,sm
   ```

- Pro úlohy větší škály (tj. > připojení 256) použijte možnost:
   ```bash
   UCX_TLS=dc,sm
   ```

- Pokud chcete vypočítat počet připojení pro úlohu MPI, použijte:
   ```bash
   Max Connections = (processes per node) x (number of nodes per job) x (number of nodes per job) 
   ```

## <a name="process-pinning"></a>Připnutí procesů

- Připnutí procesů k jádrům pomocí přístupu sekvenčního připnutí (na rozdíl od přístupu k automatickému vyrovnávání). 
- Vazba pomocí technologie NUMA/Core/HwThread je lepší než výchozí vazba.
- U hybridních paralelních aplikací (OpenMP + MPI) použijte 4 vlákna a 1 MPI pořadí na CCX na velikosti virtuálních počítačů od \ a HBv2.
- U čistě MPIch aplikací Experimentujte s 1-4 MPI hodným na CCX pro zajištění optimálního výkonu pro velikosti virtuálních počítačů s prostým a HBv2.
- Některé aplikace s extrémní citlivostí na šířku pásma můžou využívat omezený počet jader na CCX. Pro tyto aplikace může použití 3 nebo 2 jader na CCX snížit obsah šířky pásma paměti a dosáhnout vyššího reálného výkonu nebo jednotnější škálovatelnosti. Tento přístup může využít zejména MPI Allreduce.
- Pro významně větší běhy se doporučuje používat UD nebo Hybrid RC + UD Transports. K tomuto internímu účelu patří spousta knihoven MPI/knihoven za běhu (například UCX nebo MVAPICH2). Ověřte konfiguraci přenosu pro rozsáhlá spuštění.

## <a name="compiling-applications"></a>Kompilování aplikací
<br>
<details>
<summary>Kliknutím rozbalíte</summary>

I když není nutné, kompilování aplikací s příslušnými příznaky optimalizace poskytuje nejlepší výkon při škálování na virtuálních počítačích s více instancemi a pro řady HC.

### <a name="amd-optimizing-cc-compiler"></a>Kompilátor C/C++ optimalizuje procesory AMD

Systém pro kompilátor C/C++ s podporou technologie AMD (AOCC) nabízí vysokou úroveň pokročilých optimalizací, multithreading a podpora procesoru, která zahrnuje globální optimalizaci, rozstupnování, analýzu smyček a vytváření kódu. Binární soubory kompilátoru AOCC jsou vhodné pro systémy Linux s knihovnou GNU C (glibc) verze 2,17 a vyšší. Sada kompilátoru se skládá z kompilátoru jazyka C/C++ (Clang), kompilátoru programu Fortran (FLANG) a front-endu FORTRAN až po Clang (vaječných drak).

### <a name="clang"></a>Clang

Clang je kompilátor jazyka C, C++ a objektiv-C, který zpracovává předzpracování, analýzu, optimalizaci, generování kódu, sestavení a propojení. Clang podporuje  `-march=znver1` příznak pro zajištění nejlepšího generování kódu a optimalizaci pro architekturu x86 založenou na platformě AMD Zen.

### <a name="flang"></a>FLANG

Kompilátor FLANG je nedávno přidaný do sady AOCC (přidáno v dubnu 2018) a je aktuálně v předběžném vydání pro vývojáře ke stažení a testování. V závislosti na programu Fortran 2008 rozšiřuje verze GitHubu verzi FLANG ( https://github.com/flang-compiler/flang) . Kompilátor FLANG podporuje všechny možnosti kompilátoru Clang a další počet možností kompilátoru specifických pro FLANG.

### <a name="dragonegg"></a>DragonEgg

DragonEgg je modul plug-in pro RSZ, který nahrazuje služby a generátory kódu v RSZ pomocí těch z projektu LLVM. DragonEgg, který se dodává s AOCC, pracuje s RSZ-4.8. x, byl testován pro cíle x86-32/X86-64 a byl úspěšně použit na různých platformách systému Linux.

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
</details>

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [HPC](/azure/architecture/topics/high-performance-computing/) v Azure.
