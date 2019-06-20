---
author: jpconnock
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.author: jeconnoc
ms.openlocfilehash: f23d5246401f23694755e63a51c52da5dbf5ac1b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175349"
---
> [!WARNING]
> Když povolíte diagnostiku pro existující role, jsou při nasazení balíčku zakázat všechna rozšíření, které jste už nastavili. Mezi ně patří:
>
> * Microsoft Monitoring Agent diagnostiky
> * Monitorování zabezpečení Microsoft Azure
> * Antimalware od Microsoftu                 
> * Microsoft Monitoring Agent
> * Agent služby Microsoft Profiler      
> * Windows Azure Domain Extension        
> * Rozšíření diagnostiky Azure Windows   
> * Windows Azure rozšíření vzdálené plochy
> * Windows Azure Log Collector
>
> Poté, co nasadíte aktualizovanou roli, můžete resetovat vaše rozšíření prostřednictvím webu Azure portal nebo Powershellu.
>
