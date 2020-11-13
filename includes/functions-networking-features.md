---
ms.openlocfilehash: 82571d1a0e651f638dec29184f0ecdc88562b3ad
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578832"
---


| Příznak |[Plán Consumption](../articles/azure-functions/functions-scale.md#consumption-plan)|[Plán Premium](../articles/azure-functions/functions-scale.md#premium-plan)|[Vyhrazený plán](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Omezení příchozí IP adresy a přístup k privátnímu webu](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|✅Yes|
|[Integrace virtuální sítě](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌Ne|✅Ano (místní)|✅Ano (místní a brána)|✅Yes| ✅Yes|
|[Aktivační události virtuální sítě (jiné než HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌Ne| ✅Yes |✅Yes|✅Yes|✅Yes|
|[Hybridní připojení](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (jenom Windows)|❌Ne|✅Yes|✅Yes|✅Yes|✅Yes|
|[Omezení odchozích IP adres](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌Ne| ✅Yes|✅Yes|✅Yes|✅Yes|