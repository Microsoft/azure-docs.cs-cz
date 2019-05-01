---
title: Azure Disk Storage spravovaných přehled disků pro virtuální počítače s Linuxem | Dokumentace Microsoftu
description: Přehled služby Azure spravované disky, které stará o účty úložiště za vás při používání virtuálních počítačů s Linuxem
services: virtual-machines-linux,storage
author: roygara
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: overview
ms.date: 04/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 18dc1bd2eea232d0c2eb73d496dd4bd9d2d5016e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64707799"
---
# <a name="introduction-to-azure-managed-disks"></a>Úvod do služby Azure managed disks

Spravované v Azure disk je virtuální pevný disk (VHD). Můžete si ho představit jako fyzický disk v na místním serveru ale, virtualizované. Spravované disky Azure jsou uložené jako objekty BLOB stránky, které jsou objekt náhodných vstupně-výstupní operace úložiště v Azure. Označujeme jako "spravované", protože je abstrakcí objekty BLOB stránky a kontejnery objektů blob a účty služby Azure storage spravovaného disku. Se spravovanými disky vše, co musíte udělat je zřízení disku a zbytek se postará Azure.

Když vyberete do Azure můžete používat spravované disky s vašimi úlohami, Azure vytvoří a spravuje disk za vás. Dostupné typy disky jsou disky ultra (Preview), premium jednotky SSD (Solid-State Drive), SSD na úrovni standard, a standardní pevné disky (HDD). Další informace o jednotlivých typech jednotlivých disků najdete v tématu [vyberte typ disku pro virtuální počítače IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Vyberte typ disku pro virtuální počítače IaaS](disks-types.md)
