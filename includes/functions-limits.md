---
author: ggailey777
ms.service: cost-management-billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: 4643bb7f95e4fd1249d3ab6699c1f835c77f18fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77198339"
---
| Prostředek | [Plán Consumption](../articles/azure-functions/functions-scale.md#consumption-plan) | [Plán Premium](../articles/azure-functions/functions-scale.md#premium-plan) | [Plán služby App Service](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| Horizontální navýšení kapacity | Řízení událostmi | Řízení událostmi | [Ruční/automatické škálování](../articles/app-service/manage-scale-up.md) | 
| Maximální počet instancí | 200 | 100 | 10-20 |
|Výchozí [doba trvání časového limitu](../articles/azure-functions/functions-scale.md#timeout) (min) |5 | 30 |30<sup>2.</sup> |
|Maximální [doba trvání časového limitu](../articles/azure-functions/functions-scale.md#timeout) (min) |10 | bez meze<sup>8</sup> | bez meze<sup>3</sup> |
| Maximální počet odchozích připojení (pro instanci) | 600 aktivních (1200 celkem) | Neomezeně | Neomezeně |
| Maximální velikost požadavku (MB)<sup>4</sup> | 100 | 100 | 100 |
| Maximální délka řetězce dotazu<sup>4</sup> | 4 096 | 4 096 | 4 096 |
| Maximální délka URL požadavku<sup>4</sup> | 8192 | 8192 | 8192 |
| [ACU](../articles/virtual-machines/windows/acu.md) na instanci | 100 | 210-840 | 100-840 |
| Maximální kapacita paměti (GB na instanci) | 1,5 | 3.5-14 | 1.75-14 |
| Aplikace pro funkce podle plánu |100 |100 |bez meze<sup>5</sup> |
| [Plány služby App Service](../articles/app-service/overview-hosting-plans.md) | 100 na [region](https://azure.microsoft.com/global-infrastructure/regions/) |100 na skupinu prostředků |100 na skupinu prostředků |
| Skladování<sup>6</sup> |1 GB |250 GB |50-1000 GB |
| Vlastní domény pro aplikaci</a> |500<sup>7</sup> |500 |500 |
| Podpora [ssl](../articles/app-service/configure-ssl-bindings.md) vlastní domény |Bez ohraničeného připojení SSL SSL s neohraničeným | bezohraničené ssl sni a 1 IP SSL připojení v ceně |bezohraničené ssl sni a 1 IP SSL připojení v ceně | 

<sup>1</sup> Konkrétní limity pro různé možnosti plánu služby App Service najdete v tématu [omezení plánu služby App Service](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits).  
<sup>2</sup> Ve výchozím nastavení je časový limit pro běhový čas Functions 1.x v plánu služby App Service neomezený.  
<sup>3</sup> Vyžaduje, aby byl plán služby App Service nastaven na [vždy zapnutý](../articles/azure-functions/functions-scale.md#always-on). Plaťte za standardní [sazby](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> Tyto limity jsou [stanoveny v hostiteli](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>5</sup> Skutečný počet aplikací funkcí, které můžete hostovat, závisí na aktivitě aplikací, velikosti instancí počítače a odpovídajícím využití prostředků.  
<sup>6</sup> Limit úložiště je celková velikost obsahu v dočasném úložišti ve všech aplikacích ve stejném plánu služby App Service. Plán spotřeby používá soubory Azure pro dočasné úložiště.  
<sup>7</sup> Pokud je vaše aplikace funkcí hostována v [plánu spotřeby](../articles/azure-functions/functions-scale.md#consumption-plan), je podporována pouze možnost CNAME. U aplikací s funkcemi v [plánu Premium](../articles/azure-functions/functions-scale.md#premium-plan) nebo v [plánu služby App Service](../articles/azure-functions/functions-scale.md#app-service-plan)můžete namapovat vlastní doménu pomocí záznamu CNAME nebo A.  
<sup>8</sup> Garantuje me až 60 minut.