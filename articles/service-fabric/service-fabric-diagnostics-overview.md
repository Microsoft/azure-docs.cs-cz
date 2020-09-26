---
title: Přehled monitorování a diagnostiky Azure Service Fabric
description: Přečtěte si o monitorování a diagnostice pro clustery, aplikace a služby Azure Service Fabric.
author: srrengar
ms.topic: conceptual
ms.date: 1/17/2019
ms.author: srrengar
ms.openlocfilehash: 25a50a2841a03929804be45be8012f9b5d0457ff
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91357127"
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitorování a Diagnostika pro Azure Service Fabric

Tento článek poskytuje přehled monitorování a diagnostiky pro Azure Service Fabric. Monitorování a diagnostika jsou zásadní pro vývoj, testování a nasazování úloh v jakémkoli cloudovém prostředí. Můžete například sledovat, jak se aplikace používají, akce prováděné Service Fabric platformou, využití prostředků s čítači výkonu a celkový stav clusteru. Pomocí těchto informací můžete diagnostikovat a opravovat problémy a zabránit jejich výskytu v budoucnu. Další části stručně vysvětlují každou oblast Service Fabric monitorování, které je potřeba vzít v úvahu pro produkční úlohy. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="application-monitoring"></a>Monitorování aplikace
Monitorování aplikací sleduje, jak se používají funkce a komponenty vaší aplikace. Chcete monitorovat své aplikace, abyste měli jistotu, že jsou zachyceny problémy, které mají vliv na uživatele. Zodpovědnost za monitorování aplikací je na uživatelích, kteří vyvíjí aplikaci a její služby, protože je jedinečná pro obchodní logiku vaší aplikace. Monitorování aplikací může být užitečné v následujících scénářích:
* Kolik provozu má aplikace? – Potřebujete škálovat vaše služby tak, aby splňovaly požadavky uživatelů, nebo řešit potenciální kritická místa ve vaší aplikaci?
* Je moje služba pro volání úspěšná a sledovaná?
* Jaké akce jsou přijímány uživateli mé aplikace? – Shromažďování telemetrie může sloužit pro budoucí vývoj funkcí a lepší diagnostiku pro chyby aplikací.
* Vyvolává moje aplikace neošetřené výjimky? 
* Co se děje v rámci služeb spuštěných uvnitř kontejnerů?

Důležité informace o monitorování aplikací je, že vývojáři můžou používat libovolné nástroje a architektury, které by chtěli, protože jsou v kontextu vaší aplikace. Další informace o řešení Azure pro monitorování aplikací najdete v Azure Monitor Application Insights při [analýze událostí pomocí Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md).
K dispozici je také kurz s [nastavením pro aplikace .NET](service-fabric-tutorial-monitoring-aspnet.md). V tomto kurzu se naučíte, jak nainstalovat správné nástroje, příklad pro psaní vlastní telemetrie ve vaší aplikaci a zobrazení diagnostiky aplikace a telemetrie v Azure Portal. 


## <a name="platform-cluster-monitoring"></a>Monitorování platformy (clusteru)
Uživatel má kontrolu nad tím, co telemetrie pochází ze své aplikace, protože uživatel zapisuje samotný kód, ale co se týče diagnostiky od Service Fabric platformy? Jedním z cílů Service Fabric je zajištění odolnosti aplikací proti selhání hardwaru. Tento cíl se dosahuje prostřednictvím schopnosti systémových služeb platformy zjistit problémy infrastruktury a rychlé úlohy převzetí služeb při selhání na jiné uzly v clusteru. V tomto konkrétním případě se ale v takovém případě k tomu, co kdyby vlastní systémové služby, neproblémují? Nebo pokud při pokusu o nasazení nebo přesunutí úlohy dojde k porušení pravidel pro umístění služeb? Service Fabric poskytuje diagnostiku pro tyto a další informace, abyste měli jistotu, že se v clusteru probíhají aktivity. Mezi příklady scénářů pro monitorování clusteru patří:

Service Fabric poskytuje ucelenou sadu událostí z boxu. K těmto [událostem Service Fabric](service-fabric-diagnostics-events.md) se dá dostat prostřednictvím eventstoru nebo provozního kanálu (kanál událostí vystavený platformou). 

* Service Fabric kanály událostí – v systému Windows jsou události Service Fabric k dispozici od jednoho poskytovatele ETW se sadou relevantních, která se `logLevelKeywordFilters` používá k výběru mezi provozními a datovými kanály pro zasílání zpráv &. Jedná se o způsob, jakým oddělíte odchozí Service Fabricé události, které se podle potřeby filtrují. V systému Linux Service Fabric události pocházejí prostřednictvím LTTng a jsou vloženy do jedné tabulky úložiště, ze které je lze podle potřeby filtrovat. Tyto kanály obsahují uspořádané strukturované události, které je možné použít k lepšímu pochopení stavu clusteru. V době vytváření clusteru jsou diagnostiky standardně povolené, takže se vytvoří tabulka Azure Storage, kde se události z těchto kanálů odesílají do budoucna k dotazování v budoucnosti. 

