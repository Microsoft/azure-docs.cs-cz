---
title: Společné umístění virtuálních počítačů se systémem Linux
description: Seznamte se s tím, jak se souběžně vyhledávají prostředky virtuálních počítačů Azure pro Linux můžou zvýšit latence.
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: zivr
ms.openlocfilehash: 822d67211982526bdd7a36cb5922f33b448eb7c7
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91973372"
---
# <a name="co-locate-resources-for-improved-latency"></a>Snížení latence umisťováním prostředků do společného umístění

Při nasazování aplikace v Azure vytvoří rozšíření instancí napříč oblastmi nebo zónami dostupnosti latenci sítě, což může mít vliv na celkový výkon vaší aplikace. 

## <a name="proximity-placement-groups"></a>Skupiny umístění bezkontaktní komunikace

[!INCLUDE [virtual-machines-common-ppg-overview](../../../includes/virtual-machines-common-ppg-overview.md)]

## <a name="next-steps"></a>Další kroky

Nasaďte virtuální počítač do [skupiny umístění blízkosti](proximity-placement-groups.md) pomocí Azure CLI.

Naučte se [testovat latenci sítě](../../virtual-network/virtual-network-test-latency.md?toc=%252fazure%252fvirtual-machines%252flinux%252ftoc.json).

Naučte se [optimalizovat propustnost sítě](../../virtual-network/virtual-network-optimize-network-bandwidth.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

Naučte [se používat skupiny umístění pro Proximity s aplikacemi SAP](../workloads/sap/sap-proximity-placement-scenarios.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).