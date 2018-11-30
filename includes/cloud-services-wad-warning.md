---
author: jpconnock
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.author: jeconnoc
ms.openlocfilehash: f23d5246401f23694755e63a51c52da5dbf5ac1b
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52330878"
---
> [!WARNING]
> Když povolíte diagnostiku pro existující role, jsou při nasazení balíčku zakázat všechna rozšíření, které jste už nastavili. Mezi ně patří:
>
> * Microsoft Monitoring Agent diagnostiky
> * Monitorování zabezpečení Microsoft Azure
> * Antimalware od Microsoftu                 
> * Microsoft Monitoring Agent
> * Agent služby Microsoft Profiler      
> * Rozšíření Azure domény Windows        
> * Rozšíření diagnostiky Azure Windows   
> * Windows Azure rozšíření vzdálené plochy
> * Windows Azure Log Collector
>
> Poté, co nasadíte aktualizovanou roli, můžete resetovat vaše rozšíření prostřednictvím webu Azure portal nebo Powershellu.
>
