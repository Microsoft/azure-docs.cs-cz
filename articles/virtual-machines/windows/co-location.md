---
title: Spoluhledání virtuálních mích pro lepší latenci
description: Přečtěte si, jak spoluvyhledání prostředků virtuálních počítačů Azure může zlepšit latenci.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 066b6d10dbe07b902abfd15565c5ccf1a5e9c115
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083168"
---
# <a name="co-locate-resource-for-improved-latency"></a>Spoluhledání prostředku pro lepší latenci

Při nasazování aplikace v Azure, rozprostření instancí mezi oblastmi nebo zóny dostupnosti vytvoří latenci sítě, což může mít vliv na celkový výkon vaší aplikace. 


## <a name="proximity-placement-groups"></a>Skupiny umístění bezkontaktní komunikace 

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Další kroky

Nasazení virtuálního počítače do [skupiny umístění bezkontaktní](proximity-placement-groups.md) pomocí Azure PowerShellu.

Přečtěte si, jak [otestovat latenci sítě](https://aka.ms/TestNetworkLatency?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Přečtěte si, jak [optimalizovat propustnost sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-optimize-network-bandwidth?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).  

Přečtěte si, jak [používat skupiny umístění bezkontaktní komunikace s aplikacemi SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).