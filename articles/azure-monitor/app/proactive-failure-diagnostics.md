---
title: Inteligentní zjišťování – anomálie selhání v Application Insights | Microsoft Docs
description: Upozorňuje na neobvyklé změny v rychlosti neúspěšných požadavků na webovou aplikaci a poskytuje analýzu diagnostiky. Není nutná žádná konfigurace.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 12/18/2018
ms.reviewer: yossiy
ms.openlocfilehash: f8b8318a16b36593d2fbaf08bcbc19156dc96006
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820582"
---
# <a name="smart-detection---failure-anomalies"></a>Inteligentní zjišťování – anomálie selhání
[Application Insights](../../azure-monitor/app/app-insights-overview.md) vás automaticky upozorní téměř v reálném čase, pokud vaše webová aplikace působí abnormální nárůst frekvence neúspěšných žádostí. Detekuje neobvyklé zvýšení rychlosti požadavků HTTP nebo volání závislostí, která jsou hlášena jako neúspěšná. U požadavků se neúspěšné žádosti obvykle používají s kódy odpovědí 400 nebo vyšší. Abychom vám pomohli posoudit a diagnostikovat problém, je k dispozici analýza vlastností selhání a související telemetrie v oznámení. K dispozici jsou také odkazy na portál Application Insights pro další diagnostiku. Tato funkce nevyžaduje žádné nastavení ani konfiguraci, protože používá algoritmy strojového učení pro předpověď běžné míry selhání.

Tato funkce funguje pro libovolnou webovou aplikaci, která je hostována v cloudu, nebo na vašich vlastních serverech, která generuje telemetrie požadavků nebo závislostí – například pokud máte roli pracovního procesu, která volá [TrackRequest ()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) nebo [TrackDependency ()](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency).

Po nastavení [Application Insights projektu](../../azure-monitor/app/app-insights-overview.md)a za předpokladu, že vaše aplikace generuje určité minimální množství telemetrie, inteligentní detekce anomálií při selhání trvá 24 hodin, aby se seznámili s běžným chováním aplikace, než se přepne na a může odesílat. generoval.

Zde je ukázka výstrahy.

![Ukázka výstrahy inteligentního zjišťování, která zobrazuje analýzu clusteru okolo selhání](./media/proactive-failure-diagnostics/013.png)

