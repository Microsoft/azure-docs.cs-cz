---
ms.openlocfilehash: 2e92d150851c74a84f785d1f5f0ebe2e5870a54e
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936752"
---


| Funkce |[Plán Consumption](../articles/azure-functions/consumption-plan.md)|[Plán Premium](../articles/azure-functions/functions-premium-plan.md)|[Vyhrazený plán](../articles/azure-functions/dedicated-plan.md)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Omezení příchozí IP adresy a přístup k privátnímu webu](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅Ano|✅Ano|✅Ano|✅Ano|✅Ano|
|[Integrace virtuální sítě](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌No|✅Ano (místní)|✅Ano (místní a brána)|✅Ano| ✅Ano|
|[Aktivační události virtuální sítě (jiné než HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌No| ✅Ano |✅Ano|✅Ano|✅Ano|
|[Hybridní připojení](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (jenom Windows)|❌No|✅Ano|✅Ano|✅Ano|✅Ano|
|[Omezení odchozích IP adres](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌No| ✅Ano|✅Ano|✅Ano|✅Ano|