---
title: Správa svazku ceny a dat pro služby Azure Application Insights | Microsoft Docs
description: Správa telemetrie svazků a sledování nákladů ve službě Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mbullwin
ms.openlocfilehash: 22c8616c1585e3f728a03a794c527cb34fc0c4eb
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Správa svazku ceny a data ve službě Application Insights

Ceny pro [Azure Application Insights] [ start] je založena na datový svazek na aplikaci. Každý prostředek Application Insights je účtován jako samostatná služba a přispívá k fakturovaná částka u předplatného Azure.

Application Insights obsahuje dvě cenovou plány: Basic a Enterprise. Základní cenový plán je výchozí plán. Obsahuje všechny funkce Enterprise plánu, bez dalších poplatků. Základní plán faktur hlavně na objemu dat, který je konzumována. 

Plánu podnikového má poplatků za uzlu a každý uzel dostane denní příspěvek data. V podnikové síti ceny plán, budeme vám účtovat data požity výše zahrnuté povoleného užívání. Pokud používáte služby Operations Management Suite, měli byste vybrat plán Enterprise. 

Pokud máte dotazy týkající se fungování ceny pro službu Application Insights, můžete odeslat dotaz v našem [fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights).

## <a name="pricing-plans"></a>Cenových plánů

Aktuální ceny měny a oblasti, najdete v části [ceny Application Insights][pricing].

> [!NOTE]
> V dubnu 2018 jsme [zavedená](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) nový cenový model pro Azure monitorování. Tento model přijme jednoduchého modelu "průběžnými platbami" v celém portfoliu kompletní monitorování služby. Další informace o [nový cenový model](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), jak k [posoudit dopad přesun k tomuto modelu](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) na základě vaší způsobů využití a [postupy, který se přidá do nového modelu](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model).

### <a name="basic-plan"></a>Základní plán

Základní plán je výchozí ceny plán, když je vytvořen nový prostředek Application Insights. Základní plán je optimální pro všechny zákazníky kromě těch, kteří mají předplatné služby Operations Management Suite.

* V základní plán budou se účtovat podle datový svazek. Datový svazek je počet bajtů přijatých Application Insights telemetrie. Datový svazek se měří jako velikost nekomprimovaných balíček data JSON, který přijme Application Insights z vaší aplikace. Pro [tabulková data importovat do Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), datový svazek se měří jako velikost nekomprimovaných souborů, které se odesílají do služby Application Insights.
* Aplikace data svazku poplatky jsou nyní ohlášeny nové fakturace měření s názvem **přijímání dat** od duben 2018. Toto je nový měření sdílet monitorování technologie, jako jsou aplikace přehledy a analýzy protokolů a aktuálně probíhá název služby **App Services** (a brzy změna na **analýzy protokolů**). 
* [Živý datový proud metriky](app-insights-live-stream.md) dat není počítá o cenách pro účely.
* [Průběžné export](app-insights-export-telemetry.md) a [konektor Azure Log Analytics](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409) v základní plán od duben 2018 jsou k dispozici bez dalších poplatků.

### <a name="enterprise-plan-and-operations-management-suite-subscription-entitlements"></a>Plánování Enterprise a oprávnění předplatné služby Operations Management Suite

Zákazníci, kteří zakoupili E1 Operations Management Suite a E2 můžete získat Application Insights Enterprise jako další komponentu bez dalších poplatků jako [dříve oznámeno](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/). Jednotky služby Operations Management Suite E1 a E2 konkrétně obsahuje nároku na jeden uzel plánu Application Insights Enterprise. Každý uzel Application Insights zahrnuje až 200 MB dat požity za den (oddělené od analýzy protokolů přijímání dat), s dobou uchování dat 90 dnů bez dalších poplatků. Plán je popsaná v další podrobné později v článku. 

Vzhledem k tomu, že tento plán platí pouze pro zákazníky s předplatné služby Operations Management Suite, zákazníky, kteří nemají předplatné služby Operations Management Suite není k dispozici možnost vybrat tento plán.

> [!NOTE]
> Aby se zajistilo, že dostanete tento nárok, musí být vašich prostředků Application Insights v podnikové síti ceny plánu. Tato oprávnění se vztahuje pouze jako uzly. Application Insights prostředky v základní plán si uvědomte si, nějaké výhody. Tato oprávnění nejsou viditelná odhadované náklady ukazuje **využití a odhadované náklady** podokně. Pokud přesunete do nové Azure monitorování cenový model v dubnu 2018 předplatné, je základní plán také k dispozici pouze plán. Přesunutí předplatné vás nový Azure monitorování cenový model není vhodné, pokud máte předplatné služby Operations Management Suite.

Další informace o plánu podnikového najdete v tématu [Enterprise podrobnosti o cenách](app-insights-pricing-enterprise-details.md).

### <a name="multi-step-web-tests"></a>Vícekrokové webové testy

