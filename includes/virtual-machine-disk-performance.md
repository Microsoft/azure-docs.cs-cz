---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 4770ac0181c64ef800aa02ba87284c8add357e36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518046"
---
Tento článek vám pomůže objasnit výkon disku a jeho fungování při kombinaci Virtual Machines Azure a disků Azure. Také popisuje, jak můžete diagnostikovat problém pro vstupně-výstupní operace disku a změny, které můžete provést pro optimalizaci výkonu.

## <a name="how-does-disk-performance-work"></a>Jak funguje výkon disku?
Virtuální počítače Azure mají vstupně-výstupní operace za sekundu (IOPS) a omezení výkonu propustnosti na základě typu a velikosti virtuálního počítače. Disky s operačním systémem a datové disky je možné připojit k virtuálním počítačům. Disky mají vlastní vstupně-výstupní operace a omezení propustnosti.

Výkon vaší aplikace je omezené, když požádá o více IOPS nebo propustnost, než jaké jsou přiděleny virtuálním počítačům nebo připojeným diskům. Při omezené aplikace, která má optimální výkon. To může vést k negativním důsledkům, jako je vyšší latence. Pojďme se na tento koncept vyjasnit několika příklady. Aby bylo možné tyto příklady snadno sledovat, Prohlédněte si pouze IOPS. Ale stejná logika platí i pro propustnost.

## <a name="disk-io-capping"></a>Disk v/v capping

**Obecného**

- Standard_D8s_v3
  - Neuložené IOPS s mezipamětí: 12 800
- Disk s operačním systémem E30
  - IOPS: 500
- Dva datové disky E30 × 2
  - IOPS: 500

![Diagram znázorňující capping na úrovni disku](media/vm-disk-performance/disk-level-throttling.jpg)

Aplikace spuštěná na virtuálním počítači vytvoří požadavek, který vyžaduje 10 000 vstupně-výstupních operací pro virtuální počítač. Všechny, které jsou povolené virtuálním počítačem, protože virtuální počítač Standard_D8s_v3 může spustit až 12 800 IOPS.

Požadavky 10 000 IOPS se v různých discích dělí na tři různé požadavky:

- disk s operačním systémem vyžaduje 1 000 IOPS.
- pro každý datový disk jsou požadovány 4 500 IOPS.

Všechny připojené disky jsou E30 disky a můžou zpracovat jenom 500 IOPS. To znamená, že každý z nich odpoví 500 IOPS. Výkon aplikace je omezené připojenými disky a může zpracovat pouze 1 500 IOPS. Pokud se používají lepší disky, například SSD úrovně Premium disky P30, může aplikace pracovat na špičkovém výkonu při 10 000 IOPS.

## <a name="virtual-machine-io-capping"></a>Vstupně-výstupní operace virtuálního počítače capping

**Obecného**

- Standard_D8s_v3
  - Neuložené IOPS s mezipamětí: 12 800
- Disk s operačním systémem P30
  - IOPS: 5 000
- Dva datové disky P30 × 2
  - IOPS: 5 000

![Diagram znázorňující capping na úrovni virtuálního počítače](media/vm-disk-performance/vm-level-throttling.jpg)

Aplikace spuštěná na virtuálním počítači vytvoří požadavek, který vyžaduje 15 000 vstupně-výstupních operací. Standard_D8s_v3 virtuální počítač se bohužel zřídí jenom pro zpracování 12 800 IOPS. Aplikace je omezené pomocí omezení virtuálních počítačů a musí přidělit přidělený 12 800 IOPS.

Požadované 12 800 IOPS jsou rozdělené na tři různé požadavky na různé disky:

- disk s operačním systémem vyžaduje 4 267 IOPS.
- pro každý datový disk jsou požadovány 4 266 IOPS.

Všechny připojené disky jsou P30 disky, které mohou zpracovávat 5 000 IOPS. Proto reagují na své požadované částky.
