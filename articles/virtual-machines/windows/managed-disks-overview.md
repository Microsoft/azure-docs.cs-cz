---
title: Přehled spravovaného disku Azure Disk Storage pro virtuální počítače s Windows | Microsoft Docs
description: Přehled služby Azure Managed disks, které při používání virtuálních počítačů Azure s Windows zpracovávají účty úložiště.
author: roygara
ms.service: virtual-machines-windows
ms.topic: overview
ms.date: 08/15/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: c71d51580c064b5b2b0bbbbf7bf95d50aa40f72f
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515566"
---
# <a name="introduction-to-azure-managed-disks"></a>Seznámení se službou Azure Managed disks

Spravovaný disk Azure je virtuální pevný disk (VHD). Můžete si to představit jako fyzický disk na místním serveru, ale virtualizované. Služby Azure Managed disks se ukládají jako objekty blob stránky, které jsou v Azure náhodným objektem úložiště v/v. Zavoláme spravovaný spravovaný disk, protože se jedná o abstrakci přes objekty blob stránky, kontejnery objektů BLOB a účty Azure Storage. U spravovaných disků stačí zřídit disk a Azure se postará o zbytek.

Když vyberete použití Azure Managed disks s vašimi úlohami, Azure vytvoří a spravuje disk za vás. Dostupné typy disků jsou ultra disk, Premium Solid State Drive (SSD), SSD úrovně Standard a standardní jednotka pevného disku (HDD). Další informace o jednotlivých typech jednotlivých disků najdete v tématu [Výběr typu disku pro virtuální počítače s IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Vyberte typ disku pro virtuální počítače s IaaS.](disks-types.md)