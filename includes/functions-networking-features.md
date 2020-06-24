---
ms.openlocfilehash: 8d5c3fff2646536ffe5c593e13ccdab23397d44b
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2020
ms.locfileid: "84906704"
---


| Funkce |[Plán Consumption](../articles/azure-functions/functions-scale.md#consumption-plan)|[Plán Premium](../articles/azure-functions/functions-scale.md#premium-plan)|[Vyhrazený plán](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Omezení příchozí IP adresy a přístup k privátnímu webu](../articles/azure-functions/functions-networking-options.md#inbound-ip-restrictions)|✅Ano|✅Ano|✅Ano|✅Ano|✅Ano|
|[Integrace virtuální sítě](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌Ne|✅Ano (místní)|✅Ano (místní a brána)|✅Ano| ✅Ano|
|[Aktivační události virtuální sítě (jiné než HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌Ne| ✅Ano |✅Ano|✅Ano|✅Ano|
|[Hybridní připojení](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (jenom Windows)|❌Ne|✅Ano|✅Ano|✅Ano|✅Ano|
|[Omezení odchozích IP adres](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌Ne| ✅Ano|✅Ano|✅Ano|✅Ano|