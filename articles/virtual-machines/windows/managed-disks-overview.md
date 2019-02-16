---
title: Azure Disk Storage spravovaných přehled disků pro virtuální počítače s Windows | Dokumentace Microsoftu
description: Přehled služby Azure spravované disky, které stará o účty úložiště za vás při používání virtuální počítače Azure s Windows
services: virtual-machines-windows,storage
author: roygara
ms.service: virtual-machines-windows
ms.workload: storage
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/11/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 85b2dcb73024ce786b78436b7070ad2e9a96e1d4
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328466"
---
# <a name="introduction-to-azure-managed-disks"></a>Úvod do služby Azure managed disks

Spravované v Azure disk je virtuální pevný disk (VHD). Můžete si ho představit jako fyzický disk v na místním serveru ale, virtualizované. Spravované disky Azure jsou uložené jako objekty BLOB stránky, které jsou objekt náhodných vstupně-výstupní operace úložiště v Azure. Označujeme jako "spravované", protože je abstrakcí objekty BLOB stránky a kontejnery objektů blob a účty služby Azure storage spravovaného disku. Se spravovanými disky vše, co musíte udělat je zřízení disku a zbytek se postará Azure.

Když vyberete do Azure můžete používat spravované disky s vašimi úlohami, Azure vytvoří a spravuje disk za vás. Dostupné typy disků jsou Ultra Solid stav jednotky SSD (Solid-State Drive) (Preview), Premium SSD, SSD na úrovni Standard a standardních pevných disků (HDD). Další informace o jednotlivých typech jednotlivých disků najdete v tématu [vyberte typ disku pro virtuální počítače IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Vyberte typ disku pro virtuální počítače IaaS](disks-types.md)