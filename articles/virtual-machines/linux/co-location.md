---
title: Společné umístění virtuálních počítačů se systémem Linux
description: Přečtěte si, jak se ve společném umístění prostředků virtuálních počítačů Azure dá vylepšit latence.
ms.service: virtual-machines
ms.topic: article
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: d2fd8a2cd7dac7b1d3c78691c84a861d924005ce
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "79250280"
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
