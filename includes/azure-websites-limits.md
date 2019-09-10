---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 08/09/2019
ms.author: jroth
ms.openlocfilehash: 1080e3488483610af0f0c9afadaf000895021821
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "69657752"
---
| Resource | Zdarma | Sdílené | Basic | Standard | Premium (v2) | Isolated </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Webové, mobilní nebo API aplikace](https://azure.microsoft.com/services/app-service/) na [plán](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> pro Azure App Service |10 |100 |Neomezeno<sup>2</sup> |Neomezeno<sup>2</sup> |Neomezeno<sup>2</sup> |Neomezeno<sup>2</sup>|
| [Plán služby App Service](../articles/app-service/overview-hosting-plans.md) |10 na oblast |10 na skupinu prostředků |100 na skupinu prostředků |100 na skupinu prostředků |100 na skupinu prostředků |100 na skupinu prostředků|
| Typ instance COMPUTE |Sdílené |Sdílené |Vyhrazené<sup>3</sup> |Vyhrazené<sup>3</sup> |Vyhrazené<sup>3</sup></p> |Vyhrazené<sup>3</sup>|
| [Horizontální](../articles/app-service/manage-scale-up.md) navýšení kapacity (maximální počet instancí) |1 sdílená |1 sdílená |3 vyhrazené<sup></sup> 3 |10 vyhrazených<sup>3</sup> |20 vyhrazených<sup>3</sup>|100 vyhrazené<sup>4</sup>|
| Úložiště<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| Čas procesoru (5 minut)<sup>6</sup> |3 minuty |3 minuty |Neomezené, placené za standardní [sazby](https://azure.microsoft.com/pricing/details/app-service/)</a> |Neomezené, placené za standardní [sazby](https://azure.microsoft.com/pricing/details/app-service/)</a> |Neomezené, placené za standardní [sazby](https://azure.microsoft.com/pricing/details/app-service/)</a> |Neomezené, placené za standardní [sazby](https://azure.microsoft.com/pricing/details/app-service/)</a>|
| Čas procesoru (den)<sup>6</sup> |60 minut |240 minut |Neomezené, placené za standardní [sazby](https://azure.microsoft.com/pricing/details/app-service/)</a> |Neomezené, placené za standardní [sazby](https://azure.microsoft.com/pricing/details/app-service/)</a> |Neomezené, placené za standardní [sazby](https://azure.microsoft.com/pricing/details/app-service/)</a> |Neomezené, placené za standardní [sazby](https://azure.microsoft.com/pricing/details/app-service/)</a> |
| Paměť (1 hodina) |1 024 MB na plán App Service |1 024 MB na aplikaci |Není k dispozici |Není k dispozici |Není k dispozici |Není k dispozici |
| Šířka pásma |165 MB |Neomezeno, platí [sazby za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/) |Neomezeno, platí [sazby za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/) |Neomezeno, platí [sazby za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/) |Neomezeno, platí [sazby za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/) |Neomezeno, platí [sazby za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/) |
| Architektura aplikace |32 – bit |32 – bit |32bitový/64bitový (64 bitů) |32bitový/64bitový (64 bitů) |32bitový/64bitový (64 bitů) |32bitový/64bitový (64 bitů) |
| Webové sokety na instanci<sup>7</sup> |5 |35 |350 |Unlimited |Unlimited |Unlimited |
| Souběžná [připojení ladicího programu](../articles/app-service/troubleshoot-dotnet-visual-studio.md) na aplikaci |1 |1 |1 |5 |5 |5 |
| App Service certifikátů na předplatné<sup>10</sup>| Nepodporuje se | Nepodporuje se |10 |10 |10 |10 |
| Vlastní domény na aplikaci</a> |0 (pouze subdomény azurewebsites.net)|500 |500 |500 |500 |500 |
| [Podpora protokolu SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) vlastní domény |Nepodporováno, ve výchozím nastavení je k dispozici certifikát Wildcard pro *. azurewebsites.net.|Nepodporováno, ve výchozím nastavení je k dispozici certifikát Wildcard pro *. azurewebsites.net.|Neomezené SNI SSL připojení |Zahrnuté neomezené SNI SSL a 1 IP SSL připojení |Zahrnuté neomezené SNI SSL a 1 IP SSL připojení | Zahrnuté neomezené SNI SSL a 1 IP SSL připojení|
| Integrovaný nástroj pro vyrovnávání zatížení | |X |X |X |X |X<sup>9</sup> |
| [Vždy zapnuto](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Naplánovaná zálohování](../articles/app-service/manage-backup.md) | | | | Naplánované zálohy každé 2 hodiny, maximálně 12 záloh za den (ruční + naplánované) | Naplánované zálohy každou hodinu, maximálně 50 zálohování za den (ruční + plánovaná) | Naplánované zálohy každou hodinu, maximálně 50 zálohování za den (ruční + plánovaná) |
| [Automatické škálování](../articles/app-service/manage-scale-up.md) | | | |X |X |X |
| Webové [úlohy](../articles/app-service/webjobs-create.md) <sup>8</sup> |X |X |X |X |X |X |
| Podpora [Azure Scheduleru](https://azure.microsoft.com/services/scheduler/) | |X |X |X |X |X |
| [Monitorování koncových bodů](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [Pracovní sloty](../articles/app-service/deploy-staging-slots.md) | | | |5 |20 |20 |
| SLA | |  |99,95 %|99,95 %|99,95 %|99,95 %|  

<sup>1</sup> Pokud není uvedeno jinak, jsou aplikace a kvóty úložiště podle App Serviceho plánu.  
<sup>2</sup> . Skutečný počet aplikací, které můžete hostovat na těchto počítačích, závisí na aktivitách aplikací, velikosti instancí počítačů a na odpovídajícím využití prostředků.  
<sup>3</sup> . Vyhrazené instance mohou mít různé velikosti. Další informace najdete v tématu [App Service ceny](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> . Další jsou povolená na vyžádání.  
<sup>5</sup> . Limit úložiště je celková velikost obsahu napříč všemi aplikacemi ve stejném plánu App Service.  
<sup>6</sup> Tyto prostředky jsou omezené na fyzických prostředcích na vyhrazených instancích (velikost instance a počet instancí).  
<sup>7</sup> Při škálování aplikace na úrovni Basic na dvě instance máte 350 souběžných připojení pro každou z těchto dvou instancí. Pro úroveň Standard a vyšší nejsou k dispozici žádná teoretická omezení pro webové sokety, ale jiné faktory můžou omezit počet webových soketů. Například maximální počet souběžných požadavků, které `maxConcurrentRequestsPerCpu`jsou povoleny (definované), jsou: 7 500 za malý virtuální počítač, 15 000 na jeden virtuální počítač (7 500 × 2 jádra) a 75 000 na velký virtuální počítač (18 750 × 4 jádra).  
<sup>8</sup> Spouštějte vlastní spustitelné soubory nebo skripty na vyžádání, podle plánu nebo nepřetržitě jako úlohu na pozadí v rámci instance App Service. U nepřetržitého provádění úloh WebJobs se vyžaduje stálé připojení. Pro plánované úlohy WebJobs se vyžaduje Azure Scheduler na úrovni Free nebo Standard. Není k dispozici žádný předdefinovaný limit počtu webových úloh, které mohou být spuštěny v instanci App Service. Existují praktická omezení, která závisí na tom, co se snaží kód aplikace provést.  
<sup>9</sup> Služba App Service v izolovaném prostředí SKU se dá pomocí Azure Load Balancer interně vyrovnávat zatížení (interního nástroje), takže není k dispozici žádné veřejné připojení z Internetu. Následkem toho se některé služby ILB App Service v izolovaném prostředí musí používat v počítačích, které mají přímé připojení ke koncovému bodu sítě ILB.  
<sup>10</sup> Limit kvóty App Service Certificate pro každé předplatné se dá zvýšit prostřednictvím žádosti o podporu na maximální limit 200.  