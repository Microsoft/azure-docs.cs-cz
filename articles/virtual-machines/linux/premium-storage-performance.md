---
title: 'Azure Premium Storage: Návrh výkonu na virtuálních počítačích s Linuxem | Dokumenty společnosti Microsoft'
description: Navrhujte vysoce výkonné aplikace pomocí spravovaných disků Azure premium SSD. Úložiště Premium storage nabízí vysoce výkonnou podporu disku s nízkou latencí pro úlohy náročné na vstupně-výstupní služby spuštěné na virtuálních počítačích Azure.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 9940ee4cfce9721ac65f2b3cf1469e180adfa098
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267141"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Úložiště Azure premium: návrh pro vysoký výkon
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Někdy se zdá, že problém s výkonem disku je ve skutečnosti problémem sítě. V těchto situacích byste měli optimalizovat [výkon sítě](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Pokud hledáte benchmark disku, viz náš článek o [Benchmarking disk](disks-benchmarks.md).
>
> Pokud váš virtuální počítač podporuje zrychlené sítě, měli byste se ujistit, že je povolená. Pokud není povolena, můžete ji povolit na již nasazených virtuálních počítačích ve [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) i [Linuxu](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Než začnete, pokud jste novým úložištěm Premium, přečtěte si [nejprve typ disku Select a Azure pro virtuální počítače IaaS](disks-types.md) a [cíle škálovatelnosti pro účty úložiště objektů blob stránky premium](../../storage/blobs/scalability-targets-premium-page-blobs.md).


[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

Pokud hledáte benchmark disku, viz náš článek o [Benchmarking disk](disks-benchmarks.md).

Další informace o dostupných typech disků: [Vyberte typ disku.](disks-types.md)  

Pro uživatele serveru SQL Server si přečtěte články o doporučených postupech výkonu pro SQL Server:

* [Doporučené postupy výkonu pro SQL Server ve virtuálních počítačích Azure](../windows/sql/virtual-machines-windows-sql-performance.md)
* [Azure Premium Storage poskytuje nejvyšší výkon pro SQL Server ve virtuálním počítači Azure](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)