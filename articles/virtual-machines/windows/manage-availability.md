---
title: Správa dostupnosti virtuálních počítačů s Windows v Azure
description: Naučte se používat víc virtuálních počítačů, abyste zajistili vysokou dostupnost vaší aplikace pro Windows v Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 02351953-7b6a-4657-b9e1-de2ea8f6aa05
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/27/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1f9914b84b63f271c7dd7d1b8f7dbc3b69511605
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561192"
---
# <a name="manage-the-availability-of-windows-virtual-machines-in-azure"></a>Správa dostupnosti virtuálních počítačů s Windows v Azure 

Naučte se, jak nastavit a spravovat víc virtuálních počítačů, abyste zajistili vysokou dostupnost vaší aplikace pro Windows v Azure. Můžete také [spravovat dostupnost virtuálních počítačů se systémem Linux](../linux/manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Pokyny k vytváření a používání skupin dostupnosti při použití modelu nasazení Classic najdete v tématu [Konfigurace skupiny dostupnosti](classic/configure-availability-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>Další kroky
Další informace o vyrovnávání zatížení virtuálních počítačů najdete v tématu [Vyrovnávání zatížení virtuálních počítačů](tutorial-load-balancer.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Zobrazení referenčních architektur pro spouštění N-vrstvých aplikací na SQL Server v IaaS

* [N-vrstvá aplikace Windows v Azure s SQL Server](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [Spuštění N-vrstvé aplikace v několika oblastech Azure pro zajištění vysoké dostupnosti](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
