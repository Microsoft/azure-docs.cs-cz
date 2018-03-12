| Prostředek | Free | Sdílené (Preview) | Basic | Standard | Premium </th> |
| --- | --- | --- | --- | --- | --- |
| [Webové, mobilní nebo rozhraní API aplikace](https://azure.microsoft.com/services/app-service/) za [plán služby App Service](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)<sup>1</sup> |10 |100 |Unlimited<sup>2</sup> |Unlimited<sup>2</sup> |Unlimited<sup>2</sup> |
| [Aplikace logiky](https://azure.microsoft.com/services/app-service/logic/) za [plán služby App Service](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)</a><sup>1</sup> |10 |10 |10 |20 za jádra |20 za jádra |
| [Plán služby App Service](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) |1 podle oblastí |10 skupinu prostředků |100 skupinu prostředků |100 skupinu prostředků |100 skupinu prostředků |
| Výpočetní typ instance |Shared |Shared |Vyhrazené<sup>3</sup> |Vyhrazené<sup>3</sup> |Vyhrazené<sup>3</sup></p> |
| [Škálováním na více systémů](../articles/app-service/web-sites-scale.md) (maximálního počtu instancí) |sdílené 1 |sdílené 1 |3 vyhrazené<sup>3</sup> |10 vyhrazené<sup>3</sup> |20 vyhrazené (50 v App Service Environment)<sup>3,4</sup> |
| Úložiště<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |500 GB<sup>4,5</sup></p> |
| Doba využití procesoru (5 minut)<sup>6</sup> |3 minuty |3 minuty |Neomezená, platíte na standardní [sazby](https://azure.microsoft.com/pricing/details/app-service/)</a> |Neomezený, platím na sazby úrovně standard |Neomezený, platím na sazby úrovně standard |
| Doba využití procesoru (den)<sup>6</sup> |60 minut |240 minut |Neomezená, platíte na standardní [sazby](https://azure.microsoft.com/pricing/details/app-service/)</a> |Neomezený, platím na sazby úrovně standard |Neomezený, platím na sazby úrovně standard |
| Paměť (1 hodina) |1 024 MB za plán služby App Service |1 024 MB za aplikace |neuvedeno |neuvedeno |neuvedeno |
| Šířka pásma |165 MB |Neomezená, [rychlosti přenosu dat](https://azure.microsoft.com/pricing/details/data-transfers/) použít |Neomezený přenos dat, které platí sazby |Neomezený přenos dat, které platí sazby |Neomezený přenos dat, které platí sazby |
| Architektura aplikace |32 bitů |32 bitů |32-bit nebo 64bitová verze |32-bit nebo 64bitová verze |32-bit nebo 64bitová verze |
| Webové sokety na instanci<sup>7</sup> |5 |35 |350 |Unlimited |Unlimited |
| Souběžné [ladicího programu připojení](../articles/app-service/web-sites-dotnet-troubleshoot-visual-studio.md) na aplikaci. |1 |1 |1 |5 |5 |
| [azurewebsites.net subdomain with FTP/S and SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |X |X |X |X |X |
| [Vlastní domény](../articles/app-service/app-service-web-tutorial-custom-domain.md) podporu | |X |X |X |X |
| Vlastní domény [podpora protokolu SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) | | |Neomezený počet připojení SNI SSL |Neomezená SNI SSL a 1 připojení IP SSL zahrnuté |Neomezená SNI SSL a 1 připojení IP SSL zahrnuté |
| Integrovaný nástroj Load Balancer | |X |X |X |X |
| [Always On](../articles/app-service/web-sites-configure.md) | | |X |X |X |
| [Naplánovaných záloh](../articles/app-service/web-sites-backup.md) | | | | Plánované zálohování každé 2 hodiny, max 12 zálohy za den (ruční + naplánovaných) | Naplánovaných záloh každou hodinu, maximálně 50 zálohy za den (ruční + naplánovaných) |
| [Automatické škálování](../articles/app-service/web-sites-scale.md) | | | |X |X |
| [WebJobs](../articles/app-service/web-sites-create-web-jobs.md)<sup>8</sup> |X |X |X |X |X |
| [Azure Scheduler](https://azure.microsoft.com/services/scheduler/) podporu | |X |X |X |X |
| [Monitorování koncových bodů](../articles/app-service/web-sites-monitor.md) | | |X |X |X |
| [Přípravných slotů](../articles/app-service/web-sites-staged-publishing.md) | | | |5 |20 |
| Vlastní domény jednotlivé aplikace</a> | |500 |500 |500 |500 |
| SLA | |<p> |99,9 % |99.95%<sup>10</sup> |99.95%<sup>9</sup> |

<sup>1</sup>aplikace a kvóty úložišť jsou za plán služby App Service, pokud není uvedeno jinak.  
<sup>2</sup>skutečný počet aplikací, které je možné hostovat na tyto počítače závisí na aktivitu aplikace, velikost instance počítačů a odpovídající využití prostředků.  
<sup>3</sup>vyhrazené instance může mít různou velikost. V tématu [App Service – ceny](https://azure.microsoft.com/pricing/details/app-service/) další podrobnosti.  
<sup>4</sup>premium úroveň umožňuje až 50 vypočítá instancí (přičemž podléhá dostupnosti) a 500 GB místa na disku při použití prostředí App Service a 20 jinak výpočetní instance a 250 GB úložiště.  
<sup>5</sup>limitu úložiště je celková velikost obsahu mezi všechny aplikace ve stejném plán služby App Service. Další možnosti úložiště jsou k dispozici v [App Service Environment](../articles/app-service/environment/app-service-web-configure-an-app-service-environment.md#storage)  
<sup>6</sup>tyto prostředky jsou omezeny fyzické prostředky v rámci vyhrazenou instancí (velikost instance a číslo instance).  
<sup>7</sup>je škálovat aplikaci v základní vrstvě na dvě instance, kdy máte 350 souběžných připojení pro každou z obou instancí.  
<sup>8</sup>spouštět vlastní spustitelné soubory nebo skripty na vyžádání, podle plánu, nebo instance nepřetržitě jako úlohy na pozadí v rámci vaší služby App Service. U nepřetržitého provádění úloh WebJobs se vyžaduje stálé připojení. Pro plánované úlohy WebJobs se vyžaduje Azure Scheduler na úrovni Free nebo Standard. Neexistuje žádné předdefinované omezení počtu WebJobs, která se může spustit v instanci služby App Service, ale neexistují praktické omezení, které závisí na kódu aplikace pokouší o provedení.   
<sup>9</sup>SLA 99,95 % zadaná pro nasazení, které používají více instancí Azure Traffic Manager nakonfigurován pro převzetí služeb při selhání.  

