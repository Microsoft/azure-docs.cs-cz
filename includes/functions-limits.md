---
author: ggailey777
ms.service: billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: aa43fe6c7999b8fa73419916870f9dfca0c01e3d
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68601309"
---
| Resource | [Plán spotřeby](../articles/azure-functions/functions-scale.md#consumption-plan) | [Plán Premium](../articles/azure-functions/functions-scale.md#premium-plan) | [Plán App Service](../articles/azure-functions/functions-scale.md#app-service-plan) <sup>1</sup> |
| --- | --- | --- | --- |
| Horizontální navýšení kapacity | Řízená událostmi | Řízená událostmi | [Ruční nebo automatické škálování](../articles/app-service/web-sites-scale.md) | 
| Maximální počet instancí | 200 | 20 | 10-20 |
|Výchozí doba [Trvání vyprší](../articles/azure-functions/functions-scale.md#timeout) (min.) |5 | 30 |30<sup>2</sup> |
|Maximální [Doba trvání vypršela](../articles/azure-functions/functions-scale.md#timeout) (min.) |10 | Unbounded | neohraničené<sup>3</sup> |
| Maximální počet odchozích připojení (na instanci) | 600 aktivní (celkem 1200) | Unbounded | Unbounded |
| Maximální velikost požadavku (MB)<sup>4</sup> | 100 | 100 | 100 |
| Maximální délka řetězce dotazu<sup>4</sup> | 4096 | 4096 | 4096 |
| Maximální délka adresy URL žádosti<sup>4</sup> | 8192 | 8192 | 8192 |
| [ACU](../articles/virtual-machines/windows/acu.md) na instanci | 100 | 210-840 | 100-840 |
| Maximální velikost paměti (GB na instanci) | 1.5 | 3,5 – 14 | 1,75 – 14 |
| Aplikace Function App na plán |100 |100 |neohraničené<sup>5</sup> |
| [Plány služby App Service](../articles/app-service/overview-hosting-plans.md) | 100 na [oblast](https://azure.microsoft.com/global-infrastructure/regions/) |100 na skupinu prostředků |100 na skupinu prostředků |
| Úložiště<sup>6</sup> |1 GB |250 GB |50-1000 GB |
| Vlastní domény na aplikaci</a> |500<sup>7</sup> |500 |500 |
| [Podpora protokolu SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) vlastní domény |zahrnuto do nevázaného SNI SSL připojení | neohraničená SNI SSL a jsou zahrnutá 1 IP SSL připojení. |neohraničená SNI SSL a jsou zahrnutá 1 IP SSL připojení. | 

<sup>1</sup> Pokud chcete určit omezení pro různé možnosti plánu App Service, přečtěte si [omezení App Service plánu](../articles/azure-subscription-service-limits.md#app-service-limits).  
<sup>2</sup> ve výchozím nastavení je časový limit pro modul runtime Functions 1. x v plánu App Service neohraničený.  
<sup>3</sup> vyžaduje, aby byl plán App Service nastavený na [vždycky zapnutý](../articles/azure-functions/functions-scale.md#always-on). Platíte za standardní [sazby](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> tato omezení se nastavují na [hostiteli](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>5</sup> skutečný počet aplikací Function App, které můžete hostovat, závisí na aktivitách aplikací, velikosti instancí počítačů a na odpovídajícím využití prostředků.  
<sup>6</sup> limit úložiště je celková velikost obsahu v dočasném úložišti napříč všemi aplikacemi ve stejném plánu App Service. Plán spotřeby používá soubory Azure pro dočasné úložiště.  
<sup>7</sup> Pokud je vaše aplikace Function hostovaná v [plánu spotřeby](../articles/azure-functions/functions-scale.md#consumption-plan), podporuje se jenom možnost CNAME. Pro aplikace Function App v [plánu Premium](../articles/azure-functions/functions-scale.md#premium-plan) nebo v [plánu App Service](../articles/azure-functions/functions-scale.md#app-service-plan)můžete namapovat vlastní doménu pomocí záznamu CNAME nebo a.
