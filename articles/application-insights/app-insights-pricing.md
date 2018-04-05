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
ms.openlocfilehash: 42c54509cedf48e9ebabb5b50865ed56e54bee05
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Správa svazku ceny a data ve službě Application Insights

Ceny pro [Azure Application Insights] [ start] je založena na datový svazek na aplikaci. Každý prostředek Application Insights je účtován jako samostatná služba a přispívá k fakturovaná částka u předplatného Azure.

Existují dva cenovou plány. Basic, který zahrnuje všechny stejné funkce jako plánu podnikového na bez přidání náklady a faktur hlavně na objemu dat požity se nazývá výchozí plán. Pokud používáte služby Operations Management Suite, musí vyjádřit výslovný pro organizace plán, který má uzlům účtují společně s denně přídavky data a následně vám bude účtovat data požity výše zahrnuté povoleného užívání.

Pokud máte dotazy týkající se fungování ceny pro službu Application Insights, nebojte se odeslat dotaz v našem [fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights).

## <a name="the-price-plans"></a>Plány cena

Najdete v článku [Application Insights stránce s cenami] [ pricing] pro aktuální ceny měny a oblasti.

### <a name="basic-plan"></a>Základní plán

Základní plán je výchozí, když se vytvoří nový prostředek Application Insights a je optimální pro všechny zákazníky s výjimkou těch, které s předplatným služby Operations Management Suite.

* V základní plán, budou se vám účtovat podle objemu dat: počet bajtů přijatých Application Insights telemetrie. Datový svazek se měří jako velikost nekomprimovaných dat balíčku JSON přijatých Application Insights z vaší aplikace.
Pro [tabulková data importovat Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), datový svazek se měří jako velikost nekomprimovaných souborů odeslaných do služby Application Insights.
* [Živý datový proud metriky](app-insights-live-stream.md) dat není počítá o cenách pro účely.
* [Průběžné Export](app-insights-export-telemetry.md) a [analýzy protokolů konektor](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409) v základní plán od duben 2018 jsou k dispozici bez jakýchkoli dalších poplatků.

### <a name="enterprise-plan-and-operations-management-suite-subscription-entitlements"></a>Plánování Enterprise a oprávnění předplatné služby Operations Management Suite

Zákazníci, kteří zakoupili E1 Microsoft Operations Management Suite a E2 je možné získat Application Insights Enterprise jako další komponentu bez dalších poplatků jako [dříve oznámeno](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/). Jednotky služby Operations Management Suite E1 a E2 konkrétně obsahuje nároku na 1 uzel Enterprise plánu Application Insights. Jak je popsáno níže podrobněji každý uzel Application Insights zahrnuje až 200 MB dat požity za den (oddělené od analýzy protokolů přijímání dat), s dobou uchování dat 90 dnů bez dalších poplatků. Vzhledem k tomu, že tento krok platí jenom pro zákazníky s předplatným služby Operations Management Suite, zákazníků bez předplatného se nezobrazí možnost vybrat tento plán.

> [!NOTE]
> Aby se zajistilo, že dostanete tento nárok, musí mít vašich prostředků Application Insights v podnikové síti ceny plánu. Toto oprávnění platí pouze jako uzly, takže prostředky Application Insights v základní plán nebude potřeba si uvědomit, nějaké výhody. Tento nárok nebude zobrazovat na odhadované náklady zobrazený na *využití a odhadované náklady* stránky. Navíc pokud přesunete předplatné vás nový Azure monitorování cenový model 2018 duben, základní plán bude k dispozici pouze plán proto se nedoporučuje, pokud máte předplatné služby Operations Management Suite.

Další informace o plánu podnikového webu [stránce s cenami podrobnosti Enterprise.](app-insights-pricing-enterprise-details.md)

### <a name="multi-step-web-tests"></a>Vícekrokové webové testy

Je dalších poplatků pro [vícekrokové webové testy](app-insights-monitor-web-app-availability.md#multi-step-web-tests). Vztahuje se k webové testy, které provádějí posloupnost akcí.

Je bezplatná samostatné pro příkaz ping testy jedné stránky. Telemetrie z testů pomocí příkazu ping a testy vícekrokový je účtován spolu s další telemetrie z vaší aplikace.

## <a name="review-pricing-plans-and-estimate-costs"></a>Zkontrolujte cenovou plány a odhadnout náklady

Application Insights snadno pochopit cenovou plány, které jsou k dispozici a jaké nákladů je pravděpodobné, že budou založeny na poslední vzorce používání. Začněte otevřením **využití a odhadované náklady** stránky v prostředku Application Insights na portálu Azure:

![Zvolte ceny.](./media/app-insights-pricing/pricing-001.png)

**a.** Zkontrolujte datový svazek pro daný měsíc. To zahrnuje všechna data přijme a uchovávají (po všech [vzorkování](app-insights-sampling.md) ze serveru a klientské aplikace a z testy dostupnosti.

**b.** Účtována samostatně se provádí [vícekrokové webové testy](app-insights-monitor-web-app-availability.md#multi-step-web-tests). (To neobsahuje testy jednoduché dostupnosti, které jsou součástí poplatků svazku data.)

**c.** Zobrazení trendů objem dat pro poslední měsíc.

**d.** Povolení přijímání dat [vzorkování.](app-insights-sampling.md) 

**e.** Konfigurace svazku krytky denní data.

Application Insights poplatky budou přidány do faktury Azure. Zobrazí podrobnosti o vašem Azure účtovat pošle v oddílu fakturace na portálu Azure nebo v [Azure Billing Portal](https://account.windowsazure.com/Subscriptions). 

![V nabídce straně zvolte fakturace.](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Rychlost přenosu dat
Existují tři způsoby, ve kterých je omezená svazku odesílat data:

* **Vzorkování:** lze použít tento mechanismus snížíte telemetrické zprávy odesílané z vašeho serveru a klienta aplikace s minimální narušení metrik. Toto je primárním nástrojem budete muset vyladit množství dat. Další informace o [vzorkování funkce](app-insights-sampling.md). 
* **Denního limitu:** při vytváření prostředek Application Insights z portálu Azure, to je nastavena na 100 GB a den. Výchozí hodnota při vytváření prostředek Application Insights v sadě Visual Studio je malý (pouze 32,3 MB/den), který je určen pouze k usnadnění testování. V takovém případě je určena, bude uživatel vyvolat denního limitu před nasazením aplikace do produkčního prostředí. Maximální hodnota je 1 000 GB a den, pokud požadujete vyšší maximum pro intenzivní provoz aplikace. Použít pozor při nastavování denního limitu podle vašich představ by měla být **nikdy k volání denního limitu**, protože se pak dojít ke ztrátě dat pro zbytek dne a nebude možné do monitorování vaší aplikace. Chcete ho změnit, použijte možnost cap denní svazku odkazované z využití a odhadované náklady na stránce (viz níže). Odebrali jsme omezení na některé typy předplatné, které mají kreditu, který nelze použít pro službu Application Insights. Dříve Pokud předplatné má limitu útraty a automaticky, dialogu denního limitu bude mít pokyny jak ho odebrat a povolit denního limitu má být aktivována nad rámec 32,3 MB a den.
* **Omezení šířky pásma:** toto omezení rychlost přenosu dat do 32 000 událostí za sekundu, zprůměrovanou více než 1 minuta.

*Co se stane, když aplikace my překračuje míru omezení?*

* Objem dat, která vaše aplikace odesílá se hodnotí každou minutu. Pokud překročí rychlost za sekundu průměr za minutu, server odmítá některých požadavků. Sada SDK ukládá data do vyrovnávací paměti a potom se pokusí o odeslání, rozšíří nárůst za několik minut. Pokud vaše aplikace konzistentně odesílá data na vyšší míru omezení, některá data se zahodí. (ASP.NET, Java a JavaScript SDK pokusí odeslat znovu tímto způsobem, dalších sadách SDK může jednoduše rozevírací omezeny data.) Pokud dojde k omezení šířky pásma, uvidíte oznámení upozornění, že byla odepřena.

*Jak poznám, kolik dat Moje aplikace odesílá?*

* Otevřete **využití a odhadované náklady** stránky se zobrazí denní svazku graf data. 
* Nebo v Průzkumníku metrik, přidejte nový graf a vyberte **datového bodu svazku** jako jeho metriku. Přepněte na seskupení a seskupit podle **datový typ**.

## <a name="to-reduce-your-data-rate"></a>Ke snížení přenosová rychlost
Zde jsou některé věci, které může provádět ke snížení datový svazek:

* Použití [vzorkování](app-insights-sampling.md). Tato technologie snižuje rychlost přenosu dat bez zkosení vaše metriky a to bez přerušení možnost přecházet mezi související položky v hledání. V aplikacích serveru funguje automaticky.
* [Omezit počet volání Ajax, které mohou být oznámeny](app-insights-javascript.md#detailed-configuration) v každé zobrazení stránky nebo přepínač vypnout Ajax reporting.
* Vypnout kolekce modulů nepotřebujete podle [úpravy souboru ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Například můžete rozhodnout, jestli jsou inessential čítače výkonu nebo data závislostí.
* Rozdělení telemetrie k oddělení klíčů instrumentace. 
* Předem agregační metriky. Pokud jste umístili volání TrackMetric ve vaší aplikaci, můžete snížit provozu pomocí přetížení, které přijímá vaší výpočtu průměru a směrodatné odchylky dávky měření. Nebo můžete použít [předem totožný balíček](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="managing-the-maximum-daily-data-volume"></a>Správa maximální denní datový svazek

Denního limitu svazku můžete použít k omezení data shromážděná, ale pokud je splněna krytky, způsobí ztrátu všechny telemetrická data odesílaná z vaší aplikace pro zbývající dne. Je **není vhodné** tak, aby měl aplikace narazí denního limitu vzhledem k tomu, že nebudete moci sledovat stav a výkon vaší aplikace poté, co je přístupů.

Místo toho použijte [vzorkování](app-insights-sampling.md) k vyladění datový svazek na úroveň by jako a použít denního limitu pouze jako "poslední možnost" v případě, že vaše aplikace spustí neočekávaně odesílání mnohem vyšší objemy telemetrie.

Chcete-li změnit denního limitu, v části konfigurace vaší prostředku Application Insights z **využití a odhadované náklady** klikněte na tlačítko **denního limitu**.

![Úprava denní svazku cap telemetrie](./media/app-insights-pricing/pricing-003.png)

## <a name="sampling"></a>Vzorkování
[Vzorkování](app-insights-sampling.md) je metoda snižuje rychlost, kdy je odesílání telemetrie do vaší aplikace a zároveň zachovat vyhledání související události během diagnostických hledání a počty stále zachování správné událostí.

Vzorkování je efektivní způsob, jak zůstat v rámci měsíční kvóta a snížit náklady. Algoritmus vzorkování zachová související položky telemetrie, takže například při použití hledání můžete najít žádosti související s konkrétní výjimka. Algoritmus také zachová správné počty, abyste viděli správné hodnoty v Průzkumníku Metrika pro požadavků, výjimka frekvenci a dalších položek.

Existuje několik formy vzorkování.

* [Adaptivního vzorkování](app-insights-sampling.md) je výchozí hodnota pro sady SDK technologie ASP.NET, který automaticky upraví, aby svazek telemetrii, kterou vaše aplikace odesílá. Funguje automaticky v sadě SDK ve vaší webové aplikaci, tak, aby se snižuje telemetrie provoz v síti. 
* *Přijímání vzorkování* představuje alternativu, která funguje v místě, kde telemetrie z vaší aplikace do služby Application Insights. Neovlivní objem telemetrická data odesílaná z vaší aplikace, ale snižuje svazku uchována pomocí služby. Můžete ji ke snížení kvótu používané telemetrie z prohlížečů a dalších sadách SDK.

Chcete-li nastavit přijímání vzorkování, nastavte ovládacího prvku v dialogovém okně Cenová:

![Z kvóty a cenovou dialogové okno klikněte na dlaždici ukázky a vyberte zlomek vzorkování.](./media/app-insights-pricing/pricing-004.png)

> [!WARNING]
> Dialogové okno vzorkování dat je řízeno jen hodnotu přijímání vzorkování. Ho neodráží vzorkovací frekvenci, které se uplatňuje Application Insights SDK ve vaší aplikaci. Pokud příchozí telemetrii má již byla odebírána data v sadě SDK, není použita přijímání vzorkování.
>

Chcete-li zjistit skutečný vzorkovací frekvenci bez ohledu na to, kde byl použit, použijte [Analytics dotazu](app-insights-analytics.md) jako je tato:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

V každé uchovávají záznam, `itemCount` označuje počet původní záznamy, které představuje, rovna 1 + číslo předchozí zrušených záznamů. 

## <a name="automation"></a>Automation

Můžete napsat skript, nastavit plán cena pomocí Správa prostředků Azure. [Zjistěte jak](app-insights-powershell.md#price).

## <a name="limits-summary"></a>Souhrn omezení

[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>Další postup

* [Vzorkování](app-insights-sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
