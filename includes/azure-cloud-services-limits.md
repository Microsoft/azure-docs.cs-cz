---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0b24688b502a40e722d2fcc4436ff1824862f489
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "67175381"
---
| Resource | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| [Webové role nebo role pracovního procesu na nasazení](../articles/cloud-services/cloud-services-choose-me.md) <sup>1</sup> |25 |25 |
| [Vstupní koncové body instance](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) na nasazení |25 |25 |
| [Vstupní koncové body](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) na nasazení |25 |25 |
| [Interní koncové body](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) na nasazení |25 |25 |
| [Certifikáty hostované služby](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) na nasazení |199 |199 |

<sup>1</sup> Každá cloudová služba Azure s webovými nebo pracovními rolemi může mít dvě nasazení, jednu pro produkční prostředí a jednu pro přípravu. Toto omezení odkazuje na počet jedinečných rolí, tj. na konfigurace. Toto omezení neodkazuje na počet instancí na roli, tj. na škálování.

