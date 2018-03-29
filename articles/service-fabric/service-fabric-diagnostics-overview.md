---
title: Přehled diagnostiky a Azure Service Fabric monitorování | Microsoft Docs
description: Další informace o monitorování a Diagnostika pro clustery, aplikace a služby Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2018
ms.author: dekapur
ms.openlocfilehash: f784576547f0d85a825ad9dd107c6c84cd261092
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitorovací a diagnostické pro Azure Service Fabric

Tento článek obsahuje přehled nastavení monitorování a Diagnostika pro vaše aplikace spuštěné v prostředí clusterů Service Fabric. Monitorovací a diagnostické jsou důležité k vývoji, testování a nasazení úloh v jakémkoli prostředí cloudu. Monitorování umožňuje sledovat, jak se aplikace používají, vaše využití prostředků a celkový stav clusteru. Tyto informace můžete použít pro diagnostiku a opravte všechny problémy v clusteru a pomoci zabránit problémům v budoucnu. 

Hlavní cíle monitorování a Diagnostika se:
* Najít a diagnostikovat problémy infrastruktury
* Rozpoznat problémy s vaší aplikací
* Pochopení spotřeby prostředků
* Sledování výkonu aplikace, služby a infrastruktury

V clusteru Service Fabric, monitorování a Diagnostika data pocházejí ze tří úrovní: aplikace, platformy (cluster) a infrastrukturu. 
* [Úrovni aplikace](service-fabric-diagnostics-event-generation-app.md) zahrnuje data o výkonu aplikace a všechny další vlastní protokolování, který jste přidali. Data monitorování aplikace by měla skončili Statistika aplikace (AI) z vlastní aplikace. Může jít také prostřednictvím sady SDK AI, EventFlow nebo jiné kanálu podle svého výběru.
* [Platformy úroveň](service-fabric-diagnostics-event-generation-infra.md) zahrnuje události z akcí v clusteru, související se správou clusteru a aplikací v rámci něj spuštěna. Data monitorování platformy mají být odeslány k analýze protokolů OMS, řešení Service Fabric analýzy usnadnění vizualizace příchozí události. Tato data je obvykle odeslaný na účet úložiště prostřednictvím Windows nebo Linux Azure Diagnostics rozšíření, kde je přístupný pomocí analýzy protokolů OMS. 
* Úroveň infrastruktury se zaměřuje na [monitorování výkonu](service-fabric-diagnostics-event-generation-perf.md), vypadající na klíčové metriky a čítače výkonu určit využití prostředků a zatížení. Sledování výkonu můžete dosáhnout pomocí agenta – tak, aby vaše data výkonu bude mít na stejném místě jako platforma události, která umožňuje lepší diagnostiku jako jste korelovat, doporučujeme používat agenta OMS (Microsoft Monitoring) změny mezi clustery. 

![Graf Přehled diagnostiky](media/service-fabric-diagnostics-overview/diagnostics-overview.png)

## <a name="monitoring-scenarios"></a>Scénáře monitorování

