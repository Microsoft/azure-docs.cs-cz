---
title: Cíle škálovatelnosti pro účty úložiště objektů blob stránky úrovně Premium
titleSuffix: Azure Storage
description: Účet úložiště objektů blob stránky výkonu Premium je optimalizovaný pro operace čtení a zápisu. Tento typ účtu úložiště zálohuje nespravovaný disk pro virtuální počítač Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e134c69f5d602cb4369e9410e3e2b9d3478b11a0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "76756245"
---
# <a name="scalability-and-performance-targets-for-premium-page-blob-storage-accounts"></a>Škálovatelnost a výkonnostní cíle pro účty úložiště objektů blob stránky úrovně Premium

[!INCLUDE [storage-scalability-intro-include](../../../includes/storage-scalability-intro-include.md)]

## <a name="scale-targets-for-premium-page-blob-accounts"></a>Cíle škálování pro účty objektů blob stránky úrovně Premium

Účet úložiště blob stránky úrovně Premium je optimalizovaný pro operace čtení a zápisu. Tento typ účtu úložiště zálohuje nespravovaný disk pro virtuální počítač Azure.

> [!NOTE]
> Pokud je to možné, Microsoft doporučuje používat spravované disky s virtuálními počítači Azure. Další informace o spravovaných discích najdete v tématu [přehled Azure disk Storagech virtuálních počítačů s Windows](../../virtual-machines/windows/managed-disks-overview.md).

Účty úložiště blob stránky úrovně Premium mají následující cíle škálovatelnosti:

| Celková kapacita účtu                            | Celková šířka pásma pro místně redundantní účet úložiště                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| Kapacita disku: 4 TB (jednotlivý disk)/35 TB (kumulativní součet všech disků) <br>Kapacita snímku: 10 TB | Až 50 gigabitů za sekundu pro příchozí<sup>1</sup> + odchozí<sup>2</sup> |

<sup>1</sup> všechna data (požadavky), která se odesílají do účtu úložiště

<sup>2</sup> všechna data (odpovědi) přijatá z účtu úložiště

Účet objektu blob stránky úrovně Premium je účet pro obecné účely nakonfigurovaný pro výkon Premium. Doporučují se účty úložiště pro obecné účely verze 2.

Pokud používáte účty úložiště blob stránky úrovně Premium pro nespravované disky a vaše aplikace překračuje cíle škálovatelnosti jednoho účtu úložiště, Microsoft doporučuje migrovat na spravované disky. Další informace o spravovaných discích najdete v tématu [přehled Azure disk Storagech virtuálních počítačů s Windows](../../virtual-machines/windows/managed-disks-overview.md) nebo [Přehled Azure Disk Storage pro virtuální počítače](../../virtual-machines/linux/managed-disks-overview.md)se systémem Linux.

Pokud nemůžete migrovat na Managed disks, sestavte aplikaci tak, aby používala více účtů úložiště, a vytvořte oddíly dat v rámci těchto účtů úložiště. Například pokud chcete připojit 51 TB mezi více virtuálními počítači, rozšíříte je napříč dvěma účty úložiště. 35 TB je limit pro jeden účet Premium Storage. Ujistěte se, že jeden účet úložiště výkonu Premium nemá nikdy více než 35 TB zřízených disků.

## <a name="see-also"></a>Viz také

- [Škálovatelnost a výkonnostní cíle pro účty úložiště úrovně Standard](../common/scalability-targets-standard-account.md)
- [Cíle škálovatelnosti pro účty úložiště objektů blob bloku úrovně Premium](../blobs/scalability-targets-premium-block-blobs.md)
- [Omezení a kvóty předplatného Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md)
