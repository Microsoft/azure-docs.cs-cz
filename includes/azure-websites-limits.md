---
author: rothja
ms.service: app-service
ms.topic: include
ms.date: 03/17/2020
ms.author: msangapu
ms.openlocfilehash: dad7799cb5a7579b28847e3968b6b38f1f98298a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327444"
---
| Prostředek | Free | Shared | Basic | Standard | Premium (v1-V3) | Isolated </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Webové, mobilní nebo API aplikace](https://azure.microsoft.com/services/app-service/) na [plán](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> pro Azure App Service |10 |100 |Neomezeno<sup>2</sup> |Neomezeno<sup>2</sup> |Neomezeno<sup>2</sup> |Neomezeno<sup>2</sup>|
| [Plán App Service](../articles/app-service/overview-hosting-plans.md) |10 na oblast |10 na skupinu prostředků |100 na skupinu prostředků |100 na skupinu prostředků |100 na skupinu prostředků |100 na skupinu prostředků|
| Typ instance COMPUTE |Shared |Shared |Vyhrazené<sup>3</sup> |Vyhrazené<sup>3</sup> |Vyhrazené<sup>3</sup></p> |Vyhrazené<sup>3</sup>|
| [Horizontální](../articles/app-service/manage-scale-up.md) navýšení kapacity (maximální počet instancí) |1 sdílená |1 sdílená |<sup>3 vyhrazené 3</sup> |10 vyhrazených<sup>3</sup> | 20 vyhrazeno pro V1 a v2; 30 vyhrazeno pro v3. <sup>3</sup> .|100 vyhrazené<sup>4</sup>|
| Úložiště<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup> |1 TB<sup>5</sup> <br/><br/> Dostupná kvóta úložiště je 999 GB. |
| Čas procesoru (5 minut)<sup>6</sup> |3 minuty |3 minuty |Neomezené, placené za standardní [sazby](https://azure.microsoft.com/pricing/details/app-service/)</a> |Neomezené, placené za standardní [sazby](https://azure.microsoft.com/pricing/details/app-service/)</a> |Neomezené, placené za standardní [sazby](https://azure.microsoft.com/pricing/details/app-service/)</a> |Neomezené, placené za standardní [sazby](https://azure.microsoft.com/pricing/details/app-service/)</a>|
| Čas procesoru (den)<sup>6</sup> |60 minut |240 minut |Neomezené, placené za standardní [sazby](https://azure.microsoft.com/pricing/details/app-service/)</a> |Neomezené, placené za standardní [sazby](https://azure.microsoft.com/pricing/details/app-service/)</a> |Neomezené, placené za standardní [sazby](https://azure.microsoft.com/pricing/details/app-service/)</a> |Neomezené, placené za standardní [sazby](https://azure.microsoft.com/pricing/details/app-service/)</a> |
| Paměť (1 hodina) |1 024 MB na plán App Service |1 024 MB na aplikaci |N/A |N/A |N/A |N/A |
| Šířka pásma |165 MB |Neomezeno, platí [sazby za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/) |Neomezeno, platí [sazby za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/) |Neomezeno, platí [sazby za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/) |Neomezeno, platí [sazby za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/) |Neomezeno, platí [sazby za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/) |
| Architektura aplikace |32bitová |32bitová |32bitový/64bitový (64 bitů) |32bitový/64bitový (64 bitů) |32bitový/64bitový (64 bitů) |32bitový/64bitový (64 bitů) |
| Webové sokety na instanci<sup>7</sup> |5 |35 |350 |Unlimited |Unlimited |Unlimited |
| Odchozí IP připojení na instanci | 600 | 600 | Závisí na velikosti instance<sup>8</sup> | Závisí na velikosti instance<sup>8</sup> | Závisí na velikosti instance<sup>8</sup> | 16 000 |
| Souběžná [připojení ladicího programu](../articles/app-service/troubleshoot-dotnet-visual-studio.md) na aplikaci |1 |1 |1 |5 |5 |5 |
| App Service certifikátů na předplatné<sup>9</sup>| Nepodporováno | Nepodporováno |10 |10 |10 |10 |
| Vlastní domény na aplikaci</a> |0 (pouze subdomény azurewebsites.net)|500 |500 |500 |500 |500 |
| [Podpora protokolu SSL](../articles/app-service/configure-ssl-certificate.md) vlastní domény |Nepodporováno, \* ve výchozím nastavení je k dispozici certifikát Wildcard pro. azurewebsites.NET|Nepodporováno, \* ve výchozím nastavení je k dispozici certifikát Wildcard pro. azurewebsites.NET|Neomezené SNI SSL připojení |Zahrnuté neomezené SNI SSL a 1 IP SSL připojení |Zahrnuté neomezené SNI SSL a 1 IP SSL připojení | Zahrnuté neomezené SNI SSL a 1 IP SSL připojení|
| Hybridní připojení | | | 5 podle plánu | 25 na plán | 200 na aplikaci | 200 na aplikaci |
| [Integrace virtuální sítě](../articles/app-service/web-sites-integrate-with-vnet.md) | | |   |  × |  ×  |  ×  |
| [Soukromé koncové body](../articles/app-service/networking/private-endpoint.md) | | |   |   |  100 na aplikaci  |    |
| Integrovaný nástroj pro vyrovnávání zatížení | |× |× |× |× |X<sup>10</sup> |
| [Omezení přístupu](../articles/app-service/networking-features.md#access-restrictions) | pravidla 512 na aplikaci | pravidla 512 na aplikaci | pravidla 512 na aplikaci | pravidla 512 na aplikaci | pravidla 512 na aplikaci | pravidla 512 na aplikaci |
| [Vždy zapnuto](../articles/app-service/configure-common.md) | | |× |× |× |× |
| [Naplánovaná zálohování](../articles/app-service/manage-backup.md) | | | | Naplánované zálohy každé 2 hodiny, maximálně 12 záloh za den (ruční + naplánované) | Naplánované zálohy každou hodinu, maximálně 50 zálohování za den (ruční + plánovaná) | Naplánované zálohy každou hodinu, maximálně 50 zálohování za den (ruční + plánovaná) |
| [Automatické škálování](../articles/app-service/manage-scale-up.md) | | | |× |× |× |
| Webové [úlohy](../articles/app-service/webjobs-create.md)<sup>11</sup> |× |× |× |× |× |× |
| [Monitorování koncového bodu](../articles/app-service/web-sites-monitor.md) | | |× |× |× |× |
| [Pracovní sloty](../articles/app-service/deploy-staging-slots.md) na aplikaci| | | |5 |20 |20 |
| [Testování v produkčním prostředí](../articles/app-service/deploy-staging-slots.md#route-traffic)| | | |× |× |× |
| [Diagnostické protokoly](../articles/app-service/troubleshoot-diagnostic-logs.md) | × | × | × | × | × | × |
| Kudu | × | × | × | × | × | × |
| [Ověřování a autorizace](../articles/app-service/overview-authentication-authorization.md) | × | × | × | × | × | × |
| [App Service spravované certifikáty (Public Preview)](https://azure.microsoft.com/updates/secure-your-custom-domains-at-no-cost-with-app-service-managed-certificates-preview/)<sup>12</sup> | |  | × | × | × | × |
| SLA | |  |99,95 %|99,95 %|99,95 %|99,95 %|

<sup>1</sup> aplikace a kvóty úložiště jsou podle App Serviceho plánu, pokud není uvedeno jinak.

<sup>2</sup> skutečný počet aplikací, které můžete hostovat na těchto počítačích, závisí na aktivitách aplikací, velikosti instancí počítačů a na odpovídajícím využití prostředků.

<sup>3</sup> vyhrazené instance můžou mít různé velikosti. Další informace najdete v tématu [App Service ceny](https://azure.microsoft.com/pricing/details/app-service/).

<sup>4</sup> další jsou povolené na vyžádání.

<sup>5</sup> limit úložiště je celková velikost obsahu napříč všemi aplikacemi ve stejném plánu služby App Service. Celková velikost obsahu všech aplikací napříč všemi plány služby App Service v jedné skupině prostředků a oblastí nemůže překročit 500 GB. Kvóta systému souborů pro App Service hostovaných aplikací se určuje podle agregovaných plánů App Service vytvořených v oblasti a skupině prostředků.

<sup>6</sup> tyto prostředky jsou omezené na fyzických prostředcích na vyhrazených instancích (velikost instance a počet instancí).

<sup>7</sup> při škálování aplikace na úrovni Basic na dvě instance máte 350 souběžných připojení pro každou ze dvou instancí. Pro úroveň Standard a vyšší nejsou k dispozici žádná teoretická omezení pro webové sokety, ale jiné faktory můžou omezit počet webových soketů. Například maximální počet souběžných požadavků, které jsou povoleny (definované `maxConcurrentRequestsPerCpu` ), jsou: 7 500 na malý virtuální počítač, 15 000 na střední virtuální počítač (7 500 × 2 jádra) a 75 000 na velký virtuální počítač (18 750 × 4 jádra).

<sup>8</sup> maximální počet připojení IP je na jednu instanci a závisí na velikosti instance: 1 920 na instanci B1/S1/P1V3, 3 968 na instanci B2/S2/P2V3, 8 064 na instanci B3/S3/P3V3.

<sup>9</sup> limit kvóty App Service Certificate na předplatné se dá zvýšit prostřednictvím žádosti o podporu na maximální limit 200.

<sup>10</sup> služba App Service v izolovaném prostředí SKU se dá pomocí Azure Load Balancer interně vyrovnávat zatížení (interního nástroje), takže není k dispozici žádné veřejné připojení z Internetu. V důsledku toho musí být některé funkce interního nástroje izolované App Service používány z počítačů, které mají přímý přístup ke koncovému bodu sítě interního nástroje.

<sup>11</sup> spouštějte vlastní spustitelné soubory a skripty na vyžádání, podle plánu nebo nepřetržitě jako úlohu na pozadí v rámci instance App Service. U nepřetržitého provádění úloh WebJobs se vyžaduje Always On. Není k dispozici žádný předdefinovaný limit počtu webových úloh, které mohou být spuštěny v instanci App Service. Existují praktická omezení, která závisí na tom, co se snaží kód aplikace provést.

<sup>12</sup> holé domény se nepodporují. Pouze vystavení standardních certifikátů (certifikáty se zástupnými znaky nejsou k dispozici) Omezeno pouze na jeden bezplatný certifikát na vlastní doménu.
