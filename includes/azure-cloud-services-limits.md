---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0b24688b502a40e722d2fcc4436ff1824862f489
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175381"
---
| Resource | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| [Jedno nasazení webové nebo pracovní role](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Vstupní koncové body instance](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) jedno nasazení |25 |25 |
| [Vstupní koncové body](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) jedno nasazení |25 |25 |
| [Vnitřní koncové body](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) jedno nasazení |25 |25 |
| [Hostovaná služba certifikáty](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) jedno nasazení |199 |199 |

<sup>1</sup>každé cloudové služby Azure s webovým nebo pracovním rolím může mít dvě nasazení, jeden pro produkční prostředí a jeden pro přípravu. Toto omezení se odkazuje na počet různých rolí, tedy konfigurace. Tento limit neodkazuje na počet instancí na roli, to znamená, škálování.

