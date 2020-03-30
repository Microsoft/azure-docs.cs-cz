---
title: Poradce při potížích s přehledy aplikací ve webovém projektu Java
description: Průvodce odstraňováním problémů – sledování živých aplikací Java pomocí application insights.
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 04e98938bc5dd17816ae873f122073212275a414
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657176"
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Řešení potíží a otázky a odpovědi v nástroji Application Insights
Máte dotazy nebo problémy s [Azure Application Insights v Javě?][java] Tady je několik tipů.

## <a name="build-errors"></a>Chyby sestavení
**V Eclipse nebo Intellij Idea, při přidávání Application Insights SDK přes Maven nebo Gradle, dostanu sestavení nebo kontrolní součet ověřovací chyby.**

* Pokud prvek `<version>` závislosti používá vzorek se zástupnými znaky (např. (Maven) `<version>[2.0,)</version>` nebo `version:'2.0.+'`(Gradle), zkuste místo `2.0.1`toho zadat určitou verzi jako . Podívejte se na [poznámky k verzi](https://github.com/Microsoft/ApplicationInsights-Java/releases) pro nejnovější verzi.

## <a name="no-data"></a>Žádná data
**Úspěšně jsem přidal application insights a spustil jsem aplikaci, ale nikdy jsem neviděl data na portálu.**

* Počkejte chvíli a klikněte na Aktualizovat. Grafy se pravidelně aktualizují, ale můžete je také aktualizovat ručně. Interval aktualizace závisí na časovém rozsahu grafu.
* Zkontrolujte, zda máte klíč instrumentace definovaný v souboru ApplicationInsights.xml (ve složce prostředků v projektu) nebo nakonfigurovaný jako proměnná prostředí.
* Ověřte, `<DisableTelemetry>true</DisableTelemetry>` zda v souboru XML není žádný uzel.
* V bráně firewall může být pravděpodobně možné otevřít porty TCP 80 a 443 pro odchozí provoz dc.services.visualstudio.com. Zobrazit [úplný seznam výjimek brány firewall](../../azure-monitor/app/ip-addresses.md)
* V úvodní vývěsce Microsoft Azure se podívejte na mapu stavu služby. Pokud existují nějaké výstrahy, počkejte, dokud se vrátí do OK a zavřete a znovu otevřete okno aplikace Insights aplikace.
* [Zapněte protokolování](#debug-data-from-the-sdk) `<SDKLogger />` přidáním prvku pod kořenový uzel v souboru ApplicationInsights.xml (ve složce prostředků v projektu) a zkontrolujte, zda nejsou k dispozici položky s umělou ai: INFO/WARN/ERROR pro všechny podezřelé protokoly. 
* Ujistěte se, že správný soubor ApplicationInsights.xml byl úspěšně načten sadou Java SDK, a to tak, že se podíváte na výstupní zprávy konzoly pro příkaz "Konfigurační soubor byl úspěšně nalezen".
* Pokud konfigurační soubor nebyl nalezen, zkontrolujte výstupní zprávy, abyste zjistili, kde je konfigurační soubor vyhledáván, a ujistěte se, že soubor ApplicationInsights.xml je umístěn v jednom z těchto umístění hledání. Jako palec, můžete umístit konfigurační soubor v blízkosti Application Insights SDK JARs. Například: v Tomcat, to by znamenalo web-INF/třídy složky. Během vývoje můžete umístit ApplicationInsights.xml do složky prostředků vašeho webového projektu.
* Zkontrolujte také [stránku problémů GitHubu, kde](https://github.com/Microsoft/ApplicationInsights-Java/issues) najdete známé problémy se sadou SDK.
* Ujistěte se, že používáte stejnou verzi jádra Application Insights, web, agent a protokolování appenders, aby se zabránilo problémům s konfliktem verzí.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Dříve jsem viděl data, ale zastavil se
* Zkontrolujte [stav blogu](https://blogs.msdn.com/b/applicationinsights-status/).
* Dosáhli jste své měsíční kvóty datových bodů? Otevřete Nastavení/ Kvóta a Ceny, abyste to zjistili. Pokud ano, můžete svůj plán upgradovat nebo zaplatit za dodatečnou kapacitu. Viz [cenový režim](https://azure.microsoft.com/pricing/details/application-insights/).
* Nedávno jste upgradovali sadu SDK? Ujistěte se, že v adresáři projektu jsou k dispozici pouze jedinečné nádoby sady SDK. Neměly by existovat dvě různé verze sady SDK.
* Díváte se na správný zdroj AI? Porovnejte iKey vaší aplikace s prostředek, kde očekáváte telemetrii. Měly by být stejné.

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Nevidím všechna data, která očekávám
* Otevřete stránku Využití a odhadované náklady a zkontrolujte, zda je [vzorkování](../../azure-monitor/app/sampling.md) v provozu. (100% přenos znamená, že vzorkování není v provozu.) Službu Application Insights lze nastavit tak, aby přijímala pouze zlomek telemetrie, která přichází z vaší aplikace. To vám pomůže udržet v rámci vaší měsíční kvóty telemetrie.
* Máte zapnutou sdk vzorkování? Pokud ano, budou data vzorkována rychlostí stanovenou pro všechny příslušné typy.
* Používáte starší verzi sady Java SDK? Počínaje verzí 2.0.1 jsme zavedli mechanismus odolnosti proti chybám pro zpracování občasných selhání sítě a back-endu a také trvalosti dat na místních jednotkách.
* Jste stále škrtil kvůli nadměrné telemetrie? Pokud zapnete protokolování INFO, zobrazí se zpráva protokolu "Aplikace je omezena". Náš aktuální limit je 32k telemetrických položek za sekundu.

### <a name="java-agent-cannot-capture-dependency-data"></a>Agent jazyka Java nemůže zachytit data závislostí.
* Nakonfigurovali jste agenta Javy podle [pokynů k konfigurace java agenta](java-agent.md) ?
* Ujistěte se, že java agent jar a AI-Agent.xml soubor jsou umístěny ve stejné složce.
* Ujistěte se, že závislost, kterou se pokoušíte automaticky shromažďovat, je podporována pro automatické shromažďování. V současné době podporujeme pouze kolekci závislostí MySQL, MsSQL, Oracle DB a Azure Cache for Redis.

## <a name="no-usage-data"></a>Žádné údaje o využití
**Zobrazují se údaje o požadavcích a době odezvy, ale žádná zobrazení stránky, prohlížeč nebo uživatelská data**

Úspěšně jste nastavili aplikaci pro odesílání telemetrie ze serveru. Nyní je dalším krokem [nastavení webových stránek pro odesílání telemetrie z webového prohlížeče][usage].

Případně, pokud váš klient je aplikace v [telefonu nebo jiné zařízení][platforms], můžete odeslat telemetrie odtud.

Použijte stejný klíč instrumentace k nastavení telemetrie klienta i serveru. Data se zobrazí ve stejném prostředku Application Insights a budete moci korelovat události z klienta a serveru.


## <a name="disabling-telemetry"></a>Zakázání telemetrie
**Jak lze zakázat shromažďování telemetrie?**

V kódu:

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**Nebo**

Aktualizujte soubor ApplicationInsights.xml (ve složce prostředků v projektu). Pod kořenový uzel přidejte následující:

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

Pomocí metody XML je nutné restartovat aplikaci při změně hodnoty.

## <a name="changing-the-target"></a>Změna cíle
**Jak můžu změnit, kterému prostředkům Azure můj projekt odesílá data?**

* [Získejte klíč instrumentace nového prostředku.][java]
* Pokud jste do projektu přidali přehledy aplikací pomocí sady Nástrojů Azure pro eclipse, klikněte pravým tlačítkem myši na váš webový projekt, vyberte **Azure**, **Konfigurovat přehledy aplikací**a změňte klíč.
* Pokud jste nakonfigurovali instrumentační klíč jako proměnnou prostředí, aktualizujte hodnotu proměnné prostředí novým iKey.
* V opačném případě aktualizujte klíč v souboru ApplicationInsights.xml ve složce prostředků v projektu.

## <a name="debug-data-from-the-sdk"></a>Ladění dat ze sady SDK

**Jak zjistím, co sada SDK dělá?**

Chcete-li získat další informace o tom, `<SDKLogger/>` co se děje v rozhraní API, přidejte pod kořenový uzel konfiguračního souboru ApplicationInsights.xml.

### <a name="applicationinsightsxml"></a>ApplicationInsights.xml

Můžete také pokyn, aby úhozovací hod výstup do souboru:

```XML
  <SDKLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AISDK</BaseFolderPath>
</SDKLogger>
```

### <a name="spring-boot-starter"></a>Startér jarního startu

Chcete-li povolit protokolování sady SDK pomocí aplikací pro jarní `application.properties` spuštění pomocí startéru jarního spuštění application insights, přidejte do souboru následující:

```yaml
azure.application-insights.logger.type=file
azure.application-insights.logger.base-folder-path=C:/agent/AISDK
azure.application-insights.logger.level=trace
```

nebo vytisknout na standardní chybu:

```yaml
azure.application-insights.logger.type=console
azure.application-insights.logger.level=trace
```

### <a name="java-agent"></a>Agent Java

Chcete-li povolit protokolování agenta JVM, aktualizujte [soubor AI-Agent.xml](java-agent.md):

```xml
<AgentLogger type="FILE"><!-- or "CONSOLE" to print to stderr -->
    <Level>TRACE</Level>
    <UniquePrefix>AI</UniquePrefix>
    <BaseFolderPath>C:/agent/AIAGENT</BaseFolderPath>
</AgentLogger>
```

### <a name="java-command-line-properties"></a>Vlastnosti příkazového řádku java
_Od verze 2.4.0_

Povolení protokolování pomocí možností příkazového řádku bez změny konfiguračních souborů:

```
java -Dapplicationinsights.logger.file.level=trace -Dapplicationinsights.logger.file.uniquePrefix=AI -Dapplicationinsights.logger.baseFolderPath="C:/my/log/dir" -jar MyApp.jar
```

nebo vytisknout na standardní chybu:

```
java -Dapplicationinsights.logger.console.level=trace -jar MyApp.jar
```

## <a name="the-azure-start-screen"></a>Úvodní obrazovka Azure
**Dívám se na [portál Azure](https://portal.azure.com). Říká mi mapa něco o mé aplikaci?**

Ne, zobrazuje stav serverů Azure po celém světě.

*Jak najdu data o své aplikaci na úvodní ploše Azure (domovská obrazovka)*

Za předpokladu, že jste [aplikaci nastavili pro Application Insights][java], klikněte na Procházet, vyberte Application Insights a vyberte prostředek aplikace, který jste pro aplikaci vytvořili. Chcete-li se tam v budoucnu dostat rychleji, můžete aplikaci připnout na startovní tabuli.

## <a name="intranet-servers"></a>Intranetové servery
**Mohu sledovat server v síti intranet?**

Ano, za předpokladu, že váš server může odesílat telemetrická data na portál Application Insights prostřednictvím veřejného internetu.

V bráně firewall může být pravděpodobně možné otevřít porty TCP 80 a 443 pro odchozí provoz dc.services.visualstudio.com a f5.services.visualstudio.com.

## <a name="data-retention"></a>Uchovávání dat
**Jak dlouho jsou data na portálu uchovávána? Je to bezpečné?**

Viz [Uchovávání dat a ochrana osobních údajů][data].

## <a name="debug-logging"></a>Protokolování ladění
Aplikace Insights `org.apache.http`používá . To se přemístí do jádrových sklenic `com.microsoft.applicationinsights.core.dependencies.http`Application Insights pod oborem názvů . To umožňuje Application Insights pro zpracování scénářů, kde existují různé verze stejné `org.apache.http` v jednom základu kódu.

>[!NOTE]
>Pokud povolíte protokolování úrovně LADĚNÍ pro všechny obory názvů v aplikaci, `org.apache.http` bude `com.microsoft.applicationinsights.core.dependencies.http`dodrženo všemi spuštěnými moduly včetně přejmenovaných jako . Application Insights nebude možné použít filtrování pro tato volání, protože volání protokolu je provádí knihovny Apache. Protokolování úrovně ladění vytváří značné množství dat protokolu a nedoporučuje se pro živé produkční instance.


## <a name="next-steps"></a>Další kroky
**Nastavil jsem Application Insights pro svou aplikaci java server. Co jiného můžu dělat?**

* [Sledování dostupnosti webových stránek][availability]
* [Sledování využití webových stránek][usage]
* [Sledování problémů s používáním a diagnostikou problémů v aplikacích zařízení][platforms]
* [Napište kód pro sledování využití aplikace][track]
* [Zachycení diagnostických protokolů][javalogs]

## <a name="get-help"></a>Podpora
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)
* [Soubor problému na GitHubu](https://github.com/Microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[data]: ../../azure-monitor/app/data-retention-privacy.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[platforms]: ../../azure-monitor/app/platforms.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
[usage]: javascript.md

