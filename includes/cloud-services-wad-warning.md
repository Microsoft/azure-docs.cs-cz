---
author: jpconnock
ms.service: cloud-services
ms.topic: include
ms.date: 11/25/2018
ms.author: jeconnoc
ms.openlocfilehash: f23d5246401f23694755e63a51c52da5dbf5ac1b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66125390"
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
