---
ms.openlocfilehash: 2e92d150851c74a84f785d1f5f0ebe2e5870a54e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936752"
---


| Funkce |[Plán Consumption](../articles/azure-functions/consumption-plan.md)|[Plán Premium](../articles/azure-functions/functions-premium-plan.md)|[Plán Dedicated](../articles/azure-functions/dedicated-plan.md)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Omezení příchozí IP adresy a přístup k privátnímu webu](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|✅Yes|
|[Integrace virtuální sítě](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌No|✅Ano (místní)|✅Ano (místní a brána)|✅Yes| ✅Yes|
|[Aktivační události virtuální sítě (jiné než HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌No| ✅Yes |✅Yes|✅Yes|✅Yes|
|[Hybridní připojení](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (jenom Windows)|❌No|✅Yes|✅Yes|✅Yes|✅Yes|
|[Omezení odchozích IP adres](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌No| ✅Yes|✅Yes|✅Yes|✅Yes|