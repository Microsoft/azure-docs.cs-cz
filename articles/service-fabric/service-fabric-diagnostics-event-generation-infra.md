---
title: Monitorování na úrovni platformy Azure Service Fabric | Dokumentace Microsoftu
description: Další informace o protokoly používá k monitorování a Diagnostika clustery Azure Service Fabric a událostí na úrovni platformy.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: 96bbb221f5fa133ee88a09d489627e3d2f9b0713
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49409182"
---
# <a name="monitoring-the-cluster-and-platform"></a>Monitorování clusteru a platformy

Je důležité monitorovat na úrovni platformy k určení, zda hardware a cluster se nechová podle očekávání. Když Service Fabric můžete ponechat aplikace během selhání hardwaru, ale je stále potřeba zjistit, jestli k chybě dochází v aplikaci nebo v základní infrastruktuře. Byste také měli sledovat vašich clusterů do lépe plánovat kapacitu, pomáhá v rozhodování o přidání nebo odebrání hardwaru.

Service Fabric poskytuje několik událostí strukturovaných platformy, jako "[události Service Fabric](service-fabric-diagnostics-events.md)," prostřednictvím Eventstoru a různými protokolu kanály out-of-the-box. 

Eventstoru poskytuje přístup k události váš cluster na základě za entity (včetně clusteru, uzly, aplikace, služby, oddíly, repliky a kontejnery entit) a zpřístupňuje je prostřednictvím rozhraní REST API a klientské knihovny Service Fabric. Použít Eventstoru k monitorování clusterů pro vývoj/testování a získání bodu v čase přehled o stavu vašich produkčních clusterů. Další informace najdete v [přehled Eventstoru](service-fabric-diagnostics-eventstore.md).

Service Fabric nabízí také že následující protokol kanály out-of-the-box pro nastavením kanálu pro monitorování produkčních clusterech:

* [**Provozní**](service-fabric-diagnostics-event-generation-operational.md)  
Základní operace prováděné Service Fabric a clusteru, včetně událostí pro uzel vzniká, nové aplikace se nasazuje nebo upgradu vrácení zpět, atd.

* **Provozní – podrobné**  
Sestav o stavu a vyrovnávání zátěže rozhodnutí.

* **Dat & zasílání zpráv**  
Kritické protokolů a událostí vygenerovaných při zasílání zpráv (aktuálně pouze ReverseProxy) a cesta k datům (modely modelu reliable services).

* **Dat & zasílání zpráv – podrobně**  
Podrobné kanál, který obsahuje všechny protokoly, které jsou méně náročné z dat a zasílání zpráv v clusteru (v tomto kanálu má velmi velký objem událostí).

Kromě toho existují dvě strukturovaných kanály EventSource, jakož i protokoly, které shromažďujeme pro účely podpory.

* [Události Reliable Services](service-fabric-reliable-services-diagnostics.md)  
Programovací model konkrétní události.

* [Události Reliable Actors](service-fabric-reliable-actors-diagnostics.md)  
Programovací model konkrétních událostí a čítačů výkonu.

* Protokoly podpory  
Systémové protokoly generované Service Fabric pouze pro použití stranou při poskytování podpory.

Tyto různé kanály pokrýval většinu úrovně protokolování platformy, které se doporučuje. Chcete-li zvýšit úroveň protokolování platformy, zvažte prošetřují lepší pochopení stavu modelu a přidání vlastních stavových sestav a přidání vlastní **čítače výkonu** k sestavení v reálném čase přehled o dopadu vaší služby a aplikace v clusteru.

Abyste mohli využívat tyto protokoly, důrazně doporučujeme, aby při vytváření clusteru "Diagnostika" je povolená. Když zapnete diagnostiku, při nasazení clusteru Windows Azure Diagnostics je schopen potvrdit provozní, Reliable Services a Reliable actors kanálů a ukládání dat, jako je vysvětleno v [agregace událostí s využitím Azure Diagnostika](service-fabric-diagnostics-event-aggregation-wad.md).

## <a name="azure-service-fabric-health-and-load-reporting"></a>Stav služby Azure Service Fabric a generování sestav zatížení

Service Fabric má svůj vlastní stav modelu, který je podrobně popsán v těchto článcích:

- [Úvod do monitorování stavu Service Fabric](service-fabric-health-introduction.md)
- [Hlášení a kontrola stavu služeb](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Přidání vlastních stavových sestav Service Fabric](service-fabric-report-health.md)
- [Zobrazení sestav health Service Fabric](service-fabric-view-entities-aggregated-health.md)

Monitorování stavu je důležité pro několik aspektů provoz služby. Monitorování stavu je obzvláště důležité, když Service Fabric provádí upgrade s názvem aplikace. Po každé upgradovací doména služby se upgraduje a je k dispozici pro vaše zákazníky, upgradovací doména musí uplynout kontroly stavu nasazení přejde k další upgradovací doméně. Pokud není možné dosáhnout dobrý stav, nasazení se vrátí zpět, tak, aby aplikace ve známých a dobrý stav. I když někteří zákazníci může mít vliv na před služby jsou vráceny zpět, většina zákazníků se setkat s problémem. Řešení také vyvolá poměrně rychle a bez nutnosti čekání na akce z lidského operátora. Další kontroly stavu, které jsou součástí kódu, odolnější vaše služba má potíže s nasazením.

Dalším aspektem služby service health je generování sestav metrik ze služby. Metriky jsou důležité pro Service Fabric, protože se používají k vyrovnávání využití prostředků. Metriky taky může být indikátor stavu systému. Například může mít aplikaci, která má mnoho služeb, a každá instance sestavy požadavků na druhý metriku (předávajících stran). Pokud jedna služba používá více prostředků, než jiné službě, Service Fabric se přesune instance služby kolem clusteru, se pokouší spravovat využití prostředků i. Podrobnější vysvětlení fungování využití prostředků najdete v tématu [Správa spotřeby prostředků a načíst do Service Fabric s metrikami](service-fabric-cluster-resource-manager-metrics.md).

