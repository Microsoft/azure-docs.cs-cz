---
title: Inteligentní zjišťování – anomálie selhání ve službě Application Insights | Dokumentace Microsoftu
description: Vás upozorní na neobvyklé změny počet neúspěšných žádostí do vaší webové aplikace a poskytuje diagnostické analýzu. Je potřeba žádná konfigurace.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/18/2018
ms.reviewer: yossiy
ms.author: mbullwin
ms.openlocfilehash: 901e12e989bb5a6f18c79e086357624648b1b7b9
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54028216"
---
# <a name="smart-detection---failure-anomalies"></a>Inteligentní zjišťování – anomálie selhání
[Application Insights](../../application-insights/app-insights-overview.md) automaticky upozorní téměř v reálném čase, zda prostředích vašich webových aplikací neobvykle zvýší počet neúspěšných žádostí. Zjistí neobvyklý nárůst míry požadavků protokolu HTTP nebo volání závislostí, které se ohlásí jako neúspěšný. Pro požadavků neúspěšných požadavků jsou obvykle s kódy odpovědí 400 nebo vyšší. Umožňují posuzovat a diagnostikovat potíže, analýzu povaze chyby a související telemetrii najdete v oznámení. Existují také odkazy na portálu služby Application Insights pro další diagnostiku. Funkce potřebuje žádné instalace ani konfigurace, protože používá algoritmy strojového učení k předpovědi normální míra neúspěchů.

