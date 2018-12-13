---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: eb1fe7f83ed83efe078be0aadf26cc7db6f498e2
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52886283"
---
| Prostředek | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| [Web/role pracovního procesu za nasazení](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Vstupní koncové body instance](https://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint) jedno nasazení |25 |25 |
| [Vstupní koncové body](https://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint) jedno nasazení |25 |25 |
| [Vnitřní koncové body](https://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint) jedno nasazení |25 |25 |
| [Hostovaná služba certifikáty](../articles/cloud-services/cloud-services-certs-create.md#what-are-service-certificates) jedno nasazení |199 |199 |

<sup>1</sup>každé cloudové služby pomocí Web/role pracovního procesu může mít dvě nasazení, jeden pro produkční prostředí a jeden pro přípravu. Všimněte si také, že tento limit odkazuje na počet různých rolí (konfigurace), nikoli číslo instance podle role (škálování).