Tato část popisuje hlavní scénáře monitorování cluster Service Fabric – aplikace, clusteru, výkon a sledování stavu. Pro každý scénář je popsána záměrné a celková přístup pro monitorování. Další informace o těchto a dalších obecná doporučení monitorování pro prostředky Azure lze nalézt na [osvědčené postupy – monitorování a Diagnostika](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 

Tyto scénáře jsou také volně namapované na tři úrovně data monitorování a Diagnostika, jak je popsáno výše, tj. pro každý scénář správně zpracovávat v clusteru, měli byste mít monitorování a Diagnostika příchozí data na odpovídající úrovni . Stav monitorování scénář je výjimku, protože obsahuje clusteru a všechno spuštěna v ní.

## <a name="application-monitoring"></a>Sledování aplikací
Monitorování aplikací sleduje, jak jsou používány komponenty aplikace, kterou jste vytvořili, a funkce. Chcete monitorovat aplikací k problémům se, že dopad, který uživatelé jsou zachyceny. Monitorování aplikace mohou být užitečné při:
* určení správné zatížení aplikacemi a uživatel přenosy dat – potřebujete škálovat vaše služby, aby splnily požadavky na uživatele nebo IP adresa potenciální problémová místa ve vaší aplikaci?
* Identifikace problémy s komunikace služby a vzdálenou komunikaci mezi clusteru
* nad tím, co uživatelé dělají s vaší aplikací – instrumentace aplikací můžou pomoct Průvodce budoucí funkce vývoj a lepší diagnostiku pro chyby aplikace

Sledování na úrovni aplikace v Service Fabric, doporučujeme použít Application Insights (AI). AI na integraci s Service Fabric zahrnuje tooling prostředí pro Visual Studio a portálu Azure a pochopení kontext služby Service Fabric a vzdálenou komunikaci v AI řídicí panel a mapy aplikace, což komplexní protokolování se na pole prostředí. I když mnoho protokoly jsou automaticky vytvořen a shromážděné pro vás při použití AI, doporučujeme přidat další vlastní protokolování pro vaše aplikace a mít, který zobrazí v AI spolu s co je k dispozici pro vytvoření bohatší možnosti diagnostiky pro zpracování problémy v budoucnu. Viz informace o AI pomocí Service Fabric v [analýza události s Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md). 

Chcete-li začít s instrumentace váš kód ke sledování aplikace, přejděte na [aplikace úrovni událostí a protokolu generování](service-fabric-diagnostics-event-generation-app.md). 

### <a name="monitoring-application-availability"></a>Monitorování dostupnosti aplikace
Je možné, že všechno, co je v clusteru se zdá být spuštěná podle očekávání a je vytváření sestav, jako v pořádku, ale aplikace zdá se, že nelze získat přístup nebo je nedostupný. I když nejsou k dispozici mnoha případech, kdy tomu by mohlo dojít, je důležité vědět, v takovém případě mohli ho chcete zmírnit co nejdříve. Monitorování dostupnosti je široce problémem sledování dostupnosti součástí vašeho systému porozumíte celkovým "smlouva" systému. V clusteru zaměříme na dostupnost z hlediska vaší aplikace – platforma funguje zajistit, že životního cyklu aplikace scénářů správy nezpůsobí výpadku. Nicméně různé problémy v clusteru může mít za následek provozu vaší aplikace, které mají vliv, a v těchto případech jako vlastníka aplikace, obvykle budete chtít vědět, hned. Doporučujeme vám, která spolu s všechny jiné aplikace, můžete nasadit sledovací zařízení v clusteru. Účel sledovací zařízení by mohla být Zkontrolujte jednotlivé koncové body pro vaši aplikaci v časovém intervalu sady a sestavy zpět, že jsou přístupné. Můžete to provést taky pomocí externí služby, který vysílá signál ping koncový bod a vrací sestavy v daném časovém intervalu.

## <a name="cluster-monitoring"></a>Monitorování clusteru
Monitorování cluster Service Fabric je důležité zajistit, že platformy a všechny úlohy spuštěné na něm běží tak, jak má. Jedním z cílů Service Fabric je udržovat aplikacím prostřednictvím selhání hardwaru. Toho je dosaženo pomocí služeb systému platformy možnost ke zjišťování problémů s infrastrukturou a rychle převzetí služeb při selhání úlohy na jiné uzly v clusteru. Ale v tomto případě, co v případě systému služeb sami mají problémy? Nebo pokud při pokusu o přesunutí zatížení, jsou pravidla pro umístění služby došlo k porušení? Monitorování clusteru umožňuje udržení informovanosti o aktivity probíhající v clusteru, což pomáhá s diagnostikou problémy a jejich oprava efektivně. Některé klíče, který má být hledání věci:
* Způsob, jakým očekáváte, z hlediska umístění vašich aplikací a vyrovnávání práce mimo cluster chová Service Fabric? 
* Jsou akce převáděna ke změně konfigurace clusteru je potvrzené a reagovali na podle očekávání? To je obzvláště důležité, když škálování clusteru.
* Service Fabric zpracovává vaše data a vaše služba služba komunikace uvnitř clusteru správně?

Service Fabric nabízí komplexní sadu událostí předinstalované prostřednictvím kanálů Data & zasílání zpráv a funkčnosti. V systému Windows, ty jsou ve formě jednoho zprostředkovatele trasování událostí pro Windows se sadou relevantní `logLevelKeywordFilters` umožňuje výběr mezi různé kanály. V systému Linux všechny události platformy pocházet prostřednictvím LTTng a jsou vloženy do jedné tabulky, ze kterých mohou být filtrovány podle potřeby. 

Tyto kanály obsahovat kurátorované, strukturovaných události, které lze použít pro lepší pochopení stav clusteru. "Diagnostika" je povolena ve výchozím nastavení v okamžiku vytvoření clusteru, který nastavili jste s tabulkou aplikace Azure Storage níž jsou odesílány události z těchto kanálů pro vás k dotazování v budoucnu. Si můžete přečíst informace o monitorování clusteru na [platformy úrovně událostí a protokolu generování](service-fabric-diagnostics-event-generation-infra.md). Doporučujeme použít OMS analýzy protokolů pro monitorování vašeho clusteru. Analýzy protokolů OMS nabízí specifického řešení Service Fabric, analýzy prostředků infrastruktury služby, které poskytuje vlastní řídicí panel pro monitorování clusterů Service Fabric a umožňuje dotazování události vašeho clusteru a nastavit výstrahy. Další informace o to v [analýza události s OMS](service-fabric-diagnostics-event-analysis-oms.md). 

### <a name="watchdogs"></a>Watchdogs
Obecně platí sledovací zařízení je samostatný služba, která může sledovat stav a zatížení v rámci služeb, ping koncových bodů a stavu sestavy pro všechno, co je v clusteru. To může pomoct zabránit chybám, které by se na základě zobrazení jedné služby. Watchdogs jsou také vhodné místo pro hostitele kód, který provede nápravné akce bez zásahu uživatele (například čištění souborů protokolů v úložiště v určitých časových intervalech). Můžete najít implementaci služby sledovací zařízení ukázka [zde](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="performance-monitoring"></a>Sledování výkonu
Monitorování infrastruktury základní je klíčovou součástí pochopení stav clusteru a vaše využití prostředků. Měření výkonu systému, závisí na několika faktorech, z nichž každý se většinou měří prostřednictvím klíčových ukazatelů výkonu (KPI). Service Fabric relevantní klíčových ukazatelů výkonu lze mapovat na metriky, které mohou být shromažďovány z uzlů v clusteru, jako čítače výkonu.
Těchto klíčových ukazatelů výkonu můžou pomoct se:
* informace o využití prostředků a zatížení - za účelem škálování clusteru nebo optimalizací procesů služby
* predikci problémů s infrastrukturou - mnohé problémy se sebou nečekané změny (vyřazuje) ve výkonu, jako jsou například sítě využití vstupně-výstupních operací a procesoru, můžete použít klíčové ukazatele výkonu pro predikci a diagnostice problémů infrastruktury

Seznam čítačů výkonu, které by měly být shromažďovány na úrovni infrastruktury najdete na [metriky výkonu](service-fabric-diagnostics-event-generation-perf.md). 

Pro sledování úrovně výkonu aplikace Service Fabric poskytuje sadu čítačů výkonu pro programovací modely Reliable Services a aktéři. Pokud používáte některou z těchto modelů, tyto čítače výkonu pro klíčové ukazatele výkonu, které pomáhají zajistit, že jsou vaše aktéři otáčí nahoru a dolů správně nebo že žádostí o služby spolehlivé dochází ke zpracování dostatečně rychle. V tématu [monitorování pro vzdálenou komunikaci spolehlivá služba](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) a [monitorování výkonu Reliable actors](service-fabric-reliable-actors-diagnostics.md#performance-counters) Další informace o těchto. Kromě toho Application Insights také obsahuje sadu metrik výkonu, které se budou shromažďovat, pokud je nakonfigurovaná s vaší aplikací.

Shromažďování čítačů výkonu odpovídající pomocí agenta OMS a zobrazení těchto klíčových ukazatelů výkonu v OMS analýzy protokolů. Můžete také použít rozšíření agenta Azure Diagnostics (nebo jiné podobné agenta) shromáždit a uložit metrik pro analýzu. 

## <a name="health-monitoring"></a>Monitorování stavu
Platforma Service Fabric obsahuje stav modelu, který poskytuje rozšiřitelný vykazování stavu pro stav entity v clusteru. Každý uzel, aplikace, služby, oddíl, repliky nebo instance, má průběžně aktualizovat stav. Stav může být buď "OK", "Upozornění" nebo "Chyba". Stav se změní prostřednictvím sestav stavu, které jsou vygenerované pro každou entitu podle problémy v clusteru. Stav entity můžete kdykoli v Service Fabric Explorer (SFX) kontrolovat, jak je uvedeno níže, nebo může být dotazován prostřednictvím rozhraní API stavu na platformách. Můžete také přizpůsobit sestavy o stavu a upravit stav entity přidáním vlastních sestav stavu nebo pomocí rozhraní API služby stavu. Další informace o stavu modelu najdete na [Úvod do monitorování stavu Service Fabric](service-fabric-health-introduction.md).

![Řídicí panel stavu SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)

Kromě zobrazení nejnovější sestavy stavu v SFX, je také jednotlivých sestav k dispozici jako událost. Stav události se můžou shromažďovat prostřednictvím provozní kanálu (najdete v části [agregace událostí pomocí diagnostiky Azure](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)) a jsou uložené v OMS analýzy protokolů pro výstrahy a dotazování v budoucnu. To pomáhá rozpoznat problémy, které můžou mít vliv dostupnosti vaší aplikace, takže doporučujeme, abyste nastavili výstrahy pro příslušné selhání scénáře (vlastní výstrahy prostřednictvím OMS).

## <a name="monitoring-workflow"></a>Monitorování pracovního postupu 

Celkové pracovní postup monitorování a Diagnostika zahrnuje tři kroky:

1. **Generování událostí**: Jedná se o událostí (protokoly, trasování, vlastní události) na úrovni infrastruktury, platformy (cluster) a aplikace
2. **Agregace událostí**: Tato fáze je efektivní kanálů pro vaše události končit nástroj, kde je můžete analyzovat, který zahrnuje rozšíření diagnostiky Azure nebo EventFlow
3. **Analýza**: události musí být přístupné v nástroji, aby pro analýzu - vizualizace, dotazování, výstrahy, atd.

Více produkty jsou k dispozici, které zahrnují tyto tři oblasti, a můžete vybrat různé technologie pro každou. Je důležité se ujistit, že různých částí spolupracovat a přinést řešení monitorování v začátku do konce pro váš cluster.

## <a name="event-generation"></a>Generování událostí

Prvním krokem při monitorování a Diagnostika pracovního postupu je k nastavení vytváření a generování dat událostí a protokolu. Tyto události, protokoly a čítače výkonu jsou vygenerované na všech třech úrovních: úrovni aplikace (všechny instrumentace přidat do aplikace a služby, které jsou nasazené na clusteru), platformy (události vygenerované z clusteru na základě operace Service Fabric), a úroveň infrastruktury (metriky výkonu z každého uzlu). Diagnostická data, která se shromažďují na všech těchto úrovních je přizpůsobitelné, když Service Fabric povolit některé výchozí instrumentace. 

Další informace o [úroveň události platformy](service-fabric-diagnostics-event-generation-infra.md) a [události na úrovni aplikace](service-fabric-diagnostics-event-generation-app.md) pochopit, co je k dispozici a jak přidat další instrumentace. Hlavní rozhodnutí, které je nutné provést v tomto poli je, jak chcete instrumentace vaší aplikace. Pro aplikace .NET doporučujeme používat objektu ILogger, ale můžete také zkoumat EventSource, Serilog a další podobné knihovny. Pro jazyk Java doporučujeme používat Log4j. Kromě toho existuje několik dalších možností k dispozici využitelných v závislosti na povaze aplikace. Nebojte se prozkoumat co by být vhodné pro váš případ konkrétní použití, nebo vyberte takový, že umíte nejvíce pomocí. 

Po rozhodování o protokolování zprostředkovatele byste chtěli použít, musíte zajistit, bude vaše protokoly agregovat a uložené správně.

## <a name="event-aggregation"></a>Agregace událostí

Pro shromažďování protokolů a událostí generovaných aplikací a cluster, obvykle doporučujeme používat [rozšíření Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) (podobně jako více do kolekce založené na agentovi protokolu) nebo [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) (v rámci procesu protokolu kolekce). Pokud používáte Application Insights a vývoji v rozhraní .NET nebo Java, pak pomocí Application Insights SDK místo EventFlow je vhodné.

Když je možné podobné úlohy provádějí s pomocí přesně jednoho z těchto ve většině případů kombinování agenta rozšíření Azure Diagnostics se kanál v rámci procesu kolekce (AI SDK nebo EventFlow) vede k větší spolehlivost, komplexní, monitorování pracovního postupu . Agenta rozšíření Azure Diagnostics bude vaše cestu pro platformu úroveň události, i když pro svoje protokoly úrovně aplikace pomocí AI SDK nebo EventFlow (v rámci procesu shromažďování). 

V případě, že chcete použít pouze jednu z těchto tady jsou některé klíčové body třeba vzít v úvahu.
* Shromažďování protokolů aplikací pomocí rozšíření Azure Diagnostics je vhodný pro služby Service Fabric, pokud je sada protokolu zdroje a cíle se nemění často a přehledné mapování mezi zdroji a cíli. Z důvodu pro toto je konfigurace Azure Diagnostics se odehrává na vrstvě Resource Manager, takže provedení významných změn konfigurace vyžaduje aktualizaci celý cluster. To znamená, že často končí až se míň efektivní než přejdete sadou AI SDK nebo EventFlow.
* Pomocí EventFlow umožňuje mít služby odeslat protokoly přímo na platformu pro analýzu a vizualizace nebo do úložiště. K tomuto účelu lze také použít další knihovny (objektu ILogger, Serilog atd.), ale EventFlow má výhodu s byly navrženy speciálně pro kolekce v procesu protokolu a k podpoře služby Service Fabric. To má několik výhod z hlediska usnadnění konfigurace a nasazení a nabízí větší flexibilitu pro podporu různých protokolování knihovny a nástrojů pro analýzu. 

## <a name="event-analysis"></a>Analýza událostí

Existuje několik skvělé platforem, které existují na trhu, pokud jde o analýzy a vizualizace dat monitorování a diagnostiky. Jsou dva, které doporučujeme [OMS](service-fabric-diagnostics-event-analysis-oms.md) a [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) z důvodu jejich integraci s Service Fabric. Taky by měl vypadat do [elastické zásobníku](https://www.elastic.co/products) (obzvláště pokud uvažujete o spuštění clusteru v režimu offline), [Splunk](https://www.splunk.com/), nebo jakoukoli jinou platformu vaši volbu. 

Klíčové body pro libovolnou platformu, že si vyberete by měla obsahovat jak pohodlné jste s uživatelským rozhraním a dotazování umožňuje vizualizovat data a vytvářet snadno čitelné řídicí panely a dalších nástrojů poskytuje k vylepšení monitorování, možnosti například automatizované výstrahy.

Kromě platformou si zvolíte možnost při nastavování cluster Service Fabric jako prostředek služby Azure, můžete také získat přístup k Azure se na pole monitorování výkonu pro vaše počítače.

### <a name="azure-monitor"></a>Azure Monitor

Můžete použít [Azure monitorování](../monitoring-and-diagnostics/monitoring-overview.md) monitorovat mnoho prostředků Azure, na kterých je vytvořen cluster Service Fabric. Sadu metriky pro [škálovací sadu virtuálních počítačů](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) a jednotlivých [virtuální počítače](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines) se automaticky shromažďovat a zobrazí na portálu Azure. Chcete-li zobrazit shromážděných informací na portálu Azure, vyberte skupinu prostředků, která obsahuje clusteru Service Fabric. Pak vyberte škálovací sadu virtuálních počítačů, které chcete zobrazit. V **monitorování** část (v levém navigačního), vyberte **metriky** k zobrazení grafu hodnot.

![Zobrazení portálu Azure shromažďovat informace o metrikách](media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

Chcete-li přizpůsobit grafy, postupujte podle pokynů v [metriky v Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md). Také můžete vytvořit oznámení založené na tyto metriky, jak je popsáno v [vytvoření výstrahy v Azure monitorování pro služby Azure](../monitoring-and-diagnostics/monitoring-overview-alerts.md). 

## <a name="next-steps"></a>Další postup

* Další informace o monitorování platformy a události Service Fabric nabízí pro vás na [úrovni událostí a protokolu generování platformy](service-fabric-diagnostics-event-generation-infra.md)
* Začínáme s instrumentace aplikací, najdete v části [úrovně událostí a protokolu generování aplikace](service-fabric-diagnostics-event-generation-app.md)
* Projděte postup pro nastavení AI pro vaši aplikaci s [monitorování a diagnostikovat aplikaci ASP.NET Core v Service Fabric](service-fabric-tutorial-monitoring-aspnet.md)
* Zjistěte, jak nastavit OMS analýzy protokolů pro monitorování kontejnery- [monitorování a Diagnostika pro Windows kontejnerů v Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md)

