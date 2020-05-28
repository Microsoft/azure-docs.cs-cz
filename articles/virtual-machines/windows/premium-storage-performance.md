---
title: 'Azure Premium Storage: návrh pro výkon na virtuálních počítačích s Windows | Microsoft Docs'
description: Navrhněte vysoce výkonné aplikace pomocí spravovaných disků Azure Premium SSD. Premium Storage nabízí podporu vysoce výkonných disků s nízkou latencí pro úlohy náročné na vstupně-výstupní operace běžící na Azure Virtual Machines.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 06ac680ef48d9602f6e7487eb9ebecee9f3ae080
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84017595"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure Premium Storage: návrh pro vysoký výkon
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> V některých případech se může jednat o problém s výkonem disku, což je kritické místo v síti. V těchto situacích byste měli optimalizovat [výkon sítě](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Pokud chcete otestovat srovnávací test disku, přečtěte si náš článek o [testování disku](disks-benchmarks.md).
>
> Pokud váš virtuální počítač podporuje akcelerované síťové služby, měli byste se ujistit, že je povolený. Pokud není povolená, můžete ji povolit na již nasazených virtuálních počítačích v [systému Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) i [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Než začnete, pokud s Premium Storage teprve začínáte, nejdříve si přečtěte téma [Výběr typu disku Azure pro virtuální počítače s IaaS](disks-types.md) a [cíle škálovatelnosti pro účty úložiště objektů blob stránky úrovně Premium](../../storage/blobs/scalability-targets-premium-page-blobs.md).

[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

Pokud chcete otestovat srovnávací test disku, přečtěte si náš článek o [testování disku](disks-benchmarks.md).

Další informace o dostupných typech disků: [Vyberte typ disku](disks-types.md) .  

Informace o SQL Server uživatelů najdete v článcích o osvědčených postupech výkonu pro SQL Server:

* [Osvědčené postupy výkonu pro SQL Server v Azure Virtual Machines](../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md)
* [Azure Premium Storage poskytuje nejvyšší výkon pro SQL Server na virtuálním počítači Azure.](https://cloudblogs.microsoft.com/sqlserver/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm/)
