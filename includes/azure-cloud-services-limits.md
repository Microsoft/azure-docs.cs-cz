---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: acf5a7e8d7feebaac3d82629c5b561a22112de0f
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553413"
---
| Prostředek | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| [Jedno nasazení webové nebo pracovní role](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Vstupní koncové body instance](https://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint) jedno nasazení |25 |25 |
| [Vstupní koncové body](https://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint) jedno nasazení |25 |25 |
| [Vnitřní koncové body](https://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint) jedno nasazení |25 |25 |
| [Hostovaná služba certifikáty](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) jedno nasazení |199 |199 |

<sup>1</sup>každé cloudové služby Azure s webovým nebo pracovním rolím může mít dvě nasazení, jeden pro produkční prostředí a jeden pro přípravu. Toto omezení se odkazuje na počet různých rolí, tedy konfigurace. Tento limit neodkazuje na počet instancí na roli, to znamená, škálování.