[Vícekrokové webové testy](app-insights-monitor-web-app-availability.md#multi-step-web-tests) zpoplatněná dalších poplatků. Vícekrokové webové testy jsou webové testy, které provádějí posloupnost akcí.

Je bezplatná samostatné pro *příkaz ping testy* jedné stránky. Telemetrie z testů pomocí příkazu ping a testy vícekrokový je účtován stejná jako ostatní telemetrie z vaší aplikace.

## <a name="review-pricing-plans-and-estimate-costs"></a>Zkontrolujte cenovou plány a odhadnout náklady

Application Insights umožňuje snadno pochopit cenovou plány, které jsou k dispozici, a jaké náklady pravděpodobně byl založen na poslední vzorce používání. Chcete-li začít, na portálu Azure pro prostředek Application Insights, přejděte na **využití a odhadované náklady** podokně:

![Zvolte ceny](./media/app-insights-pricing/pricing-001.png)

A. Zkontrolujte datový svazek pro daný měsíc. To zahrnuje všechna data, která obdržel a uchovávají (po všech [vzorkování](app-insights-sampling.md)) ze serveru a klientské aplikace a z testy dostupnosti.  
B. Účtována samostatně se provádí [vícekrokové webové testy](app-insights-monitor-web-app-availability.md#multi-step-web-tests). (To neobsahuje testy jednoduché dostupnosti, které jsou součástí poplatků svazku data.)  
C. Zobrazení dat svazku trendech za poslední měsíc.  
D. Povolení přijímání dat [vzorkování](app-insights-sampling.md).   
E. Nastavte denní svazku cap data.  

Application Insights poplatky budou přidány do faktury Azure. Zobrazí podrobnosti o vašem Azure účtovat pošle v **fakturace** části portálu Azure, nebo v [portál Azure fakturace](https://account.windowsazure.com/Subscriptions). 

![V nabídce vlevo vyberte fakturace](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Rychlost přenosu dat
Objem dat, která posíláte je omezena třemi způsoby:

* **Vzorkování**: vzorkování můžete použít ke snížení objemu telemetrie, který se odesílá z vašeho serveru a klienta aplikace s minimální narušení metrik. Vzorkování je primárním nástrojem, které můžete použít k vyladění množství dat, která posíláte. Další informace o [vzorkování funkce](app-insights-sampling.md). 
* **Denní cap**: Když vytvoříte prostředek Application Insights na portálu Azure, denní cap nastavena na 100 GB a den. Když vytvoříte prostředek Application Insights v sadě Visual Studio, výchozí hodnota je malý (pouze 32,3 MB/den). Denní cap výchozí nastavení je usnadnit testování. Předpokládá se, že uživatel vyvolá denního limitu před nasazením aplikace do produkčního prostředí. 

    Maximální hodnota je 1 000 GB a den, pokud si vyžádáte vyšší maximum pro aplikace vysokým provozem. 

    Pozor použijte, pokud nastavíte denního limitu. Vašich představ by měla být k *nikdy dosáhl denního limitu*. Při dosažení denního limitu, dojde ke ztrátě dat pro zbývající část dne, a nelze monitorovat aplikace. Můžete změnit denní zakončení **denního limitu svazku** možnost. Této možnosti v můžete přistupovat **využití a odhadované náklady** podokně (to je podrobněji popsané v dále v tomto článku).
    Odebrali jsme omezení na některé typy předplatné, které mají kreditu, který nelze použít pro službu Application Insights. Dříve Pokud předplatné má limitu útraty a automaticky, dialogu denního limitu obsahuje pokyny k odstranění limitu útraty a povolit denního limitu má být aktivována nad rámec 32,3 MB a den.
* **Omezení**: omezení rychlost přenosu dat do 32 000 událostí za sekundu, průměr více než 1 minuta.

*Co se stane, když aplikace my překračuje míru omezení?*

* Objem dat, která vaše aplikace odesílá se hodnotí každou minutu. Pokud překročí rychlost za sekundu průměr za minutu, server odmítá některých požadavků. Sada SDK ukládá data do vyrovnávací paměti a potom se pokusí ji odešlete. Nárůst ho rozkládá za několik minut. Pokud vaše aplikace konzistentně odesílá data na více než míru omezení, některá data se zahodí. (ASP.NET, Java a JavaScript SDK pokuste se znovu odeslat data tímto způsobem, dalších sadách SDK může jednoduše rozevírací omezeny data.) Pokud dojde k omezení šířky pásma, upozornění oznámení vás upozorní, že k nim došlo.

*Jak poznám, kolik dat Moje aplikace odesílá?*

Zobrazíte množství dat, které vaše aplikace odesílá, můžete použít jednu z následujících možností:

* Přejděte na **využití a odhadované náklady** podokně se zobrazí denní svazku graf data. 
* V Průzkumníku metrik přidejte nový graf. Pro graf metriku, vyberte **datového bodu svazku**. Zapnout **seskupení**a pak skupiny podle **datový typ**.

## <a name="reduce-your-data-rate"></a>Snižuje míru vaše data
Zde jsou některé věci, které může provádět ke snížení datový svazek:

* Použití [vzorkování](app-insights-sampling.md). Tato technologie sníží přenosovou rychlost bez zkosení vaše metriky. Neztraťte možnost přecházet mezi související položky v hledání. V aplikacích pro server vzorkování funguje automaticky.
* [Omezit počet volání Ajax, které mohou být oznámeny](app-insights-javascript.md#detailed-configuration) v každé zobrazení stránky nebo přepínač vypnout Ajax reporting.
* [Upravit soubor ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) vypnout kolekce modulů, které nepotřebujete. Například můžete rozhodnout, jestli jsou inessential čítače výkonu nebo data závislostí.
* Rozdělení telemetrie mezi samostatnou instrumentace klíči. 
* Předem agregační metriky. Pokud volání TrackMetric umístili ve vaší aplikaci, můžete snížit provozu pomocí přetížení, které přijímá vaší výpočtu průměru a směrodatné odchylky dávky měření. Nebo můžete použít [předem totožný balíček](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="manage-the-maximum-daily-data-volume"></a>Spravovat maximální denní datový svazek

Chcete-li omezit data shromážděná můžete denního limitu svazku. Ale splnění krytky dojde ke ztrátě všech telemetrická data odesílaná z vaší aplikace pro zbývající dne. Je *není vhodné* tak, aby měl aplikace dosáhl denního limitu. Po dosažení denního limitu nemůže sledovat stav a výkon vaší aplikace.

Místo použití denního limitu svazku, použít [vzorkování](app-insights-sampling.md) k vyladění datový svazek na úroveň chcete. Potom pomocí denního limitu pouze jako "poslední možnost" v případě, že vaše aplikace neočekávaně začne odesílat mnohem vyšší objemy telemetrie.

Chcete-li změnit denního limitu v **konfigurace** části prostředku Application Insights v **využití a odhadované náklady** podokně, vyberte **denního limitu**.

![Upravit denní svazku cap telemetrie](./media/app-insights-pricing/pricing-003.png)

## <a name="sampling"></a>Vzorkování
[Vzorkování](app-insights-sampling.md) je metoda snižuje rychlost, kdy je odesílání telemetrie do vaší aplikace, při zachování vyhledání související události během diagnostických hledání. Je také zachovat počty správné událostí.

Vzorkování je efektivní způsob, jak zůstat v rámci měsíční kvóta a snížit náklady. Algoritmus vzorkování proto zachovává související položky telemetrie, například při použití hledání můžete najít žádosti související s konkrétní výjimka. Algoritmus také zachová správné počty tak, aby zobrazila správné hodnoty v Průzkumníku Metrika pro požadavků, výjimka frekvenci a dalších položek.

Existuje několik formy vzorkování.

* [Adaptivního vzorkování](app-insights-sampling.md) je výchozí hodnota pro sady SDK technologie ASP.NET. Adaptivního vzorkování automaticky upraví, aby svazek telemetrii, kterou vaše aplikace odesílá. Funguje automaticky v sadě SDK ve vaší webové aplikaci tak, aby se snižuje telemetrie provoz v síti. 
* *Přijímání vzorkování* představuje alternativu, která funguje v místě, kde telemetrie z vaší aplikace do služby Application Insights. Přijímání vzorkování nemá vliv objem telemetrická data odesílaná z vaší aplikace, ale snižuje svazku, který je uchován v portále služby. Přijímání vzorkování můžete použít ke snížení kvótu, který je používán telemetrie z prohlížečů a dalších sadách SDK.

Chcete-li nastavit přijímání vzorkování, přejděte na **cenová** podokně:

![V kvóty a cenovou podokně vyberte dlaždici ukázky a pak vyberte zlomek vzorkování](./media/app-insights-pricing/pricing-004.png)

> [!WARNING]
> **Dat vzorkování** podokně řídí pouze hodnotu přijímání vzorkování. Ho neodráží vzorkovací frekvenci, který je používán jako Application Insights SDK ve vaší aplikaci. Pokud příchozí telemetrii již odebírají vzorky v sadě SDK, není použita přijímání vzorkování.
>

Chcete-li zjistit skutečný vzorkovací frekvenci, bez ohledu na to, kde je byl použit, použijte [Analytics dotazu](app-insights-analytics.md). Dotaz vypadá takto:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

V každé uchovávají záznam, `itemCount` označuje počet původní záznamy, které představuje. Se rovná 1 + číslo předchozí zrušených záznamů. 

## <a name="automation"></a>Automation

Můžete napsat skript pro nastavení plánu cena pomocí nástroje Správa prostředků Azure. [Zjistěte jak](app-insights-powershell.md#price).

## <a name="limits-summary"></a>Souhrn omezení

[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>Další postup

* [Vzorkování](app-insights-sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/