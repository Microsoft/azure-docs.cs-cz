---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 04/27/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 801f0f03b49d20c84a4531bd0daad7630a0ed01d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585049"
---
## <a name="common-scenarios"></a>Obvyklé scénáře
Při navýšení zátěže můžou významně těžit z následujících scénářů:
- **Zlepšení časů spuštění**  – s využitím shlukování se vaše instance spustí výrazně rychleji. Například výchozí disk s operačním systémem pro virtuální počítače s podporou Premium je disk P4, což je zřízený výkon až 120 vstupně-výstupních operací a 25 MB/s. V případě shlukování může P4 přejít až na 3500 IOPS a 170 MB/s, což umožňuje čas spuštění zrychlit 6X.
- **Zpracování úloh služby Batch** – některé úlohy aplikace jsou cyklické v podstatě a vyžadují pro většinu času základní výkon a vyžadují pro krátkou dobu vyšší výkon. Příkladem je účetní program, který zpracovává transakce denně, které vyžadují malé množství provozu na disku. Pak na konci měsíce slučuje sestavy, které potřebují mnohem větší objem provozu na disku.
- **Připravenost na špičky provozu** – webové servery a jejich aplikace mohou kdykoli docházet k nárůstu provozu. Pokud je váš webový server zálohovaný virtuálními počítači nebo disky pomocí shlukování, servery jsou lépe vybavené pro zpracování špičky provozu. 

## <a name="bursting-flow"></a>Tok shlukování
Systém shlukování zatížení se vztahuje stejným způsobem na úrovni virtuálního počítače i na úrovni disku. Váš prostředek, buď virtuální počítač nebo disk, začne s úplnými zásobami kreditů. Tyto kredity vám umožní roztržení po dobu 30 minut s maximální sazbou shlukování. Kredity shlukování se sčítají, když je prostředek spuštěný pod limity úložiště na disku výkonu. Pro všechny vstupně-výstupní operace a MB/s, které váš prostředek používá, můžete začít nahromadění kreditů. Pokud se u vašeho prostředku narostly kredity, které se mají použít pro navýšení zátěže, a vaše zatížení potřebuje dodatečný výkon, může prostředek využít tyto kredity k tomu, aby dosáhl vyššího výkonu v/v, aby využíval vstupně-výstupní požadavky na disk.



![Diagram shlukování s intervaly](media/managed-disks-bursting/bucket-diagram.jpg)

Je zcela na vás, jak chcete používat 30 minut při roztržení. Můžete ji použít po dobu 30 minut po sobě jdoucí nebo občasně v průběhu dne. Když je produkt nasazený, je připravený s úplnými kredity a při vyčerpání kreditů, které trvá déle než jeden den, získáte plnou zásobu kreditů. Můžete nashromáždit a strávit své kredity na základě vašeho uvážení a 30 minutový interval nemusí být znovu nanejvýš na nárůst. Jedna z věcí, kterou si můžete všimnout při akumulaci, je to, že se u každého prostředku liší, protože vychází z nevyužitých vstupně-výstupních operací a MB/s pod jejich objemy výkonu. To znamená, že vyšší nároky na základní výkon můžou snížit množství jejich nárůstu rychleji než nižší směrné produkty. Například volnoběh disku P1 bez aktivity bude mít 120 vstupně-výstupních operací za sekundu, zatímco P20 disk během volnoběhu bez aktivity narůstá 2 300 vstupně-výstupních operací za sekundu.

## <a name="bursting-states"></a>Stavy shlukování
Existují tři stavy, ve kterých může být prostředek zapnutý s povoleným roztržením:
- **Nabíhají** – provoz v/v prostředku používá méně než cíl výkonu. Nahromadění kreditů při roztržení za IOPS a MB/s se od sebe liší. Vaším prostředkem může být časově rozlišené kredity IOPS a útraty v MB/s a naopak.
- **Shluking** – provoz prostředku používá více než cíl výkonu. Shlukový přenos nezávisle spotřebovává kredity z IOPS nebo šířky pásma.
- **Konstanta** – provoz prostředku je přesně v cíli výkonu.

## <a name="examples-of-bursting"></a>Příklady shlukování

Následující příklady znázorňují způsob, jakým funguje shlukování s různými kombinacemi virtuálních počítačů a disků. Aby bylo možné tyto příklady snadno sledovat, zaměřte se na MB/s, ale stejná logika se použije nezávisle na IOPS.

### <a name="non-burstable-virtual-machine-with-burstable-disks"></a>Virtuální počítač bez zátěže s použitím diskových disků s více shluky
**Kombinace virtuálních počítačů a disků:** 
- Standard_D8as_v4 
    - Neuložené MB/s v mezipaměti: 192
- Disk s operačním systémem P4
    - Zřízené MB/s: 25
    - Maximální počet shluků MB/s: 170 
- 2 datové disky P10 
    - Zřízené MB/s: 100
    - Maximální počet shluků MB/s: 170

 Když se virtuální počítač spustí, načte data z disku s operačním systémem. Vzhledem k tomu, že disk s operačním systémem je součástí virtuálního počítače, který se spouští, bude disk s operačním systémem plný kredity. Tyto kredity umožní, aby se při spuštění disku s operačním systémem za sekundu 170 MB/s.

