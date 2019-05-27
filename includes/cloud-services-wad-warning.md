---
author: jpconnock
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.author: jeconnoc
ms.openlocfilehash: f23d5246401f23694755e63a51c52da5dbf5ac1b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66125390"
---
> [!WARNING]
> Když povolíte diagnostiku pro existující role, jsou při nasazení balíčku zakázat všechna rozšíření, které jste už nastavili. Mezi ně patří:
>
> * Microsoft Monitoring Agent diagnostiky
> * Monitorování zabezpečení Microsoft Azure
> * Microsoft Antimalware                 
> * Microsoft Monitoring Agent
> * Agent služby Microsoft Profiler      
> * Windows Azure Domain Extension        
> * Rozšíření diagnostiky Azure Windows   
> * Windows Azure rozšíření vzdálené plochy
> * Windows Azure Log Collector
>
> Poté, co nasadíte aktualizovanou roli, můžete resetovat vaše rozšíření prostřednictvím webu Azure portal nebo Powershellu.
>