> [!NOTE]
> Ve výchozím nastavení získáte kratší formát e-mailu než v tomto příkladu. Můžete ale [Přepnout do tohoto podrobného formátu](#configure-alerts).
>
>

Všimněte si, že vám oznamuje:

* Rychlost selhání v porovnání s normálním chováním aplikace
* Kolik uživatelů je ovlivněno, takže víte, kolik jich má starosti.
* Charakteristický vzor spojený s chybami. V tomto příkladu je k dispozici konkrétní kód odpovědi, název požadavku (operace) a verze aplikace. Tím se okamžitě ukáže, kde začít hledat váš kód. Další možností může být konkrétní prohlížeč nebo klientský operační systém.
* Výjimka, trasování protokolu a selhání závislosti (databáze nebo jiné externí komponenty), které se zdají být přidruženy k chybám s formátováním.
* Odkazuje přímo na relevantní hledání telemetrie v Application Insights.

## <a name="failure-anomalies-v2"></a>Anomálie selhání v2
Nyní je k dispozici nová verze pravidla výstrahy anomálií selhání. Tato nová verze je spuštěná na nové platformě pro upozorňování Azure a přináší řadu vylepšení stávající verze.

### <a name="whats-new-in-this-version"></a>Co je nového v této verzi?
- Rychlejší zjišťování problémů
- Bohatší sada akcí – pravidlo upozornění se vytvoří s přidruženou [skupinou akcí](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) s názvem Application Insights inteligentní zjišťování, která obsahuje akce e-mailu a Webhooku, a dá se rozšířit tak, aby se při aktivaci výstrahy aktivovaly další akce.
- Další prioritní oznámení – e-mailová oznámení odesílaná z tohoto pravidla upozornění se teď standardně odesílají uživatelům přidruženým ke čtenáři monitorování a monitorování rolí přispěvatele. Další informace, které jsou k dispozici, [najdete tady](https://docs.microsoft.com/azure/azure-monitor/app/proactive-email-notification).
- Snadnější konfigurace prostřednictvím šablon ARM – viz příklad [zde](https://docs.microsoft.com/azure/azure-monitor/app/proactive-arm-config).
- Podpora běžných schémat výstrah – oznámení odesílaná z tohoto pravidla výstrahy se řídí [běžným schématem výstrah](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema).
- Šablona sjednoceného e-mailu – e-mailová oznámení z tohoto pravidla výstrahy mají konzistentní vzhled & s ostatními typy výstrah. Tato změna znamená, že možnost získat výstrahy chyby s podrobnými informacemi o diagnostice už nejsou k dispozici.

### <a name="how-do-i-get-the-new-version"></a>Návody získat novou verzi?
- Nově vytvořené Application Insights prostředky se teď zřídí s novou verzí pravidla výstrahy anomálií selhání.
- Stávající Application Insights prostředky s klasickou verzí pravidla upozornění na anomálii obdrží novou verzi, jakmile se jejich hostující předplatné migruje na novou platformu pro upozorňování v rámci [procesu odchodu klasických výstrah](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement).

> [!NOTE]
> Nová verze pravidla výstrahy anomálií při selhání zůstane zadarmo. Kromě toho jsou bezplatné i akce e-mailu a Webhooku aktivované přidruženou skupinou akcí "Application Insights inteligentní zjišťování".
> 
> 

## <a name="benefits-of-smart-detection"></a>Výhody inteligentní detekce
Běžné [výstrahy metriky s upozorněním](../../azure-monitor/app/alerts.md) , že se může jednat o problém. Inteligentní detekce ale spustí diagnostickou práci za vás a provede spoustu analýz, které byste jinak museli dělat sami. Výsledky získáte v hotovém balení, což vám pomůže rychle se dostat k hlavnímu adresáři problému.

## <a name="how-it-works"></a>Jak to funguje
Inteligentní detekce monitoruje telemetrie získanou z vaší aplikace a zejména míry selhání. Toto pravidlo spočítá počet požadavků, u kterých je vlastnost `Successful request` false, a počet volání závislostí, pro které je vlastnost `Successful call` false. Pro požadavky, ve výchozím nastavení `Successful request == (resultCode < 400)` (Pokud jste nenapsali vlastní kód pro [filtrování](../../azure-monitor/app/api-filtering-sampling.md#filtering) nebo generování vlastních volání [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) ). 

Výkon vaší aplikace má typický vzor chování. Některé požadavky nebo volání závislostí budou lépe náchylné k selhání než jiné. a celková míra selhání může vzrůstá zatížení. Inteligentní detekce vyhledá tyto anomálie pomocí strojového učení.

Protože telemetrie přichází do Application Insights z vaší webové aplikace, inteligentní detekce porovná aktuální chování se vzorem zjištěným během posledních několika dní. Pokud se v porovnání s předchozím výkonem vyskytne neobvyklé zvýšení míry selhání, aktivuje se analýza.

Když se aktivuje analýza, služba provede analýzu clusteru na neúspěšném požadavku a pokusí se identifikovat vzor hodnot, které charakterizují selhání. V předchozím příkladu se při analýze zjistilo, že většina selhání se týká konkrétního kódu výsledku, názvu žádosti, hostitele adresy URL serveru a instance role. Naproti tomu analýza zjistila, že vlastnost klientského operačního systému je distribuována přes více hodnot, takže není uvedena.

Pokud je vaše služba instrumentovaná pomocí těchto volání telemetrie, analyzátor vyhledá výjimku a chybu závislosti, která je přidružená k požadavkům v clusteru, který identifikoval, spolu s příkladem všech protokolů trasování přidružených k těmto žádostem.

Výsledná analýza se vám pošle jako výstraha, pokud jste ji nenakonfigurovali na.

Podobně jako u [výstrah, které jste nastavili ručně](../../azure-monitor/app/alerts.md), můžete zkontrolovat stav výstrahy a nakonfigurovat ji v okně výstrahy prostředku Application Insights. Ale na rozdíl od jiných výstrah nemusíte nastavovat nebo konfigurovat inteligentní zjišťování. Pokud chcete, můžete ho zakázat nebo změnit jeho cílové e-mailové adresy.

### <a name="alert-logic-details"></a>Podrobnosti logiky výstrahy

Výstrahy spouští náš proprietární algoritmus strojového učení, takže nemůžeme sdílet přesné podrobnosti implementace. Díky tomu říkáme, že někdy potřebujete vědět, jak základní logika funguje. Primární faktory, které jsou vyhodnocovány k určení, zda by měla být aktivována výstraha: 

* Analýza procenta selhání požadavků nebo závislostí v časovém intervalu po 20 minutách.
* Porovnání procenta selhání za posledních 20 minut s sazbou za posledních 40 minut a posledních sedm dní a hledání významných odchylek, které překračují standardní odchylku, se rovná hodnotě X.
* Použití adaptivního limitu pro minimální procento selhání, které se liší v závislosti na objemu požadavků a závislostí aplikace.

## <a name="configure-alerts"></a>Konfigurace upozornění
Můžete zakázat inteligentní zjišťování, změnit příjemce e-mailu, vytvořit Webhook nebo vyjádřit výslovný souhlas s podrobnějšími zprávami výstrah.

Otevřete stránku výstrahy. Anomálie selhání jsou zahrnuty spolu se všemi výstrahami, které jste nastavili ručně, a vidíte, zda je aktuálně ve stavu výstrahy.

![Na stránce Přehled klikněte na dlaždice výstrahy. Nebo na stránce s metrikami klikněte na tlačítko výstrahy.](./media/proactive-failure-diagnostics/021.png)

Kliknutím na výstrahu ji nakonfigurujte.

![Konfigurace](./media/proactive-failure-diagnostics/032.png)

Všimněte si, že můžete zakázat inteligentní zjišťování, ale nemůžete ho odstranit (nebo ho vytvořit jinak).

#### <a name="detailed-alerts"></a>Podrobné výstrahy
Pokud vyberete "získat podrobnější diagnostiku", e-mail bude obsahovat další diagnostické informace. Někdy budete moct problém diagnostikovat jenom z dat v e-mailu.

Existuje mírné riziko, že podrobnější výstraha může obsahovat citlivé informace, protože obsahuje zprávy výjimek a trasování. K tomu však dojde pouze v případě, že váš kód může do těchto zpráv dovolit citlivé informace.

## <a name="triaging-and-diagnosing-an-alert"></a>Třídění a diagnostika výstrahy
Výstraha indikuje, že byl zjištěn abnormální nárůst frekvence neúspěšných požadavků. Je pravděpodobnější, že došlo k nějakému problému s vaší aplikací nebo jeho prostředím.

V procentech požadavků a počtu ovlivněných uživatelů se můžete rozhodnout, jak naléhavě je problém. V předchozím příkladu je míra selhání 22,5% porovnávána s normální frekvencí 1%, což znamená, že probíhá něco špatného. Na druhé straně byly ovlivněny pouze 11 uživatelů. Pokud by to byla vaše aplikace, měli byste posoudit, jak závažná je.

V mnoha případech budete moct problém rychle diagnostikovat z názvu žádosti, výjimky, selhání závislosti a poskytnutých dat trasování.

Existují další pojistky. Například míra selhání závislosti v tomto příkladu je stejná jako frekvence výjimek (89,3%). To naznačuje, že výjimka vznikne přímo z důvodu selhání závislosti – poskytuje jasné představu, kde začít hledat v kódu.

Aby bylo možné prozkoumat další prozkoumání, odkazy v jednotlivých částech budou přebírat přímo na [stránku vyhledávání](../../azure-monitor/app/diagnostic-search.md) filtrovanou na příslušné žádosti, výjimku, závislost nebo trasování. Případně můžete otevřít [Azure Portal](https://portal.azure.com), přejít k prostředku Application Insights pro vaši aplikaci a otevřít okno chyby.

V tomto příkladu se kliknutím na odkaz Zobrazit podrobnosti o selháních závislostí otevře okno hledání Application Insights. Zobrazuje příkaz SQL, který obsahuje příklad hlavní příčiny: hodnoty NULL byly zadány v povinných polích a během operace uložení neprošly ověřením.

![Diagnostické vyhledávání](./media/proactive-failure-diagnostics/051.png)

## <a name="review-recent-alerts"></a>Zkontrolovat nedávné výstrahy

Kliknutím na **inteligentní zjišťování** se dostanete k nejnovější výstraze:

![Souhrn výstrah](./media/proactive-failure-diagnostics/070.png)


## <a name="whats-the-difference-"></a>Jaký je rozdíl...
Inteligentní zjišťování anomálií selhání doplňuje jiné podobné, ale odlišné funkce Application Insights.

* [Výstrahy metriky](../../azure-monitor/app/alerts.md) jsou nastavené vámi a můžou monitorovat široké spektrum metrik, jako je obsazení procesoru, počet požadavků, doba načítání stránek a tak dále. Můžete je použít k upozornění, například pokud potřebujete přidat další prostředky. Naproti tomu inteligentní detekce anomálií selhání pokrývá malý rozsah kritických metrik (aktuálně jenom frekvence neúspěšných požadavků), která vás bude informovat téměř v reálném čase, jakmile se míra neúspěšných požadavků vaší webové aplikace významně porovnává se službou Web App 's normální chování.

    Inteligentní zjišťování automaticky upravuje prahovou hodnotu v reakci na převládající podmínky.

    Inteligentní zjišťování spustí diagnostickou práci za vás.
* [Inteligentní detekce anomálií s výkonem](proactive-performance-diagnostics.md) taky využívá Machine Intelligence ke zjišťování neobvyklých vzorů ve vašich metrikách a nevyžaduje žádnou konfiguraci. Ale na rozdíl od inteligentní detekce anomálií při selhání je účel inteligentní detekce anomálií ve výkonu najít segmenty vašeho sběrného segmentu, které by mohly být chybně obsluhovány – například konkrétní stránky na konkrétního typu prohlížeče. Analýza se provádí denně a v případě, že se najde nějaký výsledek, bude pravděpodobně mnohem méně naléhavější než výstraha. Naproti tomu analýza anomálií selhání probíhá nepřetržitě na příchozí telemetrie a během několika minut budete upozorněni na to, jestli jsou míry selhání serveru větší, než se očekávalo.

## <a name="if-you-receive-a-smart-detection-alert"></a>Pokud se zobrazí výstraha inteligentního zjišťování
*Proč se mi zobrazila tato výstraha?*

* Zjistili jsme neobvyklý nárůst frekvence neúspěšných požadavků v porovnání s normálním směrným plánem předchozího období. Po analýze selhání a přidružené telemetrie se domníváme, že došlo k problému, který byste měli najít.

*Znamená to, že oznámení má I konečně problém?*

* Zkusíme upozornit na přerušení nebo snížení úrovně aplikace, ale jenom můžete plně pochopit sémantiku a dopad na aplikaci nebo uživatele.

*Proto si kyberbezpečnosti svoje údaje?*

* Ne. Služba je zcela automatická. Oznámení se zobrazí jenom vy. Vaše data jsou [soukromá](../../azure-monitor/app/data-retention-privacy.md).

*Musím se přihlásit k odběru této výstrahy?*

* Ne. Každá aplikace, která odesílá telemetrii žádostí, má pravidlo výstrahy inteligentního zjišťování.

*Můžu místo toho zrušit odběr nebo dostávat oznámení odesílaná kolegům?*

* Ano, v okně pravidla výstrah klikněte na pravidlo inteligentního zjišťování, které chcete nakonfigurovat. Můžete zakázat výstrahu nebo změnit příjemce výstrahy.

*Ztratil jsem e-mail. Kde najdu oznámení na portálu?*

* V protokolech aktivit. V Azure otevřete prostředek Application Insights pro vaši aplikaci a pak vyberte protokoly aktivit.

*Některé výstrahy se týkají známých problémů a nechci je přijímat.*

* U našich nevyřízených položek máme potlačení výstrah.

## <a name="next-steps"></a>Další kroky
Tyto diagnostické nástroje vám pomůžou zkontrolovat telemetrii z vaší aplikace:

* [Průzkumník metrik](../../azure-monitor/app/metrics-explorer.md)
* [Průzkumník vyhledávání](../../azure-monitor/app/diagnostic-search.md)
* [Analýza – výkonný dotazovací jazyk](../../azure-monitor/log-query/get-started-portal.md)

Inteligentní detekce jsou zcela automatické. Možná byste ale chtěli nastavit ještě nějaké další výstrahy?

* [Ručně nakonfigurované výstrahy metriky](../../azure-monitor/app/alerts.md)
* [Webové testy dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md)
