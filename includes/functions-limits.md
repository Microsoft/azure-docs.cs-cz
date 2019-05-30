---
author: ggailey777
ms.service: billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: 8f30d9fb2fcfe8f55af13d7726aa8458f8733b3f
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236015"
---
| Resource | [Plán consumption](../articles/azure-functions/functions-scale.md#consumption-plan) | [Plán Premium](../articles/azure-functions/functions-scale.md#premium-plan-public-preview) | [Plán služby App Service](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| Horizontální navýšení kapacity | Řízené událostmi | Řízené událostmi | [Ruční nebo automatické škálování](../articles/app-service/web-sites-scale.md) | 
|Výchozí [doba trvání časového limitu](../articles/azure-functions/functions-scale.md#timeout) (min) |5 | 30 |30<sup>2</sup> |
|Maximální počet [doba trvání časového limitu](../articles/azure-functions/functions-scale.md#timeout) (min) |10 | bez vazby | bez vazby<sup>3</sup> |
| Maximální počet odchozích připojení (za instanci) | 600 aktivní (celkem 1 200) | bez vazby | bez vazby |
| Žádost o maximální velikost (MB)<sup>4</sup> | 100 | 100 | 100 |
| Maximální délka řetězce dotazu<sup>4</sup> | 4 096 | 4 096 | 4 096 |
| Maximální délka adresy URL požadavku<sup>4</sup> | 8192 | 8192 | 8192 |
| [ACU](../articles/virtual-machines/windows/acu.md) na instanci | 100 | 210-840 | 100-840 |
| Maximální velikost paměti (v GB na instanci) | 1,5 | 3.5-14 | 1.75-14 |
| Aplikace Function App podle plánu |100 |100 |unbounded<sup>5</sup> |
| [Plány služby App Service](../articles/app-service/overview-hosting-plans.md) | za 100 [oblasti](https://azure.microsoft.com/global-infrastructure/regions/) |100 skupinu prostředků |100 skupinu prostředků |
| Úložiště<sup>6</sup> |1 GB |250 GB |50-1000 GB |
| Vlastní domény na aplikaci</a> |500<sup>7</sup> |500 |500 |
| Vlastní domény [podpora protokolu SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |Není podporováno, pro certifikát se zástupným znakem *. azurewebsites.net k dispozici ve výchozím nastavení| bez vazby SSL pro SNI a 1 IP SSL. připojení zahrnuté |bez vazby SSL pro SNI a 1 IP SSL. připojení zahrnuté | 

<sup>1</sup>konkrétní omezení pro různé možnosti plán služby App Service najdete v tématu [omezení plánu služby App Service](../articles/azure-subscription-service-limits.md#app-service-limits).  
<sup>2</sup>výchozí časový limit pro modul runtime verze 1.x funkcí v plánu služby App Service je bez vazby.  
<sup>3</sup>vyžaduje plán služby App Service se nastavenou na [Always On](../articles/azure-functions/functions-scale.md#always-on). Platit standardních [sazby](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> tato omezení jsou [nastavit na hostiteli](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>5</sup> závisí skutečný počet aplikací function App, které můžete hostovat na aktivity aplikací, velikosti instance počítačů a odpovídajících využití prostředků.   
<sup>6</sup>limitu úložiště je celková velikost obsahu v dočasné úložiště mezi všemi aplikacemi v rámci stejného plánu služby App Service. Plán consumption soubory Azure používá jako dočasné úložiště.  
<sup>7</sup>při je vaše aplikace funkcí hostované v [plánu Consumption](../articles/azure-functions/functions-scale.md#consumption-plan), se podporuje jenom možnost CNAME. Pro aplikace function App v [plán Premium](../articles/azure-functions/functions-scale.md#premium-plan-public-preview) nebo [plán služby App Service](../articles/azure-functions/functions-scale.md#app-service-plan), můžete namapovat vlastní doménu pomocí záznamu A nebo záznam CNAME. 
