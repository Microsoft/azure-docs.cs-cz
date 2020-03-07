---
title: Společné umístění virtuálních počítačů Azure s Linuxem
description: Přečtěte si, jak se ve společném umístění prostředků virtuálních počítačů Azure dá vylepšit latence.
services: virtual-machines-linux
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: c90e70900c019340cacc169325155ac74cf7807c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78380618"
---
# <a name="co-locate-resources-for-improved-latency"></a>Společné umístění prostředků pro lepší latenci

Při nasazování aplikace v Azure vytvoří rozšíření instancí napříč oblastmi nebo zónami dostupnosti latenci sítě, což může mít vliv na celkový výkon vaší aplikace. 

## <a name="proximity-placement-groups"></a>Skupiny umístění bezkontaktní komunikace

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Další kroky

Nasaďte virtuální počítač do [skupiny umístění blízkosti](proximity-placement-groups.md) pomocí Azure CLI.

Naučte se [testovat latenci sítě](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Naučte se [optimalizovat propustnost sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

Naučte [se používat skupiny umístění pro Proximity s aplikacemi SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
