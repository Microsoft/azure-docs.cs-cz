---
title: Řešení potíží s Application Insights v Javovém webovém projektu
description: Průvodce odstraňováním potíží – monitorování živých aplikací Java pomocí Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: ef602767-18f2-44d2-b7ef-42b404edd0e9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 04/02/2018
ms.author: mbullwin
ms.openlocfilehash: 26899ea62b8caa872b6c99b94976c87f84ba7176
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35643082"
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Řešení potíží a otázky a odpovědi v nástroji Application Insights
Dotazy nebo problémy s [Azure Application Insights v jazyce Java][java]? Zde jsou některé tipy.

## <a name="build-errors"></a>Chyby sestavení
**V prostředí Eclipse nebo Intellij Idea, při přidání Application Insights SDK pomocí Maven nebo Gradle se zobrazí chyby ověření sestavení nebo kontrolního součtu.**

* Pokud závislost <version> element je pomocí vzoru se zástupnými znaky (například (Maven) `<version>[2.0,)</version>` nebo (Gradle) `version:'2.0.+'`), zkuste místo toho zadat konkrétní verzi jako `2.0.1`. Zobrazit [poznámky k verzi](https://github.com/Microsoft/ApplicationInsights-Java/releases) pro nejnovější verzi.

## <a name="no-data"></a>Žádná data
**Můžu úspěšně přidali Application Insights a spustili svou aplikaci, ale I jste nikdy se neprohlédlo dat na portálu.**

* Počkejte minutu a klikněte na tlačítko Aktualizovat. Grafy se pravidelně samy aktualizují, ale můžete také aktualizovat ručně. Interval aktualizace závisí na časový rozsah grafu.
* Zkontrolujte, že máte Instrumentační klíč definované v souboru ApplicationInsights.xml (ve složce prostředky ve vašem projektu) nebo konfigurovat jako proměnné prostředí.
* Ověřte, že neexistuje žádná `<DisableTelemetry>true</DisableTelemetry>` uzlu v souboru xml.
* V bráně firewall možná budete muset otevřít porty TCP 80 a 443 pro odchozí provoz pro adresu dc.services.visualstudio.com. Zobrazit [úplný seznam výjimky brány firewall](app-insights-ip-addresses.md)
* V Microsoft Azure začít panelu, podívejte se na mapě služby stavu. Pokud jsou některé upozornění údaje, počkejte, dokud se změnil na OK a pak zavřete a znovu otevřete okno aplikace Application Insights.
* Zapnutí protokolování do okna konzoly integrovaného vývojového prostředí tak, že přidáte `<SDKLogger />` element v rámci kořenového uzlu souboru ApplicationInsights.xml (ve složce prostředky ve vašem projektu) a zkontrolovat položky začíná AI: informace o/WARN nebo v podrobnostech o CHYBĚ všechny podezřelé protokoly.
* Ujistěte se, že správný soubor ApplicationInsights.xml úspěšně načetl sady Java SDK zobrazením v konzole výstupní zprávy pro příkaz "konfigurační soubor byl úspěšně nalezl".
* Pokud není nalezen konfigurační soubor, zkontrolujte výstupní zprávy zobrazíte, ve kterém má být vyhledán konfiguračního souboru pro a ujistěte se, že soubor ApplicationInsights.xml nachází v jednom z těchto umístění vyhledávání. Jako říci můžete umístit konfiguračního souboru téměř Application Insights SDK JARs. Příklad: v Tomcatu, to znamená třídy webu INF/složky. Během vývoje můžete umístit soubor ApplicationInsights.xml do složky prostředky webového projektu.
* Podrobnosti se také podívat na [stránce problémů Githubu](https://github.com/Microsoft/ApplicationInsights-Java/issues) známých problémů v sadě SDK.
* Nezapomeňte použít stejnou verzi nástroje Application Insights core, web, agent a protokolování appenders, aby všechny problémy s verzí ke konfliktu.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Můžu použít k zobrazení dat, ale byla zastavena
* Zkontrolujte, [stav blogu](http://blogs.msdn.com/b/applicationinsights-status/).
* Jste nedosáhli kvóta měsíčního počtu datových bodů? Otevřete nastavení/kvóta a ceny zjistit. Pokud ano, můžete upgradovat svůj plán nebo platit za dodatečnou kapacitu. Zobrazit [cenové schéma](https://azure.microsoft.com/pricing/details/application-insights/).
* Jste nedávno přešli vaši sadu SDK? Ujistěte se prosím, že pouze kromě souborů JAR jedinečný SDK jsou k dispozici v adresáři projektu. By neměla existovat dvě různé verze sady SDK, které jsou k dispozici.
* Sledujete správné prostředku Application Insights? Porovná prosím Instrumentační klíč vaší aplikace na prostředek, kde očekáváte telemetrická data. Měly by být stejné.

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Nevidím všechna data, který bych čekal
* Otevřete využití a odhadované náklady na stránku a zkontrolujte, jestli [vzorkování](app-insights-sampling.md) je v provozu. (přenos 100 % znamená, že vzorkování není v provozu.) Služba Application Insights můžete nastavit tak, aby přijímal pouze zlomek telemetrická data přenášená z vaší aplikace. To pomáhá při synchronizaci v rámci kvóta měsíčního počtu telemetrická data. 
* Máte zapnuté vzorkování SDK? Pokud ano, dat by definuje sazby stanovené pro všechny platné typy.
* Používáte starší verzi sady Java SDK? Počínaje verzí 2.0.1, zavedli jsme odolnost proti chybám mechanismem pro zpracování sítě a selhání back-endu, jakož i trvalost dat na místních jednotkách.
* Jsou získávání omezena z důvodu nadměrného telemetrie? Pokud zapnete informace o protokolování, zobrazí se protokol zpráv "Aplikace se omezí". Naše současný limit je 32 kB telemetrie položek za sekundu.

### <a name="java-agent-cannot-capture-dependency-data"></a>Agenta Java nelze zachytit data závislostí
* Nakonfigurovali jste agenta Java pomocí následujících [konfigurace agenta Java](app-insights-java-agent.md) ?
* Ujistěte se, že že soubor jar pro Javu agenta a soubor AI Agent.xml jsou umístěny ve stejné složce.
* Ujistěte se, že závislost, kterou se pokoušíte automatické shromažďování je podporováno pro automatické shromažďování. Aktuálně podporujeme jenom MySQL, MsSQL, Oracle DB a kolekci závislostí služby Redis Cache.
* Používáte sadu JDK 1.7 nebo 1.8? Aktuálně nepodporujeme kolekce závislost na sadu JDK 9.

## <a name="no-usage-data"></a>Žádná data o využití
**Zobrazila se data o požadavků a doby odezvy, ale zobrazení stránky, prohlížeče nebo uživatelská data.**

Jste úspěšně nastavili vaší aplikace odesílat telemetrická data ze serveru. Nyní je dalším krokem je [nastavení webových stránek odesílat telemetrická data z webového prohlížeče][usage].

Případně pokud je aplikace v klientu [telefonu nebo jiné zařízení][platforms], mohla odesílat telemetrii z něj. 

Použijte stejné Instrumentační klíč pro nastavení klienta i serveru telemetrie. Data se zobrazí v stejný prostředek Application Insights a budete moct korelovat události z klienta a serveru.


## <a name="disabling-telemetry"></a>Vypnutí telemetrie
**Jak lze zakázat shromažďování telemetrie?**

V kódu:

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**Nebo** 

Aktualizujte soubor ApplicationInsights.xml (ve složce prostředky ve vašem projektu). Přidejte následující do kořenového uzlu:

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

Pomocí metody XML, je nutné restartování aplikace při změně hodnoty.

## <a name="changing-the-target"></a>Změna cíle
**Změna které prostředků Azure Můj projekt odesílá data do**

* [Získejte klíč instrumentace nového prostředku.][java]
* Pokud jste přidali Application Insights do projektu pomocí sady Azure Toolkit pro Eclipse, klikněte pravým tlačítkem na webový projekt, vyberte **Azure**, **konfigurovat Application Insights**a změníte klíč.
* Instrumentační klíč má nakonfigurovaný jako proměnné prostředí, aktualizujte hodnotu proměnné prostředí s nový Instrumentační klíč.
* V opačném případě aktualizujte klíč soubor ApplicationInsights.xml do složky prostředky ve vašem projektu.

## <a name="debug-data-from-the-sdk"></a>Dat ze sady SDK pro ladění

**Jak lze zjistit činnosti sady SDK?**

Chcete-li získat další informace o tom, co se děje v rozhraní API, přidejte `<SDKLogger/>` do kořenového uzlu souboru ApplicationInsights.xml konfigurace.

Také můžete dát pokyn protokolovací nástroj pro výstup do souboru:

```XML

    <SDKLogger type="FILE">
      <enabled>True</enabled>
      <UniquePrefix>JavaSDKLog</UniquePrefix>
    </SDKLogger>
```

Soubory lze nalézt v `%temp%\javasdklogs` nebo `java.io.tmpdir` v případě serveru Tomcat.


## <a name="the-azure-start-screen"></a>Na obrazovce start pro Azure
**Dívám se tam na [na webu Azure portal](https://portal.azure.com). Na mapě informace něco o mé aplikace?**

Ne, zobrazuje souhrn stavů serverů Azure po celém světě.

*Z panelu Azure start (domovskou obrazovku) jak najdu data o mé aplikaci?*

Za předpokladu, že jste [nastavte svoji aplikaci pro službu Application Insights][java], klikněte na tlačítko Procházet, vyberte Application Insights a vyberte prostředek aplikace, který jste vytvořili pro vaši aplikaci. Chcete-li získat existuje rychleji v budoucích verzích můžete připnout aplikaci na úvodní panel.

## <a name="intranet-servers"></a>Intranetových serverů
**Můžete monitorovat server v mém intranetu?**

Ano, za předpokladu, že váš server můžete odesílání telemetrických dat do portálu služby Application Insights prostřednictvím veřejného Internetu. 

V bráně firewall možná budete muset otevřít porty TCP 80 a 443 pro odchozí provoz pro adresu dc.services.visualstudio.com a f5.services.visualstudio.com.

## <a name="data-retention"></a>Uchovávání dat
**Jak dlouho se data uchovávají v portálu? Je bezpečný?**

Zobrazit [uchovávání dat a ochrany osobních údajů][data].

## <a name="debug-logging"></a>Protokolování ladění
Application Insights využívá `org.apache.http`. To je přemístěn do Application Insights core kromě souborů JAR v rámci oboru názvů `com.microsoft.applicationinsights.core.dependencies.http`. Díky Application Insights pro zpracování scénářů, kde různých verzí stejného `org.apache.http` existovat v základu kódu. 

>[!NOTE]
>Pokud povolíte úrovně protokolování ladění pro všechny obory názvů v aplikaci, ji bude respektovat všechny spuštěné moduly včetně `org.apache.http` přejmenovaný `com.microsoft.applicationinsights.core.dependencies.http`. Application Insights nebude možné použít filtrování těchto volání, protože se právě provádí volání protokolu knihovnou Apache. Úroveň protokolování ladění vytvořit značné množství dat protokolu a nedoporučuje se používat pro aktivní produkční instance.


## <a name="next-steps"></a>Další postup
**Nastavit Application Insights pro aplikace my server Java. Co dalšího můžu dělat?**

* [Monitorování dostupnosti webových stránek][availability]
* [Sledování používání webových stránek][usage]
* [Sledování využití a Diagnostika problémů v aplikacích zařízení][platforms]
* [Napsat kód pro sledování využití aplikací][track]
* [Zaznamenání diagnostických protokolů][javalogs]

## <a name="get-help"></a>Podpora
* [Stack Overflow](http://stackoverflow.com/questions/tagged/ms-application-insights)
* [Založte problém na Githubu](https://github.com/Microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[data]: app-insights-data-retention-privacy.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[platforms]: app-insights-platforms.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-javascript.md

