---
title: Řešení potíží s Application Insights ve webovém projektu Java
description: Průvodce odstraňováním potíží s – monitorování provozu aplikace v jazyce Java pomocí Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: ef602767-18f2-44d2-b7ef-42b404edd0e9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mbullwin
ms.openlocfilehash: 6b3205603b91077ca2c3226dcb78589de37d15cf
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Řešení potíží a otázky a odpovědi v nástroji Application Insights
Dotazy nebo problémy s [Azure Application Insights v jazyce Java][java]? Zde jsou některé tipy.

## <a name="build-errors"></a>Chyby sestavení
**V Eclipse nebo Intellij Idea, při přidání Application Insights SDK prostřednictvím Maven nebo Gradle zobrazuje se chyby ověření sestavení nebo kontrolního součtu.**

* Pokud závislost <version> element je pomocí vzoru se zástupnými znaky (například (Maven) `<version>[2.0,)</version>` nebo (Gradle) `version:'2.0.+'`), zkuste místo toho zadat konkrétní verzi jako `2.0.1`. Najdete v článku [poznámky k verzi](https://github.com/Microsoft/ApplicationInsights-Java/releases) na nejnovější verzi.

## <a name="no-data"></a>Žádná data
**I přidat Application Insights úspěšně a spustil mé aplikace, ale nikdy, uloží se data na portálu.**

* Počkejte několik minut a klikněte na tlačítko Aktualizovat. Pravidelně grafy sami obnovit, ale můžete taky aktualizovat ručně. Interval aktualizace závisí na časové rozmezí grafu.
* Zkontrolujte, že už máte klíč instrumentace definované v souboru ApplicationInsights.xml (ve složce prostředky ve vašem projektu) nebo nakonfigurovaný jako proměnné prostředí.
* Ověřte, zda je žádné `<DisableTelemetry>true</DisableTelemetry>` uzlu v souboru xml.
* V bráně firewall můžete chtít otevřít porty TCP 80 a 443 pro odchozí přenosy na adresu dc.services.visualstudio.com. Najdete v článku [úplný seznam výjimky brány firewall](app-insights-ip-addresses.md)
* Ve službě Microsoft Azure spustit Tabule, podívejte se na mapě služby stavu. Pokud jsou některé výstrahy indikace, počkejte, dokud se změnil na OK a pak zavřete a znovu otevřete okně vaší aplikace Application Insights.
* Povolení protokolování v okně konzoly IDE přidáním `<SDKLogger />` prvek v rámci kořenového uzlu souboru ApplicationInsights.xml (ve složce prostředky ve vašem projektu) a zkontrolujte položky, kterými AI: informace o/varování nebo chyby pro všechny podezřelé protokoly.
* Ujistěte se, že správný soubor ApplicationInsights.xml byl úspěšně načten Java SDK prohlížením výstup zprávy v konzole pro příkaz "konfigurační soubor byl úspěšně nalezl".
* Pokud není nalezen konfigurační soubor, zkontrolujte zprávy výstup zobrazíte, kde má být vyhledán do konfiguračního souboru pro a ujistěte se, že soubor ApplicationInsights.xml nachází v jednom z těchto umístění vyhledávání. Jako existuje pravidlo můžete umístit do konfiguračního souboru téměř Application Insights SDK JARs. Příklad: v Tomcat, to znamená složce webové-INF/třídy. Během vývoje můžete umístit soubor ApplicationInsights.xml do složky prostředky webového projektu.
* Taky podívejte se prosím na [Githubu problémy stránky](https://github.com/Microsoft/ApplicationInsights-Java/issues) známé problémy s SDK.
* Ověřte, zda použít stejnou verzi nástroje Application Insights jádra, webové aplikace, agent a protokolování appenders předejdete žádné problémy s verzí ke konfliktu.

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>Mohu použít chcete zobrazit data, ale byla zastavena
* Zkontrolujte [stav blog](http://blogs.msdn.com/b/applicationinsights-status/).
* Jste nedosáhli vaše měsíční kvóta datových bodů? Otevřete nastavení nebo kvóty a cena chcete zjistit. Pokud ano, můžete upgradovat plán nebo platit dodatečnou kapacitu. Najdete v článku [ceny schéma](https://azure.microsoft.com/pricing/details/application-insights/).
* Jste nedávno přešli vaší SDK? Ujistěte se, že pouze JAR jedinečný SDK jsou k dispozici v adresáři projektu. Nesmí existovat dvě různé verze sady SDK, které jsou dispozici.
* Hledáte na správný zdroj AI? Prosím odpovídat iKey vaší aplikace na prostředek, kde se očekává telemetrie. Měly by být stejné.

#### <a name="i-dont-see-all-the-data-im-expecting"></a>Všechna data, která se byla očekávána se nezobrazí
* Otevřete využití a odhadované náklady na stránku a zkontrolujte jestli [vzorkování](app-insights-sampling.md) je v provozu. (přenos 100 % znamená, že vzorkování není v provozu.) Službu Application Insights můžete nastavit tak, aby přijímal pouze část telemetrická data přenášená z vaší aplikace. To pomáhá při synchronizaci v rámci vaší měsíční kvóta telemetrie. 
* Máte SDK vzorkování zapnutá? Pokud ano, data by vzorkovat rychlostí zadaný u všech příslušných typů.
* Běží starší verze sady Java SDK? Počínaje verzí 2.0.1, jsme zavedli odolnost proti chybám mechanismus zpracování přerušované sítě a selhání back-end, jakož i trvalosti dat na místní jednotky.
* Jsou získávání omezené z důvodu nadměrného telemetrie? Pokud zapnete informace o protokolování, zobrazí se protokol zpráva "Aplikace je omezen". Naše současný limit je 32 kB telemetrie položek za sekundu.

### <a name="java-agent-cannot-capture-dependency-data"></a>Agenta Java nemůže zaznamenat data závislostí
* Jste nakonfigurovali agenta Java pomocí následujících [konfigurace agenta Java](app-insights-java-agent.md) ?
* Ujistěte se, že jar agenta java a soubor AI Agent.xml jsou umístěny ve stejné složce.
* Ujistěte se, že závislosti, které se pokoušíte automatické shromažďování je podporována pro kolekci automaticky. Právě jsme podporují jenom MySQL, MsSQL, Oracle DB a kolekce závislost Redis Cache.
* Používáte JDK 1.7 nebo 1.8? Aktuálně nepodporujeme kolekce závislost na JDK 9.

## <a name="no-usage-data"></a>Žádná data o využití
**Zobrazuje, data o žádosti a doby odezvy, ale žádné zobrazení stránky, prohlížeč nebo uživatelská data.**

Jste úspěšně nastavili aplikace k odesílání telemetrie ze serveru. Teď je dalším krokem je [nastavení webové stránky k odesílání telemetrie z webového prohlížeče][usage].

Případně pokud se váš klient je aplikace v [telefonu nebo jiné zařízení][platforms], mohla odesílat telemetrii z ní. 

Použijte stejný klíč instrumentace nastavit telemetrie klient i server. Data se zobrazí v rámci stejné prostředku Application Insights a budete moct korelovat události z klienta a serveru.


## <a name="disabling-telemetry"></a>Vypnutí telemetrie
**Jak můžete zakázat telemetrii kolekce?**

V kódu:

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**Or** 

Aktualizujte soubor ApplicationInsights.xml (ve složce prostředky ve vašem projektu). Přidejte následující do kořenového uzlu:

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

Pomocí metody XML, musíte aplikaci restartovat při změně hodnota.

## <a name="changing-the-target"></a>Změna cíle
**Jak můžete změnit který Azure prostředek projektu odešle data?**

* [Získejte klíč instrumentace nového prostředku.][java]
* Pokud jste přidali Application Insights do projektu pomocí sady nástrojů pro Azure pro prostředí Eclipse, klikněte pravým tlačítkem na webový projekt, vyberte **Azure**, **konfigurovat Application Insights**a změňte klíč.
* Klíč instrumentace nakonfiguroval jako proměnné prostředí, aktualizujte hodnotu proměnné prostředí s novou iKey.
* V opačném aktualizujte klíč v ApplicationInsights.xml ve složce prostředky ve vašem projektu.

## <a name="debug-data-from-the-sdk"></a>Ladění data ze sady SDK

**Jak můžete zjistit, co je to sada SDK?**

Chcete-li získat další informace o co se děje v rozhraní API, přidejte `<SDKLogger/>` do kořenového uzlu souboru ApplicationInsights.xml konfigurace.

Můžete také určit, aby protokoly pro výstup do souboru:

```XML

    <SDKLogger type="FILE">
      <enabled>True</enabled>
      <UniquePrefix>JavaSDKLog</UniquePrefix>
    </SDKLogger>
```

Soubory lze nalézt v `%temp%\javasdklogs` nebo `java.io.tmpdir` v případě Tomcat server.


## <a name="the-azure-start-screen"></a>Na obrazovce Azure start
**Dívám se na [portálu Azure](https://portal.azure.com). Mapy chci se dozvědět něco o mé aplikaci?**

Ne, zobrazuje stav servery Azure po celém světě.

*Z Azure počáteční Tabule (domovskou obrazovku) jak lze najít data o mé aplikaci?*

Za předpokladu, že jste [nastavit aplikaci pro službu Application Insights][java], klikněte na tlačítko Procházet, vyberte Application Insights a vyberte prostředek aplikace, který jste vytvořili pro vaši aplikaci. Chcete-li získat existuje rychlejší v budoucnosti budete moct připnout aplikaci Tabule start.

## <a name="intranet-servers"></a>Intranetové servery
**Můžete sledovat server v mém intranetu?**

Ano, pokud že váš server mohla odesílat telemetrii do portálu služby Application Insights prostřednictvím veřejného Internetu. 

V bráně firewall můžete chtít otevřít porty TCP 80 a 443 pro odchozí přenosy na adresu dc.services.visualstudio.com a f5.services.visualstudio.com.

## <a name="data-retention"></a>Uchovávání dat
**Jak dlouho se data uchovávají v portálu? Je bezpečné?**

V tématu [uchovávání dat a ochrana osobních údajů][data].

## <a name="debug-logging"></a>Ladění protokolování
Application Insights používá `org.apache.http`. To je přemístění v rámci Application Insights základní JAR pod oborem názvů `com.microsoft.applicationinsights.core.dependencies.http`. To umožňuje Application Insights pro zpracování scénáře, kde je to různé verze stejného `org.apache.http` existovat v základu kódu. 

>[!NOTE]
>Pokud povolíte úroveň protokolování ladění pro všechny obory názvů v aplikaci, bude uplatněn ve všech spuštěných modulů, včetně `org.apache.http` přejmenoval `com.microsoft.applicationinsights.core.dependencies.http`. Application Insights, nebudete moci použít filtrování pro těchto volání, protože probíhá Přišla žádost protokolu pomocí Apache knihovny. Úroveň protokolování ladění vytvořit značné množství dat protokolu a nedoporučuje pro produkční za provozu instance.


## <a name="next-steps"></a>Další postup
**Mám nastavit Application Insights pro aplikace my server Java. Kde můžou dělat?**

* [Monitorování dostupnosti webových stránek][availability]
* [Sledování využití webové stránky][usage]
* [Sledování využití a diagnostikovat problémy ve svých aplikacích zařízení][platforms]
* [Zápis kódu pro sledování využití vaší aplikace][track]
* [Zaznamenat diagnostických protokolů][javalogs]

## <a name="get-help"></a>Podpora
* [Stack Overflow](http://stackoverflow.com/questions/tagged/ms-application-insights)
* [Soubor problém na Githubu](https://github.com/Microsoft/ApplicationInsights-Java/issues)

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[data]: app-insights-data-retention-privacy.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[platforms]: app-insights-platforms.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-javascript.md

