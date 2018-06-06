---
title: Začínáme s Azure Application Insights s Javou v prostředí Eclipse | Microsoft docs
description: Pomocí modulu plug-in Eclipse přidejte výkon a sledování využití do vašeho webu Java pomocí Application Insights
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: e88c9f53-cd90-4abc-b097-1f170937908e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/12/2016
ms.author: mbullwin
ms.openlocfilehash: 8e8e63b053cb5bd504a41da9b537354a1dd42968
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34795556"
---
# <a name="get-started-with-application-insights-with-java-in-eclipse"></a>Začínáme s Application Insights s Javou v prostředí Eclipse
Application Insights SDK odesílá telemetrii z webové aplikace Java, takže je můžete analyzovat využití a výkonu. Modul plug-in pro službu Application Insights Eclipse automaticky nainstaluje sady SDK v projektu, abyste měli mimo pole telemetrie a rozhraní API, které můžete psát vlastní telemetrii.   

## <a name="prerequisites"></a>Požadavky
Aktuálně modul plug-in funguje pro projekty Maven a dynamické webové projekty v prostředí Eclipse.
([Přidat službu Application Insights na jiné typy projektu Java][java].)

Budete potřebovat:

* 1.7 prostředí JRE nebo 1.8
* Předplatné [Microsoft Azure](https://azure.microsoft.com/).
* [Integrované vývojové prostředí Eclipse pro vývojáře v jazyce Java EE](http://www.eclipse.org/downloads/), džínovinu nebo novější.
* Windows 7 nebo novější nebo Windows Server 2008 nebo novějším.

Pokud dáváte přednost, zkuste rozhraní Spring [konfigurace aplikace inicializátoru pružiny spouštěcí používat Průvodce Application Insights](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-java-applicationinsights)

## <a name="install-the-sdk-on-eclipse-one-time"></a>Instalace sady SDK v prostředí Eclipse (jednou)
Stačí udělat tento jednou pro každý počítač. Tento krok nainstaluje sada nástrojů, který poté můžete přidat sadu SDK do každé Dynamic Web Project.

1. V prostředí Eclipse klikněte na tlačítko Nápověda, nainstalovat nový Software.

    ![Nápověda, instalace nového softwaru](./media/app-insights-java-eclipse/0-plugin.png)
2. Sada SDK je v http://dl.microsoft.com/eclipse, v rámci Azure Toolkit.
3. Zrušte zaškrtnutí políčka **obraťte se na všechny lokality aktualizace...**

    ![Application Insights SDK zrušte vám poskytne všechny aktualizace lokality](./media/app-insights-java-eclipse/1-plugin.png)

Postupujte podle zbývajících kroků pro každý projekt, Java.

## <a name="create-an-application-insights-resource-in-azure"></a>Vytvořte prostředek Application Insights v Azure
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vytvořte nový prostředek Application Insights. Nastavte typ aplikace na webovou aplikaci Java.  

    ![Klikněte na + a vyberte Application Insights](./media/app-insights-java-eclipse/01-create.png)  

4. Najděte klíč instrumentace nového prostředku. Ten budete muset krátce vložit do projektu kódu.  

    ![V přehledu nového prostředku klikněte na tlačítko Vlastnosti a zkopírujte klíč instrumentace](./media/app-insights-java-eclipse/03-key.png)  

## <a name="add-application-insights-to-your-project"></a>Přidejte ke svému projektu Application Insights.
1. Přidejte Application Insights z místní nabídky projektu webové Java.

    ![V přehledu nového prostředku klikněte na tlačítko Vlastnosti a zkopírujte klíč instrumentace](./media/app-insights-java-eclipse/02-context-menu.png)
2. Vložte klíč instrumentace, který jste získali z portálu Azure.

    ![V přehledu nového prostředku klikněte na tlačítko Vlastnosti a zkopírujte klíč instrumentace](./media/app-insights-java-eclipse/03-ikey.png)

Klíč je odeslán společně s každou položkou telemetrie a říká službě Application Insights, aby ho zobrazila v prostředku.

## <a name="run-the-application-and-see-metrics"></a>Spusťte aplikaci a zobrazit metriky
Spusťte svoji aplikaci.

Vraťte se do zdroje Application Insights v Microsoft Azure.

Data požadavků HTTP se zobrazí v okně přehled. (Pokud zde nejsou, počkejte několik sekund a pak klikněte na tlačítko Aktualizovat.)

![Odpověď serveru, počty žádostí a selhání ](./media/app-insights-java-eclipse/5-results.png)

Proklikejte se prostřednictvím jakékoli grafu pro zobrazení podrobnějších metrik.

![Počty žádostí podle názvu](./media/app-insights-java-eclipse/6-barchart.png)

[Další informace o metrikách.][metrics]

A při zobrazení vlastností požadavku, uvidíte telemetrické události související s například požadavky a výjimkami.

![Všechny trasování pro tento požadavek](./media/app-insights-java-eclipse/7-instance.png)

## <a name="client-side-telemetry"></a>Telemetrických dat na straně klienta
V okně Rychlý Start klikněte na tlačítko Get kód ke sledování mých webových stránek:

![V okně přehledu aplikace zvolte Rychlý start, získat kód ke sledování webové stránky. Zkopírujte skript.](./media/app-insights-java-eclipse/02-monitor-web-page.png)

Vložte fragment kódu v hlavě soubory ve formátu HTML.

#### <a name="view-client-side-data"></a>Zobrazení dat na straně klienta
Otevřete aktualizované webových stránek a jejich použití. Počkejte minutu nebo dvě, pak se vraťte do Application Insights a otevřete okno využití. (V okně Přehled přejděte dolů a klikněte na využití.)

Stránka zobrazení, uživatele a relace metriky se zobrazí v okně využití:

![Relace, uživatelů a zobrazení stránek](./media/app-insights-java-eclipse/appinsights-47usage-2.png)

[Další informace o nastavení telemetrických dat na straně klienta.][usage]

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
V okně vaší přehled přejděte dolů a kliknutím **servery** dlaždici. Uvidíte rozsah čítačů výkonu.

![Posuňte se dolů a klikněte na dlaždici servery](./media/app-insights-java-eclipse/11-perf-counters.png)

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
Application Insights může otestovat váš web v pravidelných intervalech a zkontrolovat, zda je funkční a dobře reaguje. [Nastavit][availability], přejděte dolů a klikněte na tlačítko dostupnost.

![Posuňte se dolů, klikněte na tlačítko Dostupnost a pak Přidat test webu](./media/app-insights-java-eclipse/31-config-web-test.png)

Získáte tabulky s dobami odezvy a navíc e-mailová oznámení, pokud váš web nefunguje.

![Příklad webového testu](./media/app-insights-java-eclipse/appinsights-10webtestresult.png)

[Další informace o testech dostupnosti webu.][availability]

## <a name="diagnostic-logs"></a>Diagnostické protokoly
Pokud používáte Logback nebo Log4J (verze 1.2 nebo v2.0) pro trasování, může mít vaše protokoly trasování automaticky odešlou do Application Insights, kde vám umožní zkoumat a hledat v nich.

[Další informace o diagnostických protokolů][javalogs]

## <a name="custom-telemetry"></a>Vlastní telemetrii
Po zadání několika řádků kódu vložte ve webové aplikaci Java a zjistěte, co uživatelé dělají s ním nebo k diagnostice potíží.

Kód můžete vložit na webové stránce JavaScript i v jazyce Java na straně serveru.

[Další informace o vlastní telemetrii][track]

## <a name="next-steps"></a>Další postup
#### <a name="detect-and-diagnose-issues"></a>Najít a diagnostikovat problémy
* [Přidat telemetrie webového klienta] [ usage] sám výkonu telemetrie webového klienta.
* [Nastavení webových testů][availability] pro zajištění, že aplikace zůstane funkční a bude reagovat.
* [Prohledávejte události a protokoly][diagnostic] pro pomoc s diagnostikou problémů.
* [Zaznamenat trasování Log4J nebo Logback][javalogs]

#### <a name="track-usage"></a>Sledovat využití
* [Přidat telemetrie webového klienta] [ usage] monitorování zobrazení stránek a metrik základní uživatele.
* [Sledujte vlastní události a metriky](app-insights-web-track-usage.md) Další informace o tom, jak se používá aplikace, jak na klientovi a na serveru.

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-javascript.md
