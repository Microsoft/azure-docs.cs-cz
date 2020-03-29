---
title: Cíle škálovatelnosti pro účty úložiště objektů blob stránky premium
titleSuffix: Azure Storage
description: Účet úložiště objektů blob stránky výkonu úrovně premium je optimalizovaný pro operace čtení a zápisu. Tento typ účtu úložiště podporuje nespravovaný disk pro virtuální počítač Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e134c69f5d602cb4369e9410e3e2b9d3478b11a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756245"
---
# <a name="scalability-and-performance-targets-for-premium-page-blob-storage-accounts"></a>Škálovatelnost a výkonnostní cíle pro účty úložiště objektů blob stránky premium

[!INCLUDE [storage-scalability-intro-include](../../../includes/storage-scalability-intro-include.md)]

## <a name="scale-targets-for-premium-page-blob-accounts"></a>Škálování cílů pro účty objektů blob stránky premium

Účet úložiště objektů blob stránky s výkonem s výkonem je optimalizovaný pro operace čtení a zápisu. Tento typ účtu úložiště podporuje nespravovaný disk pro virtuální počítač Azure.

> [!NOTE]
> Společnost Microsoft doporučuje používat spravované disky s virtuálními počítači Azure(VM) pokud je to možné. Další informace o spravovaných discích najdete [v tématu Přehled úložiště disků Azure pro virtuální počítače s Windows](../../virtual-machines/windows/managed-disks-overview.md).

Účty úložiště objektů blob stránky Premium mají následující cíle škálovatelnosti:

| Celková kapacita účtu                            | Celková šířka pásma pro místně redundantní účet úložiště                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Kapacita disku: 4 TB (samostatný disk)/ 35 TB (kumulativní součet všech disků) <br>Kapacita snímku: 10 TB | Až 50 gigabitů za sekundu pro příchozí<sup>1</sup> + odchozí<sup>2</sup> |

<sup>1</sup> Všechna data (požadavky), která jsou odeslána na účet úložiště

<sup>2</sup> Všechna data (odpovědi), která jsou přijata z účtu úložiště

Účet objektů blob stránky premium je účet pro obecné účely nakonfigurovaný pro výkon úrovně. Obecně se doporučují účty úložiště v2 pro obecné účely.

Pokud používáte účty úložiště objektů blob stránky Premium pro nespravované disky a vaše aplikace překračuje cíle škálovatelnosti jednoho účtu úložiště, pak společnost Microsoft doporučuje migraci na spravované disky. Další informace o spravovaných discích najdete v [tématu Přehled úložiště disků Azure pro virtuální počítače s Windows](../../virtual-machines/windows/managed-disks-overview.md) nebo Přehled azure [diskového úložiště pro virtuální počítače s Linuxem](../../virtual-machines/linux/managed-disks-overview.md).

Pokud nemůžete migrovat na spravované disky, vytvořte aplikaci tak, aby používala více účtů úložiště a rozdělila data mezi tyto účty úložiště. Pokud například chcete připojit disky o velikosti 51 TB mezi více virtuálních počítačů, rozdělte je mezi dva účty úložiště. 35 TB je limit pro jeden účet úložiště premium. Ujistěte se, že jeden účet úložiště výkonu premium nikdy nemá více než 35 TB zřízených disků.

## <a name="see-also"></a>Viz také

- [Škálovatelnost a výkonnostní cíle pro účty standardního úložiště](../common/scalability-targets-standard-account.md)
- [Cíle škálovatelnosti pro účty úložiště objektů blob s prémiovými bloky](../blobs/scalability-targets-premium-block-blobs.md)
- [Limity a kvóty předplatného Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md)
