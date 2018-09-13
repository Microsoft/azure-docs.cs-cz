---
title: Začínáme s Azure Application Insights s Javou v prostředí Eclipse | Dokumentace Microsoftu
description: Pomocí modulu plug-in Eclipse pro přidání výkon a sledování využití pro web v Javě s využitím Application Insights
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: e88c9f53-cd90-4abc-b097-1f170937908e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 12/12/2016
ms.author: mbullwin
ms.openlocfilehash: ebcfe02eb8d969af26f5121bda85e4610302e838
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35642893"
---
# <a name="get-started-with-application-insights-with-java-in-eclipse"></a>Začínáme s Application Insights s Javou v Eclipse
Sada SDK služby Application Insights odesílá telemetrii z vaší webové aplikace v Javě, tak, aby využití a výkonu můžete analyzovat. Modul plug-in pro službu Application Insights Eclipse automaticky nainstaluje sady SDK ve vašem projektu, aby získáváte z telemetrických dat pole a rozhraní API, které vám pomůže psát vlastní telemetrii.   

## <a name="prerequisites"></a>Požadavky
Aktuálně modul plug-in funguje pro projekty Maven a dynamické webových projektů v prostředí Eclipse.
([Přidat službu Application Insights na jiné typy projektů Java][java].)

Budete potřebovat:

