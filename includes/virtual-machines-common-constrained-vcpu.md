---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 24fbab2bb637edf6c7e31215cdc7ac82bdb0ebab
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158486"
---
Některé databázové úlohy, jako je SQL Server nebo Oracle vyžadují vysoký poměr paměti, úložiště a šířku pásma vstupně-výstupních operací, ale ne počet vysokou jader. Mnoho úloh databáze nejsou náročné na CPU. Azure nabízí určité velikosti virtuálních počítačů, ve kterém lze omezit počet virtuálních procesorů virtuálního počítače chcete snížit náklady na licencování softwaru, a přitom stejnou paměť, úložiště a šířku pásma vstupně-výstupních operací.

Počet virtuálních procesorů může být omezena na polovinu nebo jednoho čtvrtletí původní velikost virtuálního počítače. Tyto nové velikosti virtuálních počítačů mají příponu, která určuje počet aktivních virtuálních procesorů pro snadnější identifikaci.

Například Standard_GS5 současnými velikostmi virtuálních počítačů se dodává s 32 virtuálních procesorů, 448 GB paměti RAM, 64 disků (až 256 TB) a 80 000 vstupně-výstupních operací nebo 2 GB/s šířky pásma vstupně-výstupních operací. Nový virtuální počítač o velikosti Standard_GS5 16 a Standard_GS5 8 se dodává s 8 a 16 virtuálních procesorů aktivní, při zachování rest specifikace Standard_GS5 paměti, úložiště a šířku pásma vstupně-výstupních operací.

Licenční poplatky za SQL Server nebo Oracle jsou omezeny na nový počet virtuálních procesorů a by měly být účtovány další produkty podle nový počet virtuálních procesorů. Výsledkem je 50 až 75 % zvýšení poměr specifikace virtuálních počítačů pro aktivní virtuálních procesorů (Fakturovatelné). Tyto nové velikosti virtuálních počítačů povolit úloh zákazníka používat stejnou paměť, úložiště a šířku pásma vstupně-výstupní operace a optimalizovat náklady na licencování softwaru. V tuto chvíli zůstane výpočetní náklady, která zahrnuje licencování operačního systému, je stejný jako původní velikost. Další informace najdete v tématu [velikosti virtuálních počítačů Azure pro další nákladově efektivní databázové úlohy](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).


| Název                | Virtuální procesory | Specifikace           |
|---------------------|------|-----------------|
| Standard_M8-2ms     | 2    | Stejné jako M8ms    |
| Standard_M8-4ms     | 4    | Stejné jako M8ms    |
| Standard_M16-4ms    | 4    | Stejné jako M16ms   |
| Standard_M16-8ms    | 8    | Stejné jako M16ms   |
| Standard_M32-8ms    | 8    | Stejné jako M32ms   |
| Standard_M32-16ms   | 16   | Stejné jako M32ms   |
| Standard_M64-32ms   | 32   | Stejné jako M64ms   |
| Standard_M64-16ms   | 16   | Stejné jako M64ms   |
| Standard_M128-64ms  | 64   | Stejné jako M128ms  |
| Standard_M128-32ms  | 32   | Stejné jako M128ms  |
| Standard_E4-2s_v3   | 2    | Stejné jako E4s_v3  |
| Standard_E8-4s_v3   | 4    | Stejné jako E8s_v3  |
| Standard_E8-2s_v3   | 2    | Stejné jako E8s_v3  |
| Standard_E16-8s_v3  | 8    | Stejné jako E16s_v3 |
| Standard_E16-4s_v3  | 4    | Stejné jako E16s_v3 |
| Standard_E32-16s_v3 | 16   | Stejné jako E32s_v3 |
| Standard_E32-8s_v3  | 8    | Stejné jako E32s_v3 |
| Standard_E64-32s_v3 | 32   | Stejné jako E64s_v3 |
| Standard_E64-16s_v3 | 16   | Stejné jako E64s_v3 |
| Standard_GS4-8      | 8    | Stejné jako GS4     |
| Standard_GS4-4      | 4    | Stejné jako GS4     |
| Standard_GS5-16     | 16   | Stejné jako GS5     |
| Standard_GS5-8      | 8    | Stejné jako GS5     |
| Standard_DS11-1_v2  | 1    | Stejné jako DS11_v2 |
| Standard_DS12-2_v2  | 2    | Stejné jako DS12_v2 |
| Standard_DS12-1_v2  | 1    | Stejné jako DS12_v2 |
| Standard_DS13-4_v2  | 4    | Stejné jako DS13_v2 |
| Standard_DS13-2_v2  | 2    | Stejné jako DS13_v2 |
| Standard_DS14-8_v2  | 8    | Stejné jako DS14_v2 |
| Standard_DS14-4_v2  | 4    | Stejné jako DS14_v2 |
