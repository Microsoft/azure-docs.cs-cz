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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175002"
---
Některé databázové úlohy, jako je SQL Server nebo Oracle vyžadují vysokou paměť, úložiště a vstupně-v., ale ne vysoký počet jádra. Mnoho databázových úloh není náročné na procesor. Azure nabízí určité velikosti virtuálních počítače, kde můžete omezit počet virtuálních procesorů virtuálních procesorů, abyste snížili náklady na licencování softwaru při zachování stejné paměti, úložiště a šířky pásma vstupně-va.

Počet virtuálních procesorů může být omezen na jednu polovinu nebo jednu čtvrtinu původní velikosti virtuálního počítače. Tyto nové velikosti virtuálních počítače mají příponu, která určuje počet aktivních virtuálních procesorů, aby byly pro vás snadněji identifikovatelné.

Například aktuální velikost virtuálního počítače Standard_GS5 je dodáván s 32 virtuálními procesory, 448 GB RAM, 64 disky (až 256 TB) a 80 000 IOP nebo 2 GB/s šířky pásma vstupně-v.I/O. Nové velikosti virtuálních zařízení Standard_GS5-16 a Standard_GS5-8 jsou dodávány s 16 a 8 aktivními virtuálními procesory při zachování zbývajících specifikací Standard_GS5 pro paměť, úložiště a šířku pásma vstupně-up.

Licenční poplatky účtované pro SQL Server nebo Oracle jsou omezeny na nový počet virtuálních procesorů a další produkty by měly být účtovány na základě nového počtu virtuálních procesorů. Výsledkem je 50 % až 75% zvýšení poměru specifikací virtuálních zařízení k aktivním (fakturovatelným) virtuálním procesorům. Tyto nové velikosti virtuálních zařízení umožňují úlohám zákazníků používat stejnou šířku pásma paměti, úložiště a vstupně-v.i. a zároveň optimalizovat náklady na licencování softwaru. V tuto chvíli náklady na výpočetní prostředky, které zahrnují licencování operačního serveru, zůstávají stejné jako původní velikost. Další informace najdete v tématu [velikosti virtuálních virtuálních počítače Azure pro nákladově efektivnější databázové úlohy](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).


| Name (Název)                | Virtuální procesory | Specifikace           |
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
| Standard_GS5-16.     | 16   | Stejné jako GS5     |
| Standard_GS5-8      | 8    | Stejné jako GS5     |
| Standard_DS11-1_v2  | 1    | Stejné jako DS11_v2 |
| Standard_DS12-2_v2  | 2    | Stejné jako DS12_v2 |
| Standard_DS12-1_v2  | 1    | Stejné jako DS12_v2 |
| Standard_DS13-4_v2  | 4    | Stejné jako DS13_v2 |
| Standard_DS13-2_v2  | 2    | Stejné jako DS13_v2 |
| Standard_DS14-8_v2  | 8    | Stejné jako DS14_v2 |
| Standard_DS14-4_v2  | 4    | Stejné jako DS14_v2 |
