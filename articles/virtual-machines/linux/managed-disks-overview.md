---
title: Přehled spravovaného disku Azure Disk Storage pro virtuální počítače se systémem Linux
description: Přehled služby Azure Managed disks, které zpracovávají účty úložiště při používání virtuálních počítačů se systémem Linux
author: roygara
ms.service: virtual-machines-linux
ms.topic: overview
ms.date: 11/06/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7e9a5e1b1e1b0c879a1264573e7073e70a1e95fc
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035870"
---
# <a name="introduction-to-azure-managed-disks"></a>Seznámení se službou Azure Managed disks

Spravovaný disk Azure je virtuální pevný disk (VHD). Můžete si to představit jako fyzický disk na místním serveru, ale virtualizované. Služby Azure Managed disks se ukládají jako objekty blob stránky, které jsou v Azure náhodným objektem úložiště v/v. Zavoláme spravovaný spravovaný disk, protože se jedná o abstrakci přes objekty blob stránky, kontejnery objektů BLOB a účty Azure Storage. U spravovaných disků stačí zřídit disk a Azure se postará o zbytek.

Když vyberete použití Azure Managed disks s vašimi úlohami, Azure vytvoří a spravuje disk za vás. Dostupné typy disků jsou ultra disk, Premium Solid State Drive (SSD), SSD úrovně Standard a standardní jednotka pevného disku (HDD). Další informace o jednotlivých typech jednotlivých disků najdete v tématu [Výběr typu disku pro virtuální počítače s IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Vyberte typ disku pro virtuální počítače s IaaS.](disks-types.md)