Metriky taky může pomoct vám poskytnou přehled o tom, jaký je výkon vaší služby. V průběhu času můžete použít metriky ke kontrole, že služba je zpracovávána v rámci očekávaných parametrech. Například pokud trendy ukazují, že v 9: 00 v pondělí ráno průměr RPS je 1 000, pak může nastavíte sestavu stavu, která vás upozorní, pokud RPS je nižší než 500 nebo vyšší než 1 500. Všechno, co může být zcela v pořádku, ale může být vhodné podívat, abyste měli jistotu, že vaši zákazníci mají skvělé prostředí. Služby můžete definovat sadu metrik, která může být hlášena pro účely kontroly stavu, ale který nemají vliv na vyrovnávání prostředků clusteru. K tomuto účelu nastavte váha metriky na nulu. Doporučujeme spustit všechny metriky s váhou nula a neukončil váha, dokud si nebude jisti, že chápete, jak vážení metriky ovlivňuje prostředků pro váš cluster služby Vyrovnávání.

> [!TIP]
> Nepoužívejte příliš mnoho vážený metriky. Může být obtížné pochopit, proč se přesouvání instance služby pro vyrovnávání. Několik metrik můžete urazily dlouhou cestu!

Veškeré informace, které můžete určit stav a výkon vaší aplikace je kandidátem pro metriky a hlášení o stavu. Čítač výkonu procesoru se dozvíte, jak se využívá uzlu, ale neříká vás, jestli je konkrétní služba v pořádku a vzhledem k tomu, že několik služeb, které můžou běžet na jednom uzlu. Však RPS, zpracování položek, jako jsou metriky a všechny vyřízení požadavku můžete určit stav specifické služby.

## <a name="service-fabric-support-logs"></a>Protokoly podpora Service Fabric

Pokud budete potřebovat kontaktovat podporu Microsoftu o pomoc s vaším clusterem Azure Service Fabric, podporu protokoly jsou téměř vždy povinné. Pokud váš cluster je hostovaný v Azure, podporu protokoly jsou automaticky nakonfigurované a shromažďuje jako součást vytváření clusteru. Protokoly se ukládají do vyhrazeného úložiště účtu ve skupině prostředků vašeho clusteru. Účet úložiště nemá Pevný název, ale v účtu, uvidíte kontejnery objektů blob a tabulky s názvy, které začínají *fabric*. Informace o nastavení protokolu kolekce pro samostatného clusteru najdete v tématu [vytvoření a Správa samostatného clusteru Azure Service Fabric](service-fabric-cluster-creation-for-windows-server.md) a [nastavení konfigurace pro samostatný Windows cluster](service-fabric-cluster-manifest.md). Pro samostatné instance Service Fabric protokoly odesílaných do místní sdílené složky. Jste **požadované** mít tyto protokoly pro podporu, ale nemají být použitelné kdokoli mimo tým podpory zákazníků společnosti Microsoft.

## <a name="measuring-performance"></a>Měření výkonu

Měřit výkon vašeho clusteru vám pomůže pochopit, jak je schopný zvládnout zátěž a jednotky rozhodnutí, která kolem škálování clusteru (viz informace o škálování clusteru [v Azure](service-fabric-cluster-scale-up-down.md), nebo [místní](service-fabric-cluster-windows-server-add-remove-nodes.md)). Údaje o výkonu je také užitečné ve srovnání s akcemi, které vám nebo vaší aplikace a služby mohou trvalo, při analýze protokolů v budoucnu. 

Seznam čítačů výkonu k získání při použití Service Fabric najdete v tématu [čítače výkonu v Service Fabric](service-fabric-diagnostics-event-generation-perf.md)

Tady jsou dvě běžné způsoby, ve kterých můžete nastavit shromažďování dat výkonu pro váš cluster:

* **Pomocí agenta**  
Toto je upřednostňovaný způsob shromažďování výkonu z počítače, protože agenti mají obvykle seznam možného výkonu metriky, které se můžou shromažďovat, a je poměrně snadné procesu zvolit metriky, které chcete shromažďovat nebo změnit. Přečtěte si informace o [konfigurace agenta Log Analytics pro Service Fabric](service-fabric-diagnostics-event-analysis-oms.md) a [nastavení agenta Log Analytics](../log-analytics/log-analytics-windows-agent.md) články získat další informace o agenta Log Analytics, což je jedna taková agent monitorování, která je možnost vyzvednutí údaje o výkonu pro virtuální počítače clusteru a nasazených kontejnerů.

* **Konfiguruje se Diagnostika pro zápis čítače výkonu do tabulky**  
Pro clustery v Azure to znamená změnu konfigurace diagnostiky Azure ke sbírání čítačů výkonu odpovídající z virtuálních počítačů v clusteru a povolení ho ke sbírání statistiky dockeru, pokud budete současně nasazovat žádné kontejnery. Přečtěte si informace o konfiguraci [čítače výkonu v WAD](service-fabric-diagnostics-event-aggregation-wad.md) v Service Fabric k nastavení kolekce čítačů výkonu.

## <a name="next-steps"></a>Další postup

Potřeba agregovat před odesláním na jakoukoli platformu analýzy protokolů a událostí. Přečtěte si informace o [využitím EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) a [WAD](service-fabric-diagnostics-event-aggregation-wad.md) lépe pochopit některé doporučené možnosti.
