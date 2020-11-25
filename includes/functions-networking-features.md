---
ms.openlocfilehash: 82571d1a0e651f638dec29184f0ecdc88562b3ad
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020979"
---


| Funkce |[Plán Consumption](../articles/azure-functions/functions-scale.md#consumption-plan)|[Plán Premium](../articles/azure-functions/functions-scale.md#premium-plan)|[Vyhrazený plán](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Omezení příchozí IP adresy a přístup k privátnímu webu](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|✅Yes|
|[Integrace virtuální sítě](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌No|✅Ano (místní)|✅Ano (místní a brána)|✅Yes| ✅Yes|
|[Aktivační události virtuální sítě (jiné než HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌No| ✅Yes |✅Yes|✅Yes|✅Yes|
|[Hybridní připojení](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (jenom Windows)|❌No|✅Yes|✅Yes|✅Yes|✅Yes|
|[Omezení odchozích IP adres](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌No| ✅Yes|✅Yes|✅Yes|✅Yes|