* Eventstoru – Eventstoru je funkce nabízená platformou, která poskytuje Service Fabric události platformy dostupné v Service Fabric Explorer a REST API. Pro každou entitu, např. Node, službu, aplikace a dotaz na základě času události, se můžete podívat na to, co se ve vašem clusteru chystá. Další informace o Eventstoru najdete v tématu [Přehled eventstoru](service-fabric-diagnostics-eventstore.md).    

![Snímek obrazovky se zobrazí na kartě události v podokně uzly několik událostí, včetně události NodeDown.](media/service-fabric-diagnostics-overview/eventstore.png)

Poskytnuté diagnostiky jsou ve formě komplexní sady událostí mimo pole. Tyto [události Service Fabric](service-fabric-diagnostics-events.md) ilustrují akce prováděné platformou na různých entitách, jako jsou uzly, aplikace, služby, oddíly atd. Pokud by byl uzel v posledním výše uvedeném případě mimo provoz, platforma by vygenerovala `NodeDown` událost a můžete ji okamžitě upozornit vámi zvoleným monitorovacím nástrojem. Mezi další běžné příklady patří `ApplicationUpgradeRollbackStarted` nebo `PartitionReconfigured` během převzetí služeb při selhání. **Stejné události jsou k dispozici v clusterech s Windows i Linux.**

Události se odesílají přes standardní kanály na Windows i Linux a dají se číst libovolným monitorovacím nástrojem, který je podporuje. Řešení Azure Monitor je Azure Monitor protokolů. Můžete si přečíst další informace o [integraci protokolů Azure monitor](service-fabric-diagnostics-event-analysis-oms.md) , které zahrnují vlastní operační řídicí panel pro váš cluster a některé ukázkové dotazy, ze kterých můžete vytvářet výstrahy. Další koncepty monitorování clusteru jsou k dispozici na [úrovni platforem a generování protokolů](service-fabric-diagnostics-event-generation-infra.md).

### <a name="health-monitoring"></a>Monitorování stavu
Platforma Service Fabric zahrnuje model stavu, který poskytuje rozšiřitelné vytváření sestav o stavu pro stav entit v clusteru. Každý uzel, aplikace, služba, oddíl, replika nebo instance má nepřetržitě aktualizovatelný stav. Stav může být buď "OK", "Warning" nebo "Error". Můžete si představit Service Fabric události jako operace prováděné clusterem do různých entit a stavu jako u každé entity jako přídavného jména. Pokaždé, když se změní stav konkrétní entity, vygeneruje se taky událost. Tímto způsobem můžete nastavit dotazy a výstrahy pro události stavu v monitorovacím nástroji zvoleném podobně jako u jakékoli jiné události. 

Kromě toho i tak umožníme uživatelům přepsat stav entit. Pokud vaše aplikace projde upgradem a testy ověření selžou, můžete zapisovat do Service Fabric stavu pomocí rozhraní API stavu, abyste označili, že aplikace už není v pořádku, a Service Fabric bude upgrade automaticky vrátit! Další informace o modelu stavu najdete v [úvodu ke Service Fabric sledování stavu](service-fabric-health-introduction.md) .

![Řídicí panel stavu SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)


### <a name="watchdogs"></a>Sledovací zařízení
Standardně se jedná o samostatnou službu, která může sledovat stav a zatížení napříč službami, koncovými body testu a hlásit stav pro cokoli v clusteru. To může zabránit chybám, které se nezjistily na základě zobrazení jedné služby. Pro hostování kódu, který provádí nápravné akce bez zásahu uživatele (například při čištění souborů protokolů v úložišti v určitých časových intervalech), jsou také dobrým místem. Ukázkovou implementaci sledovací služby najdete [tady](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="infrastructure-performance-monitoring"></a>Monitorování infrastruktury (výkonu)
Teď, když jsme pokryli diagnostiku ve vaší aplikaci a platformě, jak ví, že hardware funguje podle očekávání? Monitorování základní infrastruktury je klíčovou součástí porozumění stavu vašeho clusteru a využití prostředků. Měření výkonu systému závisí na mnoha faktorech, které mohou být v souladu s vašimi úlohami. Tyto faktory jsou obvykle měřeny prostřednictvím čítačů výkonu. Tyto čítače výkonu můžou pocházet z nejrůznějších zdrojů, včetně operačního systému, rozhraní .NET Framework nebo samotné platformy Service Fabric. Některé scénáře, ve kterých by byly užitečné, jsou

* Využívám svůj hardware efektivně? Chcete použít svůj hardware na procesorech 90% CPU nebo 10% CPU. To je užitečné při škálování clusteru nebo optimalizaci procesů aplikace.
* Můžu aktivně předpovědět problémy infrastruktury? – mnoho problémů předchází náhlé změny (pokles) ve výkonu, takže můžete použít čítače výkonu, jako jsou síťové vstupně-výstupní operace a využití CPU, a předpovědět a diagnostikovat problémy interaktivně.

Seznam čítačů výkonu, které by měly být shromážděny na úrovni infrastruktury, najdete v části [metriky výkonu](service-fabric-diagnostics-event-generation-perf.md). 

Service Fabric také poskytuje sadu čítačů výkonu pro programové modely Reliable Services a Actors. Pokud používáte některý z těchto modelů, můžou tyto čítače výkonu získat informace, aby se zajistilo, že se vaše aktéri správně otáčí a dolů nebo že vaše požadavky na spolehlivé služby jsou zpracovávány dostatečně rychle. Další informace najdete v tématu [monitorování spolehlivé vzdálené komunikace služby](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) a [sledování výkonu pro Reliable Actors](service-fabric-reliable-actors-diagnostics.md#performance-counters). 

Azure Monitor řešení, které se má shromáždit, je Azure Monitor protokolů stejně jako monitorování na úrovni platformy. [Log Analytics agenta](service-fabric-diagnostics-oms-agent.md) byste měli použít ke shromáždění odpovídajících čítačů výkonu a jejich zobrazení v protokolech Azure monitor.

## <a name="recommended-setup"></a>Doporučené nastavení
Teď, když jsme přešli na jednotlivé oblasti monitorování a ukázkových scénářů, tady je souhrn nástrojů pro monitorování Azure a potřebný pro monitorování všech oblastí výše. 

* Monitorování aplikací pomocí [Application Insights](service-fabric-tutorial-monitoring-aspnet.md)
* Monitorování clusteru pomocí [agenta diagnostiky](service-fabric-diagnostics-event-aggregation-wad.md) a [protokolů Azure monitor](service-fabric-diagnostics-oms-setup.md)
* Monitorování infrastruktury s [protokoly Azure monitor](service-fabric-diagnostics-oms-agent.md)

K automatizaci nasazení všech nezbytných prostředků a agentů můžete také použít a upravit ukázkovou šablonu ARM, která se nachází [zde](service-fabric-diagnostics-oms-setup.md#deploy-azure-monitor-logs-with-azure-resource-manager) . 

## <a name="other-logging-solutions"></a>Další řešení protokolování

I když jsou tato dvě řešení, která jsme doporučili, [Azure monitor protokoly](service-fabric-diagnostics-event-analysis-oms.md) a [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) vytvořili integraci s Service Fabric, mnoho událostí se zapisuje prostřednictvím poskytovatelů ETW a dají se rozšířit dalšími řešeními protokolování. Měli byste se také podívat do [elastického zásobníku](https://www.elastic.co/products) (obzvláště Pokud zvažujete spuštění clusteru v offline prostředí), [dynaTrace](https://www.dynatrace.com/)nebo jakékoli jiné platformy. Tady je seznam integrovaných partnerů, které jsou k dispozici [zde](service-fabric-diagnostics-partners.md).

Klíčové body pro libovolnou platformu, kterou si zvolíte, by měly zahrnovat to, jak vám vyhovuje uživatelské rozhraní, možnosti dotazování, vlastní vizualizace a řídicí panely a další nástroje, které poskytují, aby se vylepšily vaše možnosti monitorování. 

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak začít s instrumentací vašich aplikací, najdete v tématu [generování událostí a protokolů na úrovni aplikace](service-fabric-diagnostics-event-generation-app.md).
* Projděte si postup nastavení Application Insights pro vaši aplikaci s [monitorováním a diagnostikou ASP.NET Core aplikace na Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Přečtěte si další informace o monitorování platformy a o událostech, které Service Fabric poskytuje na [úrovni platforem a generování protokolů](service-fabric-diagnostics-event-generation-infra.md).
* Konfigurace Azure Monitor protokoluje integraci s Service Fabric při [nastavení protokolů Azure monitor pro cluster](service-fabric-diagnostics-oms-setup.md)
* Naučte se, jak nastavit protokoly Azure Monitor pro monitorování kontejnerů – [monitorování a diagnostiku kontejnerů Windows v Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Další informace najdete v tématu Příklady problémů s diagnostikou a řešení pomocí Service Fabric při [diagnostice běžných scénářů](service-fabric-diagnostics-common-scenarios.md) .
* Podívejte se na další diagnostické produkty, které se integrují s Service Fabric v [Service Fabric diagnostických partnerech](service-fabric-diagnostics-partners.md)
* Přečtěte si o obecných doporučeních pro monitorování prostředků Azure – [osvědčené postupy – monitorování a diagnostika](/azure/architecture/best-practices/monitoring). 
