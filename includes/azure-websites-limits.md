| Prostředek | Free | Sdílené (Preview) | Basic | Standard | Premium </th> |
| --- | --- | --- | --- | --- | --- |
| [Webové, mobilní telefon nebo API apps](https://azure.microsoft.com/services/app-service/) za [plán služby App Service](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)<sup>1</sup> |10 |100 |Neomezený počet<sup>2</sup> |Neomezený počet<sup>2</sup> |Neomezený počet<sup>2</sup> |
| [Aplikace logiky](https://azure.microsoft.com/services/app-service/logic/) za [plán služby App Service](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)</a><sup>1</sup> |10 |10 |10 |20 podle počtu jader |20 podle počtu jader |
| [Plán služby App Service](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) |1 na oblast |10 skupinu prostředků |100 skupinu prostředků |100 skupinu prostředků |100 skupinu prostředků |
| Výpočetní instance typu |Shared |Shared |Vyhrazené<sup>3</sup> |Vyhrazené<sup>3</sup> |Vyhrazené<sup>3</sup></p> |
| [Horizontální navýšení kapacity](../articles/app-service/web-sites-scale.md) (maximální počet instancí) |sdílené 1 |sdílené 1 |3 vyhrazené<sup>3</sup> |10 vyhrazené<sup>3</sup> |20 vyhrazených (50 ve službě ASE)<sup>3,4</sup> |
| Úložiště<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |500 GB<sup>4,5</sup></p> |
| Čas procesoru (5 minut)<sup>6</sup> |3 minuty |3 minuty |Neomezený počet, platit standardních [sazby](https://azure.microsoft.com/pricing/details/app-service/)</a> |Neomezená, platit za standardní sazby |Neomezená, platit za standardní sazby |
| Čas procesoru (den)<sup>6</sup> |60 minut |240 minut |Neomezený počet, platit standardních [sazby](https://azure.microsoft.com/pricing/details/app-service/)</a> |Neomezená, platit za standardní sazby |Neomezená, platit za standardní sazby |
| Paměť (1 hodina) |1 024 MB za plán služby App Service |1 024 MB na aplikaci |neuvedeno |neuvedeno |neuvedeno |
| Šířka pásma |165 MB |Neomezený počet, [sazby za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/) použít |Neomezené přenosy dat, které pak účtují sazby za |Neomezené přenosy dat, které pak účtují sazby za |Neomezené přenosy dat, které pak účtují sazby za |
| Architektura aplikace |32 bitů |32 bitů |32-bit/64-bit |32-bit/64-bit |32-bit/64-bit |
| Webové sokety každou instanci<sup>7</sup> |5 |35 |350 |Unlimited |Unlimited |
| Souběžné [připojení ladicího programu](../articles/app-service/web-sites-dotnet-troubleshoot-visual-studio.md) na aplikaci. |1 |1 |1 |5 |5 |
| [subdoména azurewebsites.NET s FTP/S a SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |X |X |X |X |X |
| [Vlastní domény](../articles/app-service/app-service-web-tutorial-custom-domain.md) podpory | |X |X |X |X |
| Vlastní domény [podpora protokolu SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) | | |Neomezené možnosti připojení SNI SSL |Neomezený počet SNI SSL a 1 IP SSL. připojení zahrnuté |Neomezený počet SNI SSL a 1 IP SSL. připojení zahrnuté |
| Integrovaný nástroj Load Balancer | |X |X |X |X |
| [Always On](../articles/app-service/web-sites-configure.md) | | |X |X |X |
| [Naplánované zálohování](../articles/app-service/web-sites-backup.md) | | | | Plánované zálohování každé 2 hodiny, maximum z 12 zálohy za den (ruční a plánovaná) | Naplánované zálohování každou hodinu mít délku maximálně 50 zálohy za den (ruční a plánovaná) |
| [Automatické škálování](../articles/app-service/web-sites-scale.md) | | | |X |X |
| [WebJobs](../articles/app-service/web-sites-create-web-jobs.md)<sup>8</sup> |X |X |X |X |X |
| [Azure Scheduler](https://azure.microsoft.com/services/scheduler/) podpory | |X |X |X |X |
| [Monitorování koncových bodů](../articles/app-service/web-sites-monitor.md) | | |X |X |X |
| [Pracovní sloty](../articles/app-service/web-sites-staged-publishing.md) | | | |5 |20 |
| Vlastní domény na aplikaci</a> | |500 |500 |500 |500 |
| SLA | |<p> |99,9 % |99.95%<sup>10</sup> |99.95%<sup>9</sup> |

<sup>1</sup>aplikací a kvót úložiště jsou za plán služby App Service, pokud není uvedeno jinak.  
<sup>2</sup>skutečný počet aplikací, které můžete hostovat na těchto počítačích závisí na aktivity aplikací, velikosti instance počítačů a odpovídajících využití prostředků.  
<sup>3</sup>vyhrazené instance může být různě velká. Zobrazit [App Service – ceny](https://azure.microsoft.com/pricing/details/app-service/) další podrobnosti.  
<sup>4</sup>úroveň premium umožňuje až 50 výpočetních instancí (závisí na dostupnosti) a 500 GB místa na disku při využívání prostředí App Service a 20 výpočetních instancí a 250 GB úložiště jinak.  
<sup>5</sup>limitu úložiště je celková velikost obsahu mezi všemi aplikacemi v rámci stejného plánu služby App Service. Více možností úložiště jsou k dispozici v [služby App Service Environment](../articles/app-service/environment/app-service-web-configure-an-app-service-environment.md#storage)  
<sup>6</sup>tyto prostředky jsou omezeny fyzické prostředky na vyhrazených instancích (velikost instance a počtu instancí).  
<sup>7</sup>škálovat aplikace na úrovni Basic na dvě instance, kdy máte 350 souběžných připojení pro každou z obou instancí.  
<sup>8</sup>spouštět vlastní spustitelné soubory nebo skripty na požádání, podle plánu nebo nepřetržitě jako úlohu na pozadí v rámci vaší služby App Service instance. U nepřetržitého provádění úloh WebJobs se vyžaduje stálé připojení. Pro plánované úlohy WebJobs se vyžaduje Azure Scheduler na úrovni Free nebo Standard. Neexistuje žádné předdefinované omezení počtu webových úloh, které můžete spustit v instanci služby App Service, ale existují praktickým limitům, které jsou závislé na co se kód aplikace snaží udělat.   
<sup>9</sup>smlouva SLA na úrovni 99,95 % pro nasazení, která používají víc instancí s Azure Traffic Managerem nakonfigurovaným pro převzetí služeb při selhání k dispozici.  

