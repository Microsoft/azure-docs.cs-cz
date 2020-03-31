---
title: Řada NVv4 – virtuální počítače Azure
description: Specifikace pro virtuální chod řady NVv4.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 0295ed7d44d64fcc1aeb68e1beaa37987b177edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273848"
---
# <a name="nvv4-series"></a>Řada NVv4 

Virtuální stroje řady NVv4 jsou poháněny grafickými procesory [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) a procesory AMD EPYC 7V12(Řím). S NVv4-series Azure zavádí virtuální počítače s částečnými GPU. Vyberte si virtuální stroj správné velikosti pro grafické aplikace a virtuální plochy s akcelerací GPU od 1/8th GPU s vyrovnávací pamětí 2 rámce GiB na plný GPU s vyrovnávací pamětí 16 GiB frame. Virtuální počítače NVv4 v současné době podporují pouze hostovaný operační systém Windows.

<br>

Úložiště Premium: Podporováno

Ukládání do mezipaměti úložiště Premium: Podporováno

Migrace za provozu: Není podporována

Aktualizace pro zachování paměti: Není podporováno

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální počet síťových karet |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 |

<sup>1</sup> Virtuální zařízení řady NVv4 jsou vybavena technologií vícevláknového vlákna AMD Simultaneous

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Podporované operační systémy a ovladače

Chcete-li využít funkce GPU virtuálních aplikací Řady Azure NVv4 se systémem Windows, musí být nainstalovány ovladače GPU AMD.

Pokud chcete ručně nainstalovat ovladače GPU AMD, přečtěte si informace o podporovaných operačních systémech, ovladačích, instalaci a postupech ověření v [části Nastavení ovladače GPU AMD řady N pro Windows.](./windows/n-series-amd-driver-setup.md)

## <a name="other-sizes"></a>Jiné velikosti

- [Obecné účely](sizes-general.md)
- [Optimalizované z hlediska paměti](sizes-memory.md)
- [Optimalizované z hlediska úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám můžou pomoct porovnat výpočetní výkon napříč virtuálními jednotkami Azure.
