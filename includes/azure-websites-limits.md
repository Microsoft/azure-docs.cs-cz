---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/04/2020
ms.author: jroth
ms.openlocfilehash: 505e2d8eec20853fba3743b40cbe289585d14d61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78304997"
---
| Prostředek | Free | Sdílená | Basic | Standard | Prémiová (v2) | Izolovaný </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Webové, mobilní nebo API aplikace](https://azure.microsoft.com/services/app-service/) podle plánu [služby Azure App Service](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Neomezený<sup>2</sup> |Neomezený<sup>2</sup> |Neomezený<sup>2</sup> |Neomezený<sup>2</sup>|
| [Plán služby App Service](../articles/app-service/overview-hosting-plans.md) |10 na region |10 pro skupinu prostředků |100 na skupinu prostředků |100 na skupinu prostředků |100 na skupinu prostředků |100 na skupinu prostředků|
| Typ instance výpočtu |Sdílená |Sdílená |Věnováno<sup>3</sup> |Věnováno<sup>3</sup> |Věnováno<sup>3</sup></p> |Věnováno<sup>3</sup>|
| [Horizontální navýšení kapacity](../articles/app-service/manage-scale-up.md) (maximální počet instancí) |1 sdíleno |1 sdíleno |3 vyhrazené<sup>3</sup> |10 věnováno<sup>3</sup> |30 věnováno<sup>3</sup>|100 věnováno<sup>4</sup>|
| Skladování<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TTB<sup>5</sup>|
| Čas procesoru (5 minut)<sup>6</sup> |3 minuty |3 minuty |Neomezené, platit za standardní [ceny](https://azure.microsoft.com/pricing/details/app-service/)</a> |Neomezené, platit za standardní [ceny](https://azure.microsoft.com/pricing/details/app-service/)</a> |Neomezené, platit za standardní [ceny](https://azure.microsoft.com/pricing/details/app-service/)</a> |Neomezené, platit za standardní [ceny](https://azure.microsoft.com/pricing/details/app-service/)</a>|
| Čas procesoru (den)<sup>6</sup> |60 minut |240 minut |Neomezené, platit za standardní [ceny](https://azure.microsoft.com/pricing/details/app-service/)</a> |Neomezené, platit za standardní [ceny](https://azure.microsoft.com/pricing/details/app-service/)</a> |Neomezené, platit za standardní [ceny](https://azure.microsoft.com/pricing/details/app-service/)</a> |Neomezené, platit za standardní [ceny](https://azure.microsoft.com/pricing/details/app-service/)</a> |
| Paměť (1 hodina) |1 024 MB na plán služby aplikace |1 024 MB na aplikaci |Není dostupné. |Není dostupné. |Není dostupné. |Není dostupné. |
| Šířka pásma |165 MB |Neomezené, [sazby za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/) platí |Neomezené, [sazby za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/) platí |Neomezené, [sazby za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/) platí |Neomezené, [sazby za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/) platí |Neomezené, [sazby za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/) platí |
| Architektura aplikace |32bitová |32bitová |32bitový/64bitový |32bitový/64bitový |32bitový/64bitový |32bitový/64bitový |
| Webové sokety na instanci<sup>7</sup> |5 |35 |350 |Unlimited |Unlimited |Unlimited |
| Připojení IP | 600 | 600 | Závisí na velikosti instance<sup>8</sup> | Závisí na velikosti instance<sup>8</sup> | Závisí na velikosti instance<sup>8</sup> | 16 000 |
| Souběžná [připojení ladicího programu](../articles/app-service/troubleshoot-dotnet-visual-studio.md) pro aplikaci |1 |1 |1 |5 |5 |5 |
| Certifikáty služby App Service na předplatné<sup>9</sup>| Nepodporuje se | Nepodporuje se |10 |10 |10 |10 |
| Vlastní domény pro aplikaci</a> |0 (pouze azurewebsites.net subdoména)|500 |500 |500 |500 |500 |
| Podpora [ssl](../articles/app-service/configure-ssl-certificate.md) vlastní domény |Není podporováno, certifikát se zástupnými kódy pro *.azurewebsites.net k dispozici ve výchozím nastavení|Není podporováno, certifikát se zástupnými kódy pro *.azurewebsites.net k dispozici ve výchozím nastavení|Neomezené připojení SNI SSL |Včetně neomezeného ssl ssl a 1 IP SSL připojení |Včetně neomezeného ssl ssl a 1 IP SSL připojení | Včetně neomezeného ssl ssl a 1 IP SSL připojení|
| Hybridní připojení podle plánu | | | 5 | 25 | 200 | 200 |
| Integrovaný systém vyrovnávání zatížení | |× |× |× |× |X<sup>10</sup> |
| [Vždy zapnuto](../articles/app-service/configure-common.md) | | |× |× |× |× |
| [Plánované zálohy](../articles/app-service/manage-backup.md) | | | | Plánované zálohování každé 2 hodiny, maximálně 12 záloh denně (ruční + plánované) | Plánované zálohování každou hodinu, maximálně 50 záloh za den (ruční + plánované) | Plánované zálohování každou hodinu, maximálně 50 záloh za den (ruční + plánované) |
| [Automatické škálování](../articles/app-service/manage-scale-up.md) | | | |× |× |× |
| [Webové úlohy](../articles/app-service/webjobs-create.md)<sup>11</sup> |× |× |× |× |× |× |
| [Monitorování koncových bodů](../articles/app-service/web-sites-monitor.md) | | |× |× |× |× |
| [Pracovní sloty](../articles/app-service/deploy-staging-slots.md) pro aplikaci| | | |5 |20 |20 |
| SLA | |  |99,95 %|99,95 %|99,95 %|99,95 %|  

<sup>1.</sup> Aplikace a kvóty úložiště se na plán služby App Service používají, pokud není uvedeno jinak.  
<sup>2.</sup> Skutečný počet aplikací, které můžete hostovat na těchto počítačích, závisí na aktivitě aplikací, velikosti instancí počítače a odpovídajícím využití prostředků.  
<sup>3.</sup> Vyhrazené instance mohou mít různé velikosti. Další informace najdete v tématu [App Service pricing](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4.</sup> Další jsou povoleny na vyžádání.  
<sup>5.</sup> Limit úložiště je celková velikost obsahu ve všech aplikacích ve stejném plánu služby App. Celková velikost obsahu všech aplikací ve všech plánech služby App service v jedné skupině prostředků a oblasti nesmí překročit 500 GB.  
<sup>6.</sup> Tyto prostředky jsou omezeny fyzickými prostředky na vyhrazené instance (velikost instance a počet instancí).  
<sup>7.</sup> Pokud změníte velikost aplikace na úrovni Basic na dvě instance, máte 350 souběžných připojení pro každou ze dvou instancí. Pro úroveň Standard a vyšší neexistují žádná teoretická omezení webových soketů, ale další faktory mohou omezit počet webových soketů. Například maximální souběžné požadavky povolené `maxConcurrentRequestsPerCpu`(definované ) jsou: 7 500 na malý virtuální virtuální virtuální proud, 15 000 na střední virtuální virtuální virtuální m (7 500 x 2 jádra) a 75 000 na velký virtuální virtuální virtuální m (18 750 x 4 jádra).  
<sup>8.</sup> Maximální ip připojení jsou na instanci a závisí na velikosti instance: 1 920 na b1/S1/P1V2 instance, 3 968 na B2/S2/P2V2 instance, 8 064 na B3/S3/P3V2 instance.  
<sup>9.</sup> Limit kvóty certifikátu služby App Service na jedno předplatné lze zvýšit prostřednictvím žádosti o podporu na maximální limit 200.  
<sup>10.</sup> Izolované sloky služby App Service lze interně vyvážit zatížení (ILB) pomocí Azure Load Balancer, takže neexistuje žádné veřejné připojení z internetu. V důsledku toho musí být některé funkce izolované služby ILB app service používány z počítačů, které mají přímý přístup ke koncovému bodu sítě ILB.  
<sup>11.</sup> Spouštějte vlastní spustitelné soubory nebo skripty na vyžádání, podle plánu nebo průběžně jako úlohu na pozadí v rámci instance služby App Service. Vždy zapnuto je vyžadováno pro průběžné provádění webových úloh. Neexistuje žádné předdefinované omezení počtu webových úloh, které lze spustit v instanci služby App Service. Existují praktická omezení, která závisí na tom, co se pokouší provést kód aplikace.  
