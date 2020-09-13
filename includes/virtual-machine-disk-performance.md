---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 07/07/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 66380e0bab0a83d086ebebb5e595908cecd11643
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89663874"
---
Tento článek vám pomůže objasnit výkon disku a jeho fungování při kombinaci Virtual Machines Azure a disků Azure. Také popisuje, jak můžete diagnostikovat problém pro vstupně-výstupní operace disku a změny, které můžete provést pro optimalizaci výkonu.

## <a name="how-does-disk-performance-work"></a>Jak funguje výkon disku?
Virtuální počítače Azure mají omezení počtu vstupně-výstupních operací za sekundu a propustnost na základě typu a velikosti virtuálního počítače. Disky s operačním systémem a datové disky, které se dají připojit k virtuálním počítačům, mají vlastní meze IOPs a propustnosti. Když vaše aplikace spuštěná na virtuálních počítačích požaduje více IOPS nebo propustnosti, než je přidělená virtuálnímu počítači nebo připojeným diskům, výkon vaší aplikace bude omezené. Pokud k tomu dojde, bude aplikace vycházet z optimálního výkonu a může vést k nějakým negativním důsledkům, jako je vyšší latence. Pojďme se na to přesvědčit několika příklady. Aby bylo možné tyto příklady snadno sledovat, Prohlédněte si pouze IOPs, ale stejná logika platí také pro propustnost.

## <a name="disk-io-capping"></a>Disk v/v capping
Nastavení:
- Standard_D8s_v3 
    - Neuložené IOPS s mezipamětí: 12 800
- Disk s operačním systémem E30
    - IOPS: 500 
- 2 datové disky E30
    - IOPS: 500

![Capping na úrovni disku](media/vm-disk-performance/disk-level-throttling.jpg)

Aplikace spuštěná na virtuálním počítači vytvoří požadavek, který vyžaduje 10 000 vstupně-výstupních operací pro virtuální počítač. Všechny, které jsou povolené virtuálním počítačem, protože virtuální počítač Standard_D8s_v3 může spustit až 12 800 IOPs. Tyto požadavky 10 000 IOPs se pak rozdělí na tři různé požadavky na různé disky. pro disk s operačním systémem se vyžadují 1 000 IOPs a na každý datový disk se vyžadují 4 500 IOPs. Vzhledem k tomu, že jsou všechny připojené disky E30 disky a můžou zpracovávat jenom 500 IOPs, reagují na každý z 500 IOPs. Výkon aplikace se pak omezené připojenými disky a může zpracovat jenom 1 500 vstupně-výstupních operací. Pokud se používaly lepší disky, jako SSD úrovně Premium disky P30, může to v nejvyšší době 10 000 IOPS docházet k provozu.

## <a name="virtual-machine-io-capping"></a>Vstupně-výstupní operace virtuálního počítače capping
Nastavení:
- Standard_D8s_v3 
    - Neuložené IOPS s mezipamětí: 12 800
- Disk s operačním systémem P30
    - IOPS: 5 000 
- 2 datové disky P30 
    - IOPS: 5 000

![Capping na úrovni virtuálního počítače](media/vm-disk-performance/vm-level-throttling.jpg)

Aplikace spuštěná na virtuálním počítači vytvoří požadavek, který vyžaduje 15 000 vstupně-výstupních operací. Standard_D8s_v3 virtuální počítač se bohužel zřídí jenom pro zpracování 12 800 IOPs. Z toho se aplikace omezené pomocí omezení virtuálních počítačů a pak musí přidělit přidělený 12 800 vstupně-výstupních operací. Požadované 12 800 IOPs se pak rozdělí na tři různé požadavky na různé disky. pro disk s operačním systémem se vyžadují 4 267 IOPs a na každý datový disk se vyžadují 4 266 IOPs. Vzhledem k tomu, že jsou všechny připojené disky P30 disky, které mohou zpracovávat 5 000 IOPs, reagují na ně požadované částky.