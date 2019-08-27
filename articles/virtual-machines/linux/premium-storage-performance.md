---
title: 'Premium Storage Azure: Návrh pro výkon na virtuálních počítačích s Windows | Microsoft Docs'
description: Navrhněte vysoce výkonné aplikace s využitím Azure Premium Storage. Premium Storage nabízí podporu vysoce výkonných disků s nízkou latencí pro úlohy náročné na vstupně-výstupní operace běžící na Azure Virtual Machines.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 79bd41c08a566b55efb4a29084847848e001629f
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2019
ms.locfileid: "70036044"
---
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> V některých případech se může jednat o problém s výkonem disku, což je kritické místo v síti. V těchto situacích byste měli optimalizovat [výkon sítě](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Pokud chcete otestovat srovnávací test disku, přečtěte si náš článek o [testování disku](disks-benchmarks.md).
>
> Pokud váš virtuální počítač podporuje akcelerované síťové služby, měli byste se ujistit, že je povolený. Pokud není povolená, můžete ji povolit na již nasazených virtuálních počítačích v [systému Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) i [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Než začnete, pokud s Premium Storage teprve začínáte, nejdříve si přečtěte téma [Výběr typu disku Azure pro virtuální počítače s IaaS](disks-types.md) a [Azure Storage škálovatelnost a výkonnostní cíle pro účty úložiště](../../storage/common/storage-scalability-targets.md).


[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

Pokud chcete otestovat srovnávací test disku, přečtěte si náš článek o [testování disku](disks-benchmarks.md).

Další informace o dostupných typech disků: [Vybrat typ disku](disks-types.md)  

Informace o SQL Server uživatelů najdete v článcích o osvědčených postupech výkonu pro SQL Server:

* [Osvědčené postupy výkonu pro SQL Server v Azure Virtual Machines](../windows/sql/virtual-machines-windows-sql-performance.md)
* [Azure Premium Storage poskytuje nejvyšší výkon pro SQL Server na virtuálním počítači Azure.](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)