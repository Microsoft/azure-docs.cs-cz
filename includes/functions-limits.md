---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: 37a61e270f95ffdc420536046e0ce1eda1e489b4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85391470"
---
| Prostředek |[Plán Consumption](../articles/azure-functions/functions-scale.md#consumption-plan)|[Plán Premium](../articles/azure-functions/functions-scale.md#premium-plan)|[Vyhrazený plán](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/aks/quotas-skus-regions.md) |
| --- | --- | --- | --- | --- | --- |
|Výchozí [Doba trvání časového limitu](../articles/azure-functions/functions-scale.md#timeout) (min.) |5 | 30 |30<sup>1</sup> | 30 | 30 |
|Maximální [Doba trvání časového limitu](../articles/azure-functions/functions-scale.md#timeout) (min.) |10 | neohraničené<sup>7</sup> | neohraničené<sup>2</sup> | Unbounded | Unbounded |
| Maximální počet odchozích připojení (na instanci) | 600 aktivní (celkem 1200) | Unbounded | Unbounded | Unbounded | Unbounded |
| Maximální velikost požadavku (MB)<sup>3</sup> | 100 | 100 | 100 | 100 | Závisí na clusteru |
| Maximální délka řetězce dotazu<sup>3</sup> | 4 096 | 4 096 | 4 096 | 4 096 | Závisí na clusteru |
| Maximální délka adresy URL požadavku<sup>3</sup> | 8192 | 8192 | 8192 | 8192 | Závisí na clusteru |
|[ACU](../articles/virtual-machines/windows/acu.md) na instanci | 100 | 210-840 | 100-840 | 210-250<sup>8</sup> | [Ceny AKS](https://azure.microsoft.com/pricing/details/container-service/) |
| Maximální velikost paměti (GB na instanci) | 1.5 | 3,5 – 14 | 1,75 – 14 | 3,5 – 14 | Podporuje se libovolný uzel. |
| Aplikace Function App na plán |100 |100 |neohraničené<sup>4</sup> | Unbounded | Unbounded |
| [Plány služby App Service](../articles/app-service/overview-hosting-plans.md) | 100 na [oblast](https://azure.microsoft.com/global-infrastructure/regions/) |100 na skupinu prostředků |100 na skupinu prostředků | - | - |
| Úložiště<sup>5</sup> |5 TB |250 GB |50-1000 GB | 1 TB | neuvedeno |
| Vlastní domény na aplikaci</a> |500<sup>6</sup> |500 |500 | 500 | neuvedeno |
| [Podpora protokolu SSL](../articles/app-service/configure-ssl-bindings.md) vlastní domény |zahrnuto do nevázaného SNI SSL připojení | neohraničená SNI SSL a jsou zahrnutá 1 IP SSL připojení. |neohraničená SNI SSL a jsou zahrnutá 1 IP SSL připojení. | neohraničená SNI SSL a jsou zahrnutá 1 IP SSL připojení. | neuvedeno |

<sup>1</sup> ve výchozím nastavení je časový limit pro modul runtime Functions 1. x v plánu App Service neohraničený.  
<sup>2</sup> vyžaduje, aby byl plán App Service nastavený na [vždycky zapnutý](../articles/azure-functions/functions-scale.md#always-on). Platíte za standardní [sazby](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>3</sup> tato omezení se [nastavují na hostiteli](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>4</sup> skutečný počet aplikací Function App, které můžete hostovat, závisí na aktivitách aplikací, velikosti instancí počítačů a na odpovídajícím využití prostředků.  
<sup>5</sup> limit úložiště je celková velikost obsahu v dočasném úložišti napříč všemi aplikacemi ve stejném plánu App Service. Plán spotřeby používá soubory Azure pro dočasné úložiště.  
<sup>6</sup> Pokud je vaše aplikace Function hostovaná v [plánu spotřeby](../articles/azure-functions/functions-scale.md#consumption-plan), podporuje se jenom možnost CNAME. Pro aplikace Function App v [plánu Premium](../articles/azure-functions/functions-scale.md#premium-plan) nebo v [plánu App Service](../articles/azure-functions/functions-scale.md#app-service-plan)můžete namapovat vlastní doménu pomocí záznamu CNAME nebo a.  
<sup>7</sup> garantuje až 60 minut.  
<sup>8</sup> pracovních procesů jsou role, které hostují zákaznické aplikace. Pracovní procesy jsou dostupné ve třech pevných velikostech: One vCPU/3,5 GB RAM; Dva vCPU/7 GB paměti RAM; Čtyři vCPU/14 GB paměti RAM.
