---
title: Škálování aplikací HPC - Azure Virtual Machines | Dokumentace Microsoftu
description: Zjistěte, jak škálovat aplikace HPC na virtuálních počítačích Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: e2e2476449f956361639e42e7c398e53e42d44ab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66810109"
---
# <a name="scaling-hpc-applications"></a>Škálování aplikací HPC

Zajištění optimálního výkonu vertikálně navýšit kapacitu a škálování aplikací HPC v Azure vyžaduje optimalizace výkonu a experimenty optimalizace pro konkrétní úlohu. V této části a stránky pro konkrétní řady virtuálních počítačů nabízejí obecné pokyny pro škálování vašich aplikací.

## <a name="compiling-applications"></a>Sestavování aplikací

I když ne nezbytné, kompilace aplikace s příznaky optimalizace odpovídající obsahuje nejlepší výkon vertikálně navýšit kapacitu HB a virtuální počítače řady hybridní připojení.

### <a name="amd-optimizing-cc-compiler"></a>AMD optimalizace jazyka C /C++ kompilátoru

AMD optimalizace jazyka C /C++ systému kompilátoru kompilátor (AOCC) nabízí vysokou úroveň pokročilé optimalizace, více vláken a procesorem, který podporuje, která zahrnuje globální optimalizace, vektorizace, meziprocedurální analýz, smyčka transformací, a generování kódu. Kompilátor AOCC binární soubory jsou vhodné pro systémy Linux s knihovna GNU C (glibc) verze 2.17 a vyšší. Kompilátor suite se skládá z C /C++ kompilátor (clang), kompilátor až po Fortran (FLANG) a až po Fortran front-end pro Clang (Dragon Egg).

### <a name="clang"></a>Clang

Je clang C, C++a kompilátor jazyka Objective-C zpracovává předběžného zpracování, analýza, optimalizace, generování kódu, sestavení a propojení. Clang podporuje `-march=znver1` příznaku pro povolení nejlepší generování kódu a ladění pro AMD Zen založené na x86 architektury.

### <a name="flang"></a>FLANG

Kompilátor FLANG je nedávný dodatek k sadě AOCC (přidáno platný od dubna 2018) a aktuálně je v předběžné verzi pro vývojáře pro stažení a testování. Podle 2008 až po Fortran, AMD rozšiřuje Githubu verzi FLANG (https://github.com/flangcompiler/flang). Kompilátor FLANG podporuje všechny možnosti kompilátoru Clang a další číslo FLANG specifické možnosti kompilátoru.

### <a name="dragonegg"></a>DragonEgg

DragonEgg je modul plug-in gcc, který nahrazuje nástroje GCC společnosti a generátory kódu soubory z projektu LLVM. DragonEgg, který je součástí AOCC spolupracuje s gcc 4.8.x, je testovaná pro cíle x86 – 32/x86-x 64 a byl úspěšně použit u různých platforem Linuxu.

GFortran je skutečná front-endu pro programy až po Fortran zodpovědná za jejich předběžné zpracování, analýzu a sémantická analýza generování zprostředkující reprezentace GCC GIMPLE (IR). DragonEgg je modul plug-in GNU, zapojení do toku GFortran kompilace. Implementuje modul plug-in GNU rozhraní API. Architektura modulu plug-in DragonEgg stane ovladač kompilátoru řízení různých fází kompilace.  Po provedení kroků ke stažení a instalace, je možné vyvolat Dragon Egg pomocí: 

```bash
$ gfortran [gFortran flags] 
   -fplugin=/path/AOCC-1.2-Compiler/AOCC-1.2-     
   FortranPlugin/dragonegg.so [plugin optimization flags]     
   -c xyz.f90 $ clang -O3 -lgfortran -o xyz xyz.o $./xyz
```
   
### <a name="pgi-compiler"></a>PGI kompilátoru
PGI Community Edition verze pro práci s AMD EPYC je potvrzen 17. PGI kompilaci verze datového proudu poskytovat úplné paměti šířky pásma platformy. Stejně tak by měl novější 18.10 edice Community (listopadu 2018) pracovat dobře. Tady je ukázka rozhraní příkazového řádku pro kompilátor optimálně s kompilátorem Intel:

```bash
pgcc $(OPTIMIZATIONS_PGI) $(STACK) -DSTREAM_ARRAY_SIZE=800000000 stream.c -o stream.pgi
```

### <a name="intel-compiler"></a>Intel Compiler
Verze kompilátoru Intel pro práci s AMD EPYC je potvrzen 18. Tady je ukázka rozhraní příkazového řádku pro kompilátor optimálně s kompilátorem Intel.

```bash
icc -o stream.intel stream.c -DSTATIC -DSTREAM_ARRAY_SIZE=800000000 -mcmodel=large -shared-intel -Ofast –qopenmp
```

### <a name="gcc-compiler"></a>Kompilátor GCC 
Pro prostředí HPC AMD doporučuje GCC kompilátoru 7.3 nebo novější. Starší verze, jako je například 4.8.5 součástí RHEL a CentOS 7.4, se nedoporučuje. GCC 7.3 a novější, bude poskytovat výrazně vyšší výkon na HPL HPCG a DGEMM testy.

```bash
gcc $(OPTIMIZATIONS) $(OMP) $(STACK) $(STREAM_PARAMETERS) stream.c -o stream.gcc
```

## <a name="scaling-applications"></a>Škálování aplikací 

Pro optimální škálování efektivity, výkonu a konzistenci aplikace platí následující návrhy:

* PIN kód zpracovává jader 0 až 59 pomocí Připnutí sekvenční přístup (na rozdíl od pro automatické vyvážení přístup). 
* Vazba podle Numa/Core/HwThread je lepší než výchozí vazby.
* Pro hybridní paralelních aplikací MPI (OpenMP + Interface) použijte 1 MPI zařazení za CCX a 4 vlákna.
* Pro aplikace MPI pure můžete Experimentujte s 1 – 4 MPI řadí za CCX pro zajištění optimálního výkonu.
* Některé aplikace se citlivosti a šířky pásma paměti můžou mít užitek z pomocí nižší počet jader na CCX. Pro tyto aplikace pomocí 2 nebo 3 jader na CCX může omezit kolize paměti šířky pásma a vyšší výkon skutečné nebo konzistentnější škálovatelnost. Zejména MPI Allreduce mohou využít.
* Pro spuštění podstatně větší měřítko se doporučuje použít UD nebo hybridní RC + UD přenosy. Mnoho knihoven knihovny nebo modulu runtime MPI tomu interně (například UCX nebo MVAPICH2). Zkontrolujte konfiguraci přenosu pro spuštění ve velkém měřítku.

## <a name="next-steps"></a>Další postup

Další informace o [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) v Azure.
