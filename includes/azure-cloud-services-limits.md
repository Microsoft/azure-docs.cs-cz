---
author: rothja
ms.service: cloud-services
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 390460cf6a22c164ee41a9b6679c7b7f2979d8ec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85838978"
---
| Prostředek | Omezení |
| --- | --- |
| [Webové role nebo role pracovního procesu na nasazení](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |
| [Vstupní koncové body instance](/previous-versions/azure/reference/gg557552(v=azure.100)#instanceinputendpoint) na nasazení |25 |
| [Vstupní koncové body](/previous-versions/azure/reference/gg557552(v=azure.100)#inputendpoint) na nasazení |25 |
| [Interní koncové body](/previous-versions/azure/reference/gg557552(v=azure.100)#internalendpoint) na nasazení |25 |
| [Certifikáty hostované služby](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) na nasazení |199 |

<sup>1</sup> Každá cloudová služba Azure s webovými nebo pracovními rolemi může mít dvě nasazení, jednu pro produkční prostředí a jednu pro přípravu. Toto omezení odkazuje na počet jedinečných rolí, tj. na konfigurace. Toto omezení neodkazuje na počet instancí na roli, tj. na škálování.