Tato funkce funguje pro jazyk Java a ASP.NET webových aplikací hostovaných v cloudu nebo na vašich vlastních serverech. Funguje i pro každou aplikaci, která generuje telemetrická data požadavku nebo závislost – například pokud máte role pracovního procesu, která volá [TrackRequest()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) nebo [TrackDependency()](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

Po nastavení [Application Insights pro váš projekt](../../application-insights/app-insights-overview.md), a pokud vaše aplikace generuje určité minimální množství telemetrických dat, inteligentní detekci anomálií selhání trvá další normálního chování aplikace, než bude 24 hodin Přepnout a odesílání oznámení.

Tady je ukázka upozornění.

![Ukázkové upozornění inteligentního zjišťování zobrazující analýzy cluster po selhání](./media/proactive-failure-diagnostics/013.png)

> [!NOTE]
> Ve výchozím nastavení získáte kratší formát e-mailu než v tomto příkladu. Ale můžete [přepnout na tento podrobný formát](#configure-alerts).
>
>

Všimněte si, že musíte:

* Chybovost v porovnání s normální chování.
* Kolik uživatelů se TOP týká – abyste věděli, kolik si dělat starosti.
* Charakteristické model přidružený k selhání. V tomto příkladu je konkrétní odezvy kód, název požadavku (operace) a verze aplikace. Která okamžitě zjistíte, kde začít hledat ve vašem kódu. Další možnosti může být konkrétní operační systém prohlížeč nebo klienta.
* Výjimky, trasování protokolu a chyb závislostí (databází nebo dalších externích součástí), který se přidruží charakterizované selhání.
* Odkazy přímo na relevantní hledání na telemetrická data ve službě Application Insights.

## <a name="benefits-of-smart-detection"></a>Výhody inteligentního zjišťování
Běžné [upozornění na metriku](../../azure-monitor/app/alerts.md) říct, pravděpodobně došlo k potížím. Ale inteligentního zjišťování spustí diagnostické práce za vás provádí spoustu analýzy, které by jinak museli dělat sami. Získáte výsledky elegantně zabaleny, pomáhá zajistit, abyste se rychle dostanete se k problému.

## <a name="how-it-works"></a>Jak to funguje
Inteligentní zjišťování monitoruje telemetrická data přijatá z vaší aplikace, zejména chybovost. Toto pravidlo se počítá počet požadavků, pro kterou `Successful request` vlastnost má hodnotu false a počet závislosti volání pro kterou `Successful call` vlastnost má hodnotu false. Pro žádosti, ve výchozím nastavení `Successful request == (resultCode < 400)` (Pokud jste napsali vlastní kód pro [filtr](../../azure-monitor/app/api-filtering-sampling.md#filtering) nebo generovat vlastní [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) volání). 

Výkon vaší aplikace je typický vzor chování. Některé požadavky nebo volání závislostí budou náchylnější k selhání než jiné; a celková míra selhání můžou směřovat stoupajícím zatížením. Inteligentní zjišťování využívá strojové učení k vyhledání těchto anomálie.

Jak telemetrická data pochází z vaší webové aplikace do Application Insights, inteligentní zjišťování porovná aktuální chování s vzory za posledních několik dnů. Pokud je abnormální nárůst chybovosti porovnáním s předchozím výkonu, analýzu se aktivuje.

Při spuštění analýzy provádí služba analýzy clusteru neúspěšných požadavků a pokouší se určit vzorek hodnot, které charakterizují chyby. V předchozím příkladu analýzy zjistil, že většina selhání se týkají výsledné kódu, žádost o název, hostitel adresy URL serveru a role instance. Naopak analýzy zjistil, že vlastnost operační systém klienta je distribuována na více hodnot, a proto není uvedený.

Pokud vaše služba se neinstrumentují službou těchto volání telemetrie, analyzátor hledá výjimky a chyby závislosti, které jsou propojeny s požadavky v clusteru, který má identifikovali, společně s příkladem všech protokolů trasování tyto žádosti spotřebují.

Výsledný analýzy vám zaslán jako upozornění, pokud jste ji nakonfigurovali nikoli k.

Podobně jako [výstrahy, je nastavit ručně](../../azure-monitor/app/alerts.md), můžete zkontrolovat stav výstrahy a nakonfigurovat jej v okně oznámení vašemu prostředku Application Insights. Ale na rozdíl od ostatních výstrah, není nutné vytvořit nebo nakonfigurovat inteligentní zjišťování. Pokud chcete, můžete jej zakázat nebo změnit jeho cíl e-mailové adresy.

### <a name="alert-logic-details"></a>Podrobnosti o logika upozornění

Výstrahy jsou aktivovány naše speciální algoritmu strojového učení, takže jsme nelze sdílet podrobnosti implementace přesné. To ale nutné dodat rozumí tomu, že někdy potřebujete další informace o tom, jak funguje logiku. K základním faktorům, které je vyhodnocován pro určení, pokud by měl být výstraha jsou: 

* Analýza selhání procento požadavky a závislosti v postupné časovým intervalem 20 minut.
* Porovnání selhání procento posledních 20 minut rychlost v posledních 40 minut a posledních sedmi dnů a hledáte významné odchylky, které překračují X a časy této směrodatnou odchylku.
* Pomocí adaptivního mezní procento minimální selhání, které se liší na základě aplikace objemu požadavků nebo závislostí.

## <a name="configure-alerts"></a>Konfigurace upozornění
Můžete zakázat inteligentní zjišťování, změnit příjemců e-mailu, vytvořte webhook nebo vyjádřit výslovný souhlas s podrobnější oznámení.

Otevření stránky s upozorněními. Anomálie selhání je zahrnut spolu s všechny výstrahy, které jste ručně nastavili, a uvidíte, jestli je aktuálně ve stavu upozornění.

![Na stránce Přehled klikněte na dlaždici upozornění. Nebo na libovolné stránce metriky, klikněte na tlačítko výstrahy.](./media/proactive-failure-diagnostics/021.png)

Klikněte na výstrahu, kterou chcete nakonfigurovat.

![Konfigurace](./media/proactive-failure-diagnostics/032.png)

Všimněte si, že inteligentní zjišťování můžete zakázat, ale nelze ji odstranit, (nebo vytvořte jiný).

#### <a name="detailed-alerts"></a>Podrobné výstrahy
Pokud zvolíte možnost "Získat podrobnější diagnostiku" e-mail bude obsahovat další diagnostické informace. Někdy budete mít k diagnostice problému jenom z dat v e-mailu.

Není lehké riziko, že podrobnější výstraha může obsahovat citlivé informace, protože obsahuje výjimkách a trasovací zprávy. Ale to by mohlo dojít pouze pokud váš kód by mohlo znamenat citlivé informace do těchto zpráv.

## <a name="triaging-and-diagnosing-an-alert"></a>Třídění a Diagnostika výstrahu
Výstraha naznačuje, že se zjistil neobvyklý nárůst míry neúspěšných žádostí. Je pravděpodobné, že je nějaký problém s vaší aplikace nebo její prostředí.

Z procento žádostí a počet ovlivněných uživatelů můžete rozhodnout, jak urgentní je problém. V předchozím příkladu je míra selhání 22.5 % porovná s normální mírou 1 %, znamená, že něco špatně se děje. Na druhé straně pouze 11 uživatele vliv. Pokud se vaše aplikace, by mohli posoudit, jak závažná je.

V mnoha případech bude možné k diagnostice problému rychle z název žádosti, výjimky, závislosti selhání a trasování data k dispozici.

Existují některé další příčiny. Například míra selhání závislostí v tomto příkladu je stejný jako frekvence výjimek (89.3 %). To naznačuje, že výjimka nastane přímo z chyb závislostí – díky tomu získáte jasno, kde začít hledat ve vašem kódu.

Dále prozkoumat odkazy v každé části přejdete přímo do [stránka hledání](../../azure-monitor/app/diagnostic-search.md) vyfiltrovaný tak, aby příslušné požadavky, výjimky, závislosti nebo trasování. Nebo můžete otevřít [webu Azure portal](https://portal.azure.com), přejděte do prostředku Application Insights pro vaši aplikaci a otevře se okno selhání.

V tomto příkladu kliknutím na odkaz 'Zobrazit podrobnosti o neúspěšných závislostí' Otevře se okno hledání Application Insights. Zobrazí příkaz SQL, který obsahuje příklad z hlavních příčin: Hodnoty Null byly k dispozici na povinná a neprošel ověřením při ukládání operace.

![Diagnostické vyhledávání](./media/proactive-failure-diagnostics/051.png)

## <a name="review-recent-alerts"></a>Nedávné výstrahy můžete zkontrolovat

Klikněte na tlačítko **inteligentního zjišťování** zobrazíte nejnovější oznámení:

![Souhrn výstrah podle](./media/proactive-failure-diagnostics/070.png)


## <a name="whats-the-difference-"></a>Jaký je rozdíl...
Inteligentní detekce anomálie selhání doplňuje dalších podobných ale různé prvky služby Application Insights.

* [Upozornění na metriku](../../azure-monitor/app/alerts.md) vámi nastavené a můžete monitorovat širokou řadu metrik, jako jsou vytížení procesoru, požadavků, doby načítání stránek a tak dále. Můžete využít k by vás varovala, například pokud budete muset přidat další prostředky. Oproti tomu inteligentní detekci anomálií selhání pokrývá malé řadu důležité metriky (aktuálně jenom neúspěšné frekvence požadavků), navržená tak, aby byli informováni vždy, můžete v téměř reálném čase způsobem, jakmile se vaše webová aplikace neprošla požadavku zvyšuje rychlost výrazně ve srovnání s webovou aplikaci normální chování.

    Inteligentní zjišťování automaticky přizpůsobí prahové hodnoty v odpovědi na obvyklé podmínky.

    Inteligentní zjišťování spustí diagnostické práce za vás.
* [Inteligentní detekce anomálie výkonu](proactive-performance-diagnostics.md) také používá strojové inteligence ke zjištění neobvyklé vzory v metriky a není nutná žádná konfigurace sami. Ale na rozdíl od inteligentního zjišťování anomálií selhání účelu inteligentního zjišťování anomálie výkonu je vyhledání segmenty vaší potrubí využití, které může být chybně dodávat – například podle konkrétní stránky na konkrétní typ prohlížeče. Analýza se provádí denně a pokud se nenajde žádné výsledky, je pravděpodobně mnohem méně naléhavé než výstrahu. Naopak analýzy pro anomálie selhání je průběžně provedla příchozích telemetrických dat a se pak zobrazí upozornění během několika minut, pokud míra selhání serveru jsou větší, než se očekávalo.

## <a name="if-you-receive-a-smart-detection-alert"></a>Pokud se zobrazí upozornění inteligentního zjišťování
*Proč jste dostali Tato výstraha?*

* Zjistili jsme neobvyklý nárůst míry neúspěšných žádostí ve srovnání s normální základních hodnot v předchozím období. Po dokončení analýzy selhání a související telemetrii myslíme si, že dojde k nějakému problému, který byste se podívat do.

*Znamená oznámení, že mám jednoznačně problém?*

* Snažíme se upozornění na narušení aplikace nebo snížení výkonu, ale pouze plně rozumíte sémantika a dopad na aplikaci nebo uživatele.

*Ano guys podíváte na moje data?*

* Ne. Tato služba je plně automatická. Pouze dostanete oznámení. Vaše data jsou [privátní](../../azure-monitor/app/data-retention-privacy.md).

*Budu muset předplatit Tato výstraha?*

* Ne. Každá aplikace, že odešle požadavek telemetrie se pravidlo upozornění inteligentního zjišťování.

*Je možné zrušit nebo dostávat oznámení, namísto toho odesílána kolegové?*

* Ano, pravidla v upozornění, klikněte na pravidlo inteligentní zjišťování k jeho konfiguraci. Můžete zakázat upozornění nebo změnit příjemce pro výstrahy.

*Mohu ztratit e-mailu. Kde najdu oznámení na portálu?*

* V protokolech aktivit. V Azure otevřete prostředek Application Insights pro vaši aplikaci a pak vyberte protokoly aktivit.

*Některé výstrahy jsou o známých problémech a nebudete chtít přijímat.*

* Potlačení výstrah máme v backlogu.

## <a name="next-steps"></a>Další postup
Tyto diagnostické nástroje umožňují kontrolovat telemetrie z vaší aplikace:

* [Průzkumník metrik](../../azure-monitor/app/metrics-explorer.md)
* [Průzkumník služby Search](../../azure-monitor/app/diagnostic-search.md)
* [Analýza – výkonný dotazovací jazyk](../../azure-monitor/log-query/get-started-portal.md)

Inteligentní detekce je úplně automatický. Ale možná chcete nastavit některé další oznámení?

* [Ručně konfigurované metriky výstrahy](../../azure-monitor/app/alerts.md)
* [Testy dostupnosti webu](../../azure-monitor/app/monitor-web-app-availability.md)
