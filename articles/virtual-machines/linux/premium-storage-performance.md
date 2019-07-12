---
title: 'Azure Premium Storage: Návrh pro zajištění výkonu na virtuálních počítačích s Windows | Dokumentace Microsoftu'
description: Navrhněte výkonné aplikace pomocí služby Azure Premium Storage. Premium Storage nabízí podporu vysoce výkonných disků s nízkou latencí pro úlohy můžu vstupně-výstupními operacemi na Azure Virtual Machines.
services: virtual-machines-linux,storage
author: roygara
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.topic: article
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: c6de3144a87a5bfad38e1b33148f292b26c0f181
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67658251"
---
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> V některých případech možný problém výkonu disků je ve skutečnosti sítě kritickým bodem. V takových situacích doporučujeme optimalizovat vaše [výkon sítě](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Pokud chcete provést srovnávací testy na disku, najdete v článku na [srovnávací testy disk](disks-benchmarks.md).
>
> Pokud váš virtuální počítač podporuje akcelerované síťové služby, by měl se ujistěte, zda že je povoleno. Pokud není povolená, můžete ji povolit na již nasazených virtuálních počítačů na obou [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) a [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Než začnete, pokud jste ještě na Premium Storage, nejdřív přečíst [vyberte typ, který Azure disk pro virtuální počítače IaaS](disks-types.md) a [škálovatelnost a výkonnostní cíle Azure Storage pro účty úložiště](../../storage/common/storage-scalability-targets.md).


[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]
