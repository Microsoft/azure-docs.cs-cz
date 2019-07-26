---
title: Přehled spravovaného disku Azure Disk Storage pro virtuální počítače se systémem Linux | Microsoft Docs
description: Přehled služby Azure Managed disks, které při používání virtuálních počítačů se systémem Linux zpracovávají účty úložiště.
services: virtual-machines-linux,storage
author: roygara
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: overview
ms.date: 04/22/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 18dc1bd2eea232d0c2eb73d496dd4bd9d2d5016e
ms.sourcegitcommit: 57a7d4f67635212f5bf0c56e58fd87c8ec366f2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "64707799"
---
# <a name="introduction-to-azure-managed-disks"></a>Seznámení se službou Azure Managed disks

Spravovaný disk Azure je virtuální pevný disk (VHD). Můžete si to představit jako fyzický disk na místním serveru, ale virtualizované. Služby Azure Managed disks se ukládají jako objekty blob stránky, které jsou v Azure náhodným objektem úložiště v/v. Zavoláme spravovaný spravovaný disk, protože se jedná o abstrakci přes objekty blob stránky, kontejnery objektů BLOB a účty Azure Storage. U spravovaných disků stačí zřídit disk a Azure se postará o zbytek.

Když vyberete použití Azure Managed disks s vašimi úlohami, Azure vytvoří a spravuje disk za vás. K dispozici jsou tyto typy disků: Ultra Disks (Preview), disk SSD (Solid-State Drive), Standard SSD a standardní pevné disky (HDD). Další informace o jednotlivých typech jednotlivých disků najdete v tématu [Výběr typu disku pro virtuální počítače s IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Vyberte typ disku pro virtuální počítače s IaaS.](disks-types.md)