![Virtuální počítač odešle požadavek na propustnost 192 MB/s na disk s operačním systémem a disk s operačním systémem odpoví 170 MB/s dat.](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-startup.jpg)

Po dokončení spuštění se aplikace na virtuálním počítači spustí a má nekritickou úlohu. Tato úloha vyžaduje 15 MB/S, která se rovnoměrně rozloží napříč všemi disky.

![Aplikace pošle požadavek na 15 MB/s propustnosti virtuálnímu počítači, virtuální počítač provede požadavek a pošle každému z nich požadavek na 5 MB/s. Každý disk vrátí 5 MB/s, virtuální počítač vrátí 15 MB/s do aplikace.](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-idling.jpg)

Aplikace pak potřebuje zpracovat dávkovou úlohu, která vyžaduje 192 MB/s. disk s operačním systémem používá 2 MB/s a zbývající jsou rovnoměrně rozdělená mezi datovými disky.

![Aplikace odesílá požadavek na virtuální počítač z propustnosti 192 MB/s a pošle žádost do datových disků (95 MB/s každého) a 2 MB na disk s operačním systémem, datové disky, které splňují požadavky, a všechny disky vrátí požadovanou propustnost do virtuálního počítače, který je vrátí do aplikace.](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-bursting.jpg)

### <a name="burstable-virtual-machine-with-non-burstable-disks"></a>Virtualizovaný virtuální počítač s disky, které nelze rozshlukovat
**Kombinace virtuálních počítačů a disků:** 
- Standard_L8s_v2 
    - Neuložené MB/s v mezipaměti: 160
    - Maximální počet shluků MB/s: 1 280
- Disk s operačním systémem P50
    - Zřízené MB/s: 250 
- 2 datové disky P10 
    - Zřízené MB/s: 250

 Po počátečním spuštění se aplikace na virtuálním počítači spustí a má nekritickou úlohu. Tato úloha vyžaduje 30 MB/s, která se rovnoměrně rozloží napříč všemi disky.
![Aplikace pošle požadavek na virtuální počítač o 30 MB/s a pošle požadavek na virtuální počítač a pošle každý z nich požadavek na 10 MB/s. Každý disk vrátí 10 MB/s, virtuální počítač vrátí 30 MB/s na aplikaci.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-normal.jpg)

Aplikace pak potřebuje zpracovat dávkovou úlohu, která vyžaduje 600 MB/s. Standard_L8s_v2 rozšíří, aby splňovala tuto poptávku, a požadavky na disky se rovnoměrně rozloží na P50 disky.

![Aplikace odesílá požadavek na virtuální počítač z propustnosti 600 MB/s, virtuální počítač bere v úvahu nárůst požadavků a každý z jeho disků pošle požadavek na 200 MB/s. Každý disk vrátí 200 MB/s, protože shluky virtuálních počítačů vrátí do aplikace 600 MB/s.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-bursting.jpg)
### <a name="burstable-virtual-machine-with-burstable-disks"></a>Virtuální počítač s více podsítěmi s roztržením disků
**Kombinace virtuálních počítačů a disků:** 
- Standard_L8s_v2 
    - Neuložené MB/s v mezipaměti: 160
    - Maximální počet shluků MB/s: 1 280
- Disk s operačním systémem P4
    - Zřízené MB/s: 25
    - Maximální počet shluků MB/s: 170 
- 2. datové disky P4 
    - Zřízené MB/s: 25
    - Maximální počet shluků MB/s: 170 

Když se virtuální počítač spustí, bude nárůst požadavků na jeho limit počtu 1 280 MB/s z disku s operačním systémem a disk s operačním systémem bude reagovat na výkon při jeho nárůstu na 170 MB/s.

![Při spuštění se virtuální počítač rozpíná za účelem odeslání žádosti o 1 280 MB/s na disk s operačním systémem, nárůsty disku s operačním systémem za účelem vrácení 1 280 MB/s.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-startup.jpg)

Po spuštění spustíte aplikaci, která má nekritickou úlohu. Tato aplikace vyžaduje 15 MB/s, která se rovnoměrně rozšíří napříč všemi disky.

![Aplikace pošle požadavek na 15 MB/s propustnosti virtuálnímu počítači, virtuální počítač provede požadavek a pošle každému z nich požadavek na 5 MB/s. Každý disk vrátí 5 MB/s, virtuální počítač vrátí 15 MB/s do aplikace.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

Aplikace pak potřebuje zpracovat dávkovou úlohu, která vyžaduje 360 MB/s. Standard_L8s_v2 se rozpíná, aby splňovala tuto poptávku, a pak požadavky. Disk s operačním systémem vyžaduje jenom 20 MB/s. Zbývajících 340 MB/s se zpracovává na základě shlukování datových disků P4.

![Aplikace odesílá požadavek na virtuální počítač z propustnosti 360 MB/s, virtuální počítač bere v úvahu nárůst požadavků a každý z nich odešle požadavek na 170 MB/s a 20 MB/s z disku s operačním systémem. Každý disk vrátí požadované MB/s, což počet nárůstů virtuálních počítačů vrátí do aplikace v počtu 360 MB/s.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)