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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/26/2020
ms.locfileid: "67175002"
---
Některé databázové úlohy, jako je SQL Server nebo Oracle, vyžadují vysoké množství paměti, úložiště a vstupně-výstupních operací, ale ne vysoký počet jader. Mnoho úloh databáze není náročné na procesor. Azure nabízí určité velikosti virtuálních počítačů, ve kterých můžete omezit počet vCPU virtuálních počítačů a snížit tak náklady na licencování softwaru a zároveň zachovat stejnou velikost paměti, úložiště a vstupně-výstupní šířky pásma.

Počet vCPU může být omezen na jednu polovinu nebo jednu čtvrtinu původní velikosti virtuálního počítače. Tyto nové velikosti virtuálních počítačů mají příponu, která určuje počet aktivních vCPU, aby je bylo snazší identifikovat.

Aktuální velikost virtuálního počítače Standard_GS5 například přináší 32 vCPU, 448 GB RAM, 64 disků (až 256 TB) a 80 000 IOPs nebo 2 GB/s šířky pásma I/O. Nové velikosti virtuálních počítačů Standard_GS5-16 a Standard_GS5-8 se dodávají s 16 a 8 aktivními vCPU a přitom udržují zbytek specifikací Standard_GS5 pro paměť, úložiště a šířku pásma I/O.

Licenční poplatky účtované za SQL Server nebo Oracle jsou omezené na nový počet vCPU a další produkty by se měly účtovat na základě nového počtu vCPU. Výsledkem je 50% až 75% zvýšení poměru specifikací virtuálních počítačů na aktivní (fakturovatelný) vCPU. Tyto nové velikosti virtuálních počítačů umožňují zákaznickým úlohám při optimalizaci nákladů na software využívat stejnou paměť, úložiště a I/O šířku pásma. V tomto okamžiku zůstává náklady na výpočetní výkon, včetně licencování operačního systému, stejné jako původní velikost. Další informace najdete v tématu [velikosti virtuálních počítačů Azure pro cenově výhodné databázové úlohy](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).


| Název                | Virtuální procesory | Specifikace           |
|---------------------|------|-----------------|
| Standard_M8 – 2 MS     | 2    | Stejné jako M8ms    |
| Standard_M8 – 4ms     | 4    | Stejné jako M8ms    |
| Standard_M16 – 4ms    | 4    | Stejné jako M16ms   |
| Standard_M16 – 8ms    | 8    | Stejné jako M16ms   |
| Standard_M32 – 8ms    | 8    | Stejné jako M32ms   |
| Standard_M32 – 16MS   | 16   | Stejné jako M32ms   |
| Standard_M64 – 32ms   | 32   | Stejné jako M64ms   |
| Standard_M64 – 16MS   | 16   | Stejné jako M64ms   |
| Standard_M128 – 64ms  | 64   | Stejné jako M128ms  |
| Standard_M128 – 32ms  | 32   | Stejné jako M128ms  |
| Standard_E4 – 2s_v3   | 2    | Stejné jako E4s_v3  |
| Standard_E8 – 4s_v3   | 4    | Stejné jako E8s_v3  |
| Standard_E8 – 2s_v3   | 2    | Stejné jako E8s_v3  |
| Standard_E16 – 8s_v3  | 8    | Stejné jako E16s_v3 |
| Standard_E16 – 4s_v3  | 4    | Stejné jako E16s_v3 |
| Standard_E32 – 16s_v3 | 16   | Stejné jako E32s_v3 |
| Standard_E32 – 8s_v3  | 8    | Stejné jako E32s_v3 |
| Standard_E64 – 32s_v3 | 32   | Stejné jako E64s_v3 |
| Standard_E64 – 16s_v3 | 16   | Stejné jako E64s_v3 |
| Standard_GS4-8      | 8    | Stejné jako GS4 úrovně     |
| Standard_GS4-4      | 4    | Stejné jako GS4 úrovně     |
| Standard_GS5-16     | 16   | Stejné jako GS5     |
| Standard_GS5-8      | 8    | Stejné jako GS5     |
| Standard_DS11 – 1_v2  | 1    | Stejné jako DS11_v2 |
| Standard_DS12 – 2_v2  | 2    | Stejné jako DS12_v2 |
| Standard_DS12 – 1_v2  | 1    | Stejné jako DS12_v2 |
| Standard_DS13 – 4_v2  | 4    | Stejné jako DS13_v2 |
| Standard_DS13 – 2_v2  | 2    | Stejné jako DS13_v2 |
| Standard_DS14 – 8_v2  | 8    | Stejné jako DS14_v2 |
| Standard_DS14 – 4_v2  | 4    | Stejné jako DS14_v2 |