* Prostředí JRE 1.7 nebo 1.8
* Předplatné [Microsoft Azure](https://azure.microsoft.com/).
* [Integrované vývojové prostředí Eclipse pro vývojáře na platformě Java EE](http://www.eclipse.org/downloads/), Indigo nebo novější.
* Windows 7 nebo novější nebo Windows Server 2008 nebo novější

Pokud dáváte přednost rozhraní Spring, zkuste [nakonfigurovat aplikaci Spring Boot Initializer, abyste mohli použít příručku ke službě Application Insights](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-java-applicationinsights).

## <a name="install-the-sdk-on-eclipse-one-time"></a>Instalace sady SDK v Eclipse (jednou)
Stačí provést jednou na počítač. Tento krok nainstaluje sadu nástrojů, které potom můžete přidat sadu SDK do jednotlivých dynamický webový projekt.

1. V Eclipse klikněte na tlačítko Nápověda, instalace nového softwaru.

    ![Nápověda, instalace nového softwaru](./media/app-insights-java-eclipse/0-plugin.png)
2. Sada SDK je v http://dl.microsoft.com/eclipse, v rámci sady Azure Toolkit.
3. Zrušte zaškrtnutí políčka **kontaktovat všechny weby aktualizace...**

    ![Pro sadu SDK Application Insights zrušte kontaktujte všechny weby aktualizace](./media/app-insights-java-eclipse/1-plugin.png)

Postupujte podle pokynů pro každý projekt v Javě.

## <a name="create-an-application-insights-resource-in-azure"></a>Vytvořte prostředek Application Insights v Azure
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vytvořte nový prostředek Application Insights. Nastavte typ aplikace na webovou aplikaci Java.  

    ![Klikněte na + a vyberte Application Insights](./media/app-insights-java-eclipse/01-create.png)  

4. Najděte klíč instrumentace nového prostředku. Ten budete muset krátce vložit do projektu kódu.  

    ![V přehledu nového prostředku klikněte na tlačítko Vlastnosti a zkopírujte klíč instrumentace](./media/app-insights-java-eclipse/03-key.png)  

## <a name="add-application-insights-to-your-project"></a>Přidejte ke svému projektu Application Insights.
1. Přidejte Application Insights v místní nabídce vašeho webového projektu v Javě.

    ![V přehledu nového prostředku klikněte na tlačítko Vlastnosti a zkopírujte klíč instrumentace](./media/app-insights-java-eclipse/02-context-menu.png)
2. Vložte klíč instrumentace, který jste získali z portálu Azure portal.

    ![V přehledu nového prostředku klikněte na tlačítko Vlastnosti a zkopírujte klíč instrumentace](./media/app-insights-java-eclipse/03-ikey.png)

Klíč se pošle společně s každou položkou telemetrie a říká službě Application Insights, aby ho zobrazila v prostředku.

## <a name="run-the-application-and-see-metrics"></a>Spuštění aplikace a zobrazení metrik
Spusťte aplikaci.

Vraťte se do prostředku Application Insights v Microsoft Azure.

Data požadavků HTTP se zobrazí v okně přehled. (Pokud zde nejsou, počkejte několik sekund a pak klikněte na tlačítko Aktualizovat.)

![Odpověď serveru, počty žádostí a selhání ](./media/app-insights-java-eclipse/5-results.png)

Proklikejte se prostřednictvím jakékoli grafu pro zobrazení podrobnějších metrik.

![Požadavek se počítá podle názvu](./media/app-insights-java-eclipse/6-barchart.png)

[Další informace o metrikách.][metrics]

A při zobrazení vlastností požadavku uvidíte telemetrické události související s například požadavky a výjimky.

![Všechna trasování pro tuto žádost](./media/app-insights-java-eclipse/7-instance.png)

## <a name="client-side-telemetry"></a>Telemetrie na straně klienta
Z okna rychlý Start klikněte na tlačítko získat kód ke sledování mých webových stránek:

![V okně přehledu aplikace zvolte Rychlý start, získat kód ke sledování webové stránky. Zkopírujte skript.](./media/app-insights-java-eclipse/02-monitor-web-page.png)

Vložte fragment kódu v čele soubory ve formátu HTML.

#### <a name="view-client-side-data"></a>Zobrazení dat na straně klienta
Otevřete aktualizované webové stránky a jejich použití. Počkejte minutu nebo dvě, a vraťte se do služby Application Insights a otevřete okno využití. (V okně Přehled přejděte dolů a klikněte na odkaz využití.)

Stránka zobrazení, uživatelů a relací metriky se zobrazí v okně využití:

![Relací, uživatelů a zobrazení stránek](./media/app-insights-java-eclipse/appinsights-47usage-2.png)

[Další informace o nastavení telemetrie na straně klienta.][usage]

## <a name="publish-your-application"></a>Publikování aplikace
Teď publikujte aplikaci na server, dovolte osobám ji používat a sledujte telemetrii zobrazenou na portálu.

* Ujistěte se, že brána firewall umožňuje vaší aplikace odesílat telemetrii na tyto porty:

  * dc.services.visualstudio.com:443
  * dc.services.visualstudio.com:80
  * f5.services.visualstudio.com:443
  * f5.services.visualstudio.com:80
* Na serverech Windows nainstalujte:

  * [Microsoft Visual C++ Redistributable](http://www.microsoft.com/download/details.aspx?id=40784)

    (Umožňuje čítače výkonu.)

## <a name="exceptions-and-request-failures"></a>Výjimky a chyby požadavků
Nezpracované výjimky jsou shromažďovány automaticky:

![](./media/app-insights-java-eclipse/21-exceptions.png)

Chcete-li shromažďovat data o dalších výjimkách, máte dvě možnosti:

* [Vložit volání pro TrackException ve vašem kódu](app-insights-api-custom-events-metrics.md#trackexception).
* [Nainstalovat na server agenta Java](app-insights-java-agent.md). Určete metody, které chcete sledovat.

## <a name="monitor-method-calls-and-external-dependencies"></a>Volání metody monitorování a externí závislosti
[Nainstalujte agenta Java](app-insights-java-agent.md) k protokolování určených vnitřních metod a volání provedená prostřednictvím JDBC s daty časování.

## <a name="performance-counters"></a>Čítače výkonu
V okně Přehled, posuňte se dolů a klikněte na tlačítko **servery** dlaždici. Uvidíte rozsah čítačů výkonu.

![Posuňte se dolů klikněte na dlaždici na serverech](./media/app-insights-java-eclipse/11-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Vlastní nastavení kolekce čítačů výkonu
Pro zakázání shromažďování standardní sady čítačů výkonu přidejte následující kód do kořenového uzlu souboru ApplicationInsights.xml:

```XML

    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>Shromažďování dalších čítačů výkonu
Můžete zadat další čítače výkonu, které se mají shromažďovat.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>Čítače JMX (vystavené ve virtuálním počítači Java)

```XML

    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName` – název zobrazený na portálu služby Application Insights
* `objectName` – název objektu JMX
* `attribute` – atribut názvu objektu JMX k načtení
* `type`(volitelné) – typ atributu JMX objektu:
  * Výchozí hodnota: jednoduchý typ, například int nebo long.
  * `composite`: data čítače výkonu jsou ve formátu „Attribute.Data“
  * `tabular`: data čítače výkonu jsou ve formátu řádku tabulky

#### <a name="windows-performance-counters"></a>Čítače výkonu Windows
Každý [čítač výkonu systému Windows](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) je členem určité kategorie (stejným způsobem, jakým je pole členem třídy). Kategorie mohou být buď globální, nebo mohou mít číslované nebo pojmenované instance.

```XML

    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName – Název zobrazený na portálu služby Application Insights.
* categoryName – kategorie čítače výkonu (objekt výkonu) ke kterému je přiřazen tento čítač výkonu.
* counterName – název čítače výkonu.
* instanceName – název instance kategorie čítače výkonu nebo prázdný řetězec (""), pokud kategorie obsahuje jednu instanci. Pokud je categoryName proces a čítač výkonu, který chcete shromáždit, pochází z aktuálního procesu JVM, na kterém běží vaše aplikace, zadejte `"__SELF__"`.

Čítače výkonu jsou zobrazené jako vlastní metriky v [Průzkumníku metrik][metrics].

![](./media/app-insights-java-eclipse/12-custom-perfs.png)

### <a name="unix-performance-counters"></a>Čítače výkonu Unix
* [Nainstalujte collectd s modulem plug-in Application Insights](app-insights-java-collectd.md) a získejte celou řadu dat systému a sítě.

## <a name="availability-web-tests"></a>Testy dostupnosti webu
Application Insights může otestovat váš web v pravidelných intervalech a zkontrolovat, zda je funkční a dobře reaguje. [Chcete-li nastavit][availability], přejděte dolů a klikněte na tlačítko dostupnost.

![Posuňte se dolů, klikněte na tlačítko Dostupnost a pak Přidat test webu](./media/app-insights-java-eclipse/31-config-web-test.png)

Získáte tabulky s dobami odezvy a navíc e-mailová oznámení, pokud váš web nefunguje.

![Příklad webového testu](./media/app-insights-java-eclipse/appinsights-10webtestresult.png)

[Další informace o testech dostupnosti webu.][availability]

## <a name="diagnostic-logs"></a>Diagnostické protokoly
Pokud již používáte, Logback nebo Log4J (verze 1.2 nebo 2.0) pro trasování, může mít protokoly trasování automaticky odesílaných do Application Insights, kde můžete prozkoumat a hledat v nich.

[Další informace o diagnostických protokolů][javalogs]

## <a name="custom-telemetry"></a>Vlastní telemetrii
Vložte několik řádků kódu webové aplikace v Javě a zjistěte, jak uživatelé pracují s ním nebo pomoc s diagnostikou problémů.

Kód můžete vložit do webové stránky JavaScript i v Javě na straně serveru.

[Další informace o vlastní telemetrii][track]

## <a name="next-steps"></a>Další postup
#### <a name="detect-and-diagnose-issues"></a>Detekujte a Diagnostikujte problémy
* [Přidání telemetrie webového klienta] [ usage] získejte telemetrii výkonu z webového klienta.
* [Nastavení webových testů][availability] pro zajištění, že aplikace zůstane funkční a bude reagovat.
* [Prohledávejte události a protokoly][diagnostic] pro pomoc s diagnostikou problémů.
* [Záznam trasování Log4J nebo Logback][javalogs]

#### <a name="track-usage"></a>Sledovat využití
* [Přidání telemetrie webového klienta] [ usage] pro monitorování zobrazení stránek a metrik základní uživatel.
* [Sledujte vlastní události a metriky](app-insights-web-track-usage.md) Další informace o aplikaci způsobu, jak u klienta a serveru.

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-javascript.md
