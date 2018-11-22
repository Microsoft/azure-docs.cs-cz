---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2b6ba1dcddd435c42ad864b8e87175d0e98c9b3a
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279542"
---
| Prostředek | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| [Web/role pracovního procesu za nasazení](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [Vstupní koncové body instance](https://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint) jedno nasazení |25 |25 |
| [Vstupní koncové body](https://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint) jedno nasazení |25 |25 |
| [Vnitřní koncové body](https://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint) jedno nasazení |25 |25 |

<sup>1</sup>každé cloudové služby pomocí Web/role pracovního procesu může mít dvě nasazení, jeden pro produkční prostředí a jeden pro přípravu. Všimněte si také, že tento limit odkazuje na počet různých rolí (konfigurace), nikoli číslo instance podle role (škálování).

