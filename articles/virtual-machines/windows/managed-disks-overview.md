---
title: Přehled spravovaného disku Azure Disk Storage pro virtuální počítače s Windows | Microsoft Docs
description: Přehled služby Azure Managed disks, které při používání virtuálních počítačů Azure s Windows zpracovávají účty úložiště.
services: virtual-machines-windows,storage
author: roygara
ms.service: virtual-machines-windows
ms.workload: storage
ms.tgt_pltfrm: vm-windows
ms.topic: overview
ms.date: 04/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: fb1ee8556935b141dfee6a18c96ecafb476aa584
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "64725811"
---
# <a name="introduction-to-azure-managed-disks"></a>Seznámení se službou Azure Managed disks

Spravovaný disk Azure je virtuální pevný disk (VHD). Můžete si to představit jako fyzický disk na místním serveru, ale virtualizované. Služby Azure Managed disks se ukládají jako objekty blob stránky, které jsou v Azure náhodným objektem úložiště v/v. Zavoláme spravovaný spravovaný disk, protože se jedná o abstrakci přes objekty blob stránky, kontejnery objektů BLOB a účty Azure Storage. U spravovaných disků stačí zřídit disk a Azure se postará o zbytek.

Když vyberete použití Azure Managed disks s vašimi úlohami, Azure vytvoří a spravuje disk za vás. Dostupné typy disků jsou ultra Solid State Drive (SSD) (verze Preview), SSD úrovně Premium, SSD úrovně Standard a standardní jednotky pevného disku (HDD). Další informace o jednotlivých typech jednotlivých disků najdete v tématu [Výběr typu disku pro virtuální počítače s IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Vyberte typ disku pro virtuální počítače s IaaS.](disks-types.md)