---
title: Spoluhledání virtuálních aplikací Windows Azure
description: Přečtěte si, jak spoluvyhledání prostředků virtuálních počítačů Azure může zlepšit latenci.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: b61a84f2b284177b7443be00efeea65e7eb8aaef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266790"
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