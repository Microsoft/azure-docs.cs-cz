---
title: Správa využití a nákladů pro Azure Application Insights | Microsoft Docs
description: Spravujte svazky telemetrie a sledujte náklady na Application Insights.
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: DaleKoetke
ms.author: dalek
ms.date: 3/30/2021
ms.reviewer: lagayhar
ms.openlocfilehash: e048e788e674e90a62b15784c590c07e5d36b816
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078396"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Správa využití a nákladů pro službu Application Insights

> [!NOTE]
> Tento článek popisuje, jak pochopit a řídit náklady na Application Insights.  Související článek, [sledování využití a odhadované náklady](..//usage-estimated-costs.md) popisuje, jak zobrazit využití a odhadované náklady napříč více funkcemi monitorování Azure pro různé cenové modely.

Application Insights je navržený tak, aby získal vše, co potřebujete k monitorování dostupnosti, výkonu a využití webových aplikací, ať už jsou hostované v Azure nebo místně. Application Insights podporuje oblíbené jazyky a architektury, jako je .NET, Java a Node.js, a integruje se s procesy a nástroji DevOps, jako je Azure DevOps, JIRA a PagerDuty. Je důležité porozumět tomu, co určuje náklady na monitorování vašich aplikací. V tomto článku si projdeme, jaké jednotky vaše aplikace sledují a jak je můžete aktivně monitorovat a řídit.

Pokud máte dotazy ohledně toho, jak ceny fungují pro Application Insights, můžete odeslat dotaz na [stránce s dotazem pro Microsoft Q&](/answers/topics/azure-monitor.html).

## <a name="pricing-model"></a>Cenový model

Ceny za [Azure Application Insights][start] jsou Model průběžných **plateb** na základě ingestování objemu dat a volitelně pro delší dobu uchovávání dat. Každý prostředek Application Insights se účtuje jako samostatná služba a přispívá vám k fakturaci za vaše předplatné Azure. Objem dat se měří jako velikost balíčku nekomprimovaného data JSON, který přijímá Application Insights z vaší aplikace. Objem dat se měří v GB (10 ^ 9 bajtů). Za použití [Live Metrics Stream](./live-stream.md)se neúčtují žádné poplatky za objem dat.

U [více kroků webové testy](./availability-multistep.md) se účtují další poplatky. Webové testy s více kroky jsou webové testy, které provádějí posloupnost akcí. Pro *testy pomocí příkazů testování* jedné stránky se neúčtují žádné samostatné poplatky. Telemetrie z testů příkazového testu a testů pro více kroků se účtují stejně jako jiné telemetrie z vaší aplikace.

Možnost Application Insights [Povolit upozorňování na vlastní dimenze metriky](./pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) může také generovat další náklady, protože to může způsobit vytvoření dalších metrik před agregací. [Přečtěte si další informace](./pre-aggregated-metrics-log-metrics.md) o cenách založených na protokolu a předem agregovaných metrikách v Application Insights a o [cenách](https://azure.microsoft.com/pricing/details/monitor/) pro Azure monitor vlastních metrik.

### <a name="workspace-based-application-insights"></a>Application Insights na základě pracovního prostoru

U Application Insights prostředků, které odesílají svá data do pracovního prostoru Log Analytics nazývaného [prostředky Application Insights založené na pracovním prostoru](create-workspace-resource.md), se fakturace za příjem a uchování dat provádí v pracovním prostoru, kde se nacházejí Application Insights data. To zákazníkům umožňuje využít všechny možnosti Log Analytics [cenového modelu](../logs/manage-cost-storage.md#pricing-model) , který zahrnuje rezervace kapacity kromě průběžných plateb. Log Analytics také obsahuje další možnosti uchovávání dat, včetně [uchovávání informací podle datového typu](../logs/manage-cost-storage.md#retention-by-data-type). Application Insights datové typy v pracovním prostoru obdrží 90 dnů uchování bez poplatků. Použití webových testů a povolení upozorňování na vlastní dimenze metriky je stále hlášeno prostřednictvím Application Insights. Přečtěte si, jak sledovat příjem dat a dobu uchovávání v Log Analytics pomocí [odhadu využití a odhadované náklady](../logs/manage-cost-storage.md#understand-your-usage-and-estimate-costs), [Azure cost management + fakturace](../logs/manage-cost-storage.md#viewing-log-analytics-usage-on-your-azure-bill) a [Log Analytics dotazy](#data-volume-for-workspace-based-application-insights-resources). 

## <a name="estimating-the-costs-to-manage-your-application"></a>Odhad nákladů na správu aplikace

Pokud ještě nepoužíváte Application Insights, můžete pomocí [cenové kalkulačky Azure monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) odhadnout náklady na používání Application Insights. Začněte zadáním "Azure Monitor" do vyhledávacího pole a kliknutím na výslednou Azure Monitor dlaždici. Posuňte se dolů na stránku Azure Monitor a v rozevíracím seznamu Typ vyberte Application Insights.  Tady můžete zadat počet GB dat, která se mají shromáždit za měsíc, takže otázka bude, kolik dat Application Insights shromažďovat monitorování vaší aplikace.

Existují dva přístupy: použití výchozího monitorování a adaptivního vzorkování, který je k dispozici v sadě ASP.NET SDK, nebo k odhadu vašich pravděpodobných příjmu dat na základě toho, co viděli jiní podobní zákazníci.

### <a name="data-collection-when-using-sampling"></a>Shromažďování dat při použití vzorkování

Díky [adaptivnímu vzorkování](sampling.md#adaptive-sampling)sady ASP.NET SDK se datový svazek automaticky upraví tak, aby udržoval v rámci zadané maximální míry provozu pro výchozí Application Insights monitorování. Pokud aplikace vytvoří nízké množství telemetrie, například při ladění nebo z důvodu nízkého využití, položky se nebudou vyřadit procesorem vzorkování, pokud je svazek pod úrovní konfigurovaných událostí za sekundu. U vysoce výkonných aplikací s výchozí prahovou hodnotou pět událostí za sekundu se adaptivní vzorkování omezí počet denních událostí na 432 000. Když použijete typickou průměrnou velikost události 1 KB, bude to odpovídat 13,4 GB telemetrie za 31 dní na uzel, který hostuje vaši aplikaci, protože vzorkování se provádí místně pro každý uzel.

> [!NOTE]
> Velikost dat protokolu Azure Monitor se počítá v GB (1 GB = 10 ^ 9 bajtů).

Pro sady SDK, které nepodporují adaptivní vzorkování, můžete využívat [vzorkování](./sampling.md#ingestion-sampling)ingestování, které vzorky, když jsou data přijímána, Application Insights na základě procenta dat, která se mají zachovat, nebo [vzorkování s pevnou sazbou pro ASP.NET, ASP.NET Core a weby Java](sampling.md#fixed-rate-sampling) , aby se snížil provoz odeslaný z webového serveru a webových prohlížečů.

### <a name="learn-from-what-similar-customers-collect"></a>Další informace o shromažďování podobných zákazníků

Když v cenové kalkulačce monitorování Azure pro Application Insights povolíte funkci "objem dat odhadu na základě aktivity aplikace", můžete zadat vstupy o vaší aplikaci (požadavky za měsíc a zobrazení stránek měsíčně), pokud budete shromažďovat telemetrii na straně klienta), a pak Kalkulačka bude informovat medián a 90. percentil dat shromažďovaných podobnými aplikacemi. Tyto aplikace rozcházejí z rozsahu konfigurace Application Insights (například některé mají výchozí [vzorkování](./sampling.md), některé nemají žádné vzorkování atd.), takže stále máte kontrolu nad tím, jak omezit objem dat, která jsou až na střední úrovni, a to pomocí vzorkování. Ale jedná se o výchozí bod, který vám pomůže pochopit, co podobné zákazníky vidí.

## <a name="understand-your-usage-and-estimate-costs"></a>Pochopení nákladů na využití a odhadované náklady

Application Insights usnadňuje pochopení toho, jaké náklady budou pravděpodobně založeny na nedávných vzorech použití. Začněte tím, že v Azure Portal pro prostředek Application Insights přejdete na stránku **využití a odhadované náklady** :

![Zvolit ceny](./media/pricing/pricing-001.png)

A. Prohlédněte si svůj objem dat za měsíc. To zahrnuje všechna data, která jsou přijatá a zachovaná (po případném [vzorkování](./sampling.md)) z vašich serverových a klientských aplikací, a z testů dostupnosti.  
B. Pro [webové testy s více kroky](./availability-multistep.md)se provede samostatný poplatek. (Nezahrnuje jednoduché testy dostupnosti, které jsou zahrnuté do poplatků za objem dat.)  
C. Zobrazení trendů objemu dat za minulý měsíc.  
D. Povolit [vzorkování](./sampling.md)přijímání dat
E. Nastavte limit denního objemu dat.  

(Všimněte si, že všechny ceny zobrazené na snímcích obrazovky v tomto článku jsou například jenom pro účely. Aktuální ceny v měně a oblasti najdete v tématu [Application Insights ceny][pricing].)

Pokud chcete prozkoumat využití Application Insightsější, otevřete stránku **metriky** , přidejte metriku s názvem "svazek datových bodů" a pak vyberte možnost *použít rozdělení* pro rozdělení dat podle typu položky telemetrie.

Do faktury Azure se přidají poplatky za Application Insights. Podrobnosti o fakturaci Azure najdete v části **cost management + fakturace** Azure Portal nebo na [portálu fakturace Azure](https://account.windowsazure.com/Subscriptions).  Podrobnosti o použití tohoto pro Application Insights [najdete níže](#viewing-application-insights-usage-on-your-azure-bill) . 

![V nabídce vlevo vyberte fakturace.](./media/pricing/02-billing.png)

### <a name="using-data-volume-metrics"></a>Použití metriky objemu dat
<a id="understanding-ingested-data-volume"></a>

Pokud chcete získat další informace o vašich datových svazcích, vyberte **metriky** pro prostředek Application Insights a přidejte nový graf. U metriky grafu v části **metriky založené na protokolu** vyberte **svazek datového bodu**. Klikněte na **použít rozdělení** a vyberte seskupit podle **`Telemetryitem` typu**.

![Podívejte se na objem dat pomocí metrik.](./media/pricing/10-billing.png)

### <a name="queries-to-understand-data-volume-details"></a>Dotazy pro pochopení podrobností o objemu dat

Existují dva způsoby, jak prozkoumat datové svazky pro Application Insights. První používá agregované informace v `systemEvents` tabulce a druhá používá `_BilledSize` vlastnost, která je k dispozici na všech přijatých událostech. `systemEvents` nebude mít informace o velikosti dat pro [Application-Insights založené na pracovních prostorech](#data-volume-for-workspace-based-application-insights-resources).

#### <a name="using-aggregated-data-volume-information"></a>Použití informací agregovaného objemu dat

V tabulce můžete například pomocí `systemEvents` dotazu zobrazit datový svazek, který se v posledních 24 hodinách podrží:

```kusto
systemEvents
| where timestamp >= ago(24h)
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

Nebo chcete-li zobrazit graf objemu dat (v bajtech) podle datového typu za posledních 30 dní, můžete použít:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes) by BillingTelemetryType, bin(timestamp, 1d) | render barchart  
```

Všimněte si, že tento dotaz se dá použít v [upozornění protokolu Azure](../alerts/alerts-unified-log.md) k nastavení výstrah na datových svazcích.  

Pokud se chcete dozvědět víc o změnách dat telemetrie, můžeme k získání počtu událostí podle typu použít dotaz:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| summarize count() by BillingTelemetryType, bin(timestamp, 1d)
| render barchart  
```

#### <a name="using-data-size-per-event-information"></a>Použití informací o velikosti dat na událost

Pokud chcete získat další informace o zdroji vašich datových svazků, můžete použít `_BilledSize` vlastnost, která se nachází na každé přijaté události.

Například pro zjištění, které operace generují nejvíc objemů dat za posledních 30 dní, můžeme `_BilledSize` pro všechny události závislosti přičíst:

```kusto
dependencies
| where timestamp >= startofday(ago(30d))
| summarize sum(_BilledSize) by operation_Name
| render barchart  
```

#### <a name="data-volume-for-workspace-based-application-insights-resources"></a>Objem dat pro prostředky Application Insights na základě pracovního prostoru

Chcete-li se podívat na trendy datových svazků pro všechny [prostředky Application Insights založené na pracovním prostoru](create-workspace-resource.md) v pracovním prostoru za poslední týden, přejít do pracovního prostoru Log Analytics a spustit dotaz:

```kusto
union (AppAvailabilityResults),
      (AppBrowserTimings),
      (AppDependencies),
      (AppExceptions),
      (AppEvents),
      (AppMetrics),
      (AppPageViews),
      (AppPerformanceCounters),
      (AppRequests),
      (AppSystemEvents),
      (AppTraces)
| where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now())
| summarize sum(_BilledSize) by _ResourceId, bin(TimeGenerated, 1d)
| render areachart
```

Pokud chcete zadat dotaz na trendy datových svazků podle typu pro konkrétní prostředek Application Insights na základě pracovního prostoru, v pracovním prostoru Log Analytics použijte:

```kusto
union (AppAvailabilityResults),
      (AppBrowserTimings),
      (AppDependencies),
      (AppExceptions),
      (AppEvents),
      (AppMetrics),
      (AppPageViews),
      (AppPerformanceCounters),
      (AppRequests),
      (AppSystemEvents),
      (AppTraces)
| where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now())
| where _ResourceId contains "<myAppInsightsResourceName>"
| summarize sum(_BilledSize) by Type, bin(TimeGenerated, 1d)
| render areachart
```

## <a name="viewing-application-insights-usage-on-your-azure-bill"></a>Zobrazení využití Application Insights na faktuře Azure

Azure poskytuje skvělou užitečnou funkci centra [Azure cost management + fakturace](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=/azure/billing/TOC.json) . Například funkce "cost Analysis" umožňuje zobrazit vaše výdaje na prostředky Azure. Přidání filtru podle typu prostředku (do Microsoft. Insights/Components for Application Insights) vám umožní sledovat vaše útraty. Pak u možnosti "seskupit podle" vyberte kategorii měřičů "nebo" měřič ".  V případě Application Insights prostředků v aktuálních cenových plánech se většina využití zobrazí jako Log Analytics pro kategorii měřiče, protože pro všechny Azure Monitor komponenty se používá jeden back-end protokol. 

Další vysvětlení vašeho využití můžete získat [stažením informací o využití z webu Azure Portal](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal).
Ve stažených tabulkách můžete zobrazit využití podle prostředku Azure za den. V této excelové tabulce můžete využití vašich Application Insightsch prostředků najít při prvním filtrování ve sloupci měřiče měření, pokud chcete zobrazit Application Insights a Log Analytics, a pak přidat filtr na sloupec ID instance, který je obsahuje Microsoft. Insights/Components.  Většina využití Application Insights se oznamuje u měřičů s kategorií měřičů Log Analytics, protože pro všechny Azure Monitor komponenty existuje back-end s jedním protokolem.  Pouze Application Insights prostředky se staršími cenovými úrovněmi a webovými testy ve více krocích jsou hlášeny s kategorií měřičů Application Insights.  Využití se zobrazí ve sloupci "spotřebované množství" a jednotka pro každou položku je zobrazena ve sloupci Měrná jednotka.  K dispozici jsou také další podrobnosti, které vám pomůžou [porozumět informacím na faktuře za Microsoft Azure](../../cost-management-billing/understand/review-individual-bill.md).

## <a name="managing-your-data-volume"></a>Správa objemu dat

Objem dat, která odesíláte, můžete spravovat pomocí následujících technik:

* **Vzorkování**: vzorkování můžete použít ke snížení objemu telemetrie, která je odesílána z vašeho serveru a klientských aplikací s minimálním zkreslením metrik. Vzorkování je primární nástroj, který můžete použít k optimalizaci množství dat, která odesíláte. Přečtěte si další informace o [funkcích vzorkování](./sampling.md).

* **Omezení volání AJAX**: můžete [omezit počet volání AJAX, která lze nahlásit](./javascript.md#configuration) v každém zobrazení stránky, nebo vypnout vytváření sestav AJAX. Všimněte si, že vypnutí volání AJAX zakáže [korelaci JavaScriptu](./javascript.md#enable-correlation).

* **Zakázat nepotřebné moduly**: [Upravit ApplicationInsights.config](./configuration-with-applicationinsights-config.md) pro vypnutí modulů shromažďování, které nepotřebujete. Můžete se třeba rozhodnout, že čítače výkonu nebo data závislostí jsou nepostradatelná.

* **Předběžně agregovaná metrika**: Pokud do své aplikace vložíte volání do TrackMetric, můžete snížit provoz pomocí přetížení, které přijímá výpočet průměrné a směrodatné odchylky dávky měření. Nebo můžete použít [předagregaci balíčku](https://www.myget.org/gallery/applicationinsights-sdk-labs).
 
* **Denní limit**: když v Azure Portal vytvoříte prostředek Application Insights, denní limit je nastavený na 100 GB za den. Při vytváření prostředku Application Insights v aplikaci Visual Studio je výchozí hodnota malá (pouze 32,3 MB/den). Hodnota denní limit je nastavená tak, aby se usnadnilo testování. Je určeno, že uživatel si před nasazením aplikace do produkčního prostředí zvýší denní limit. 

    Maximální limit je 1 000 GB za den, pokud nepožadujete vyšší maximum pro vysokou přenosovou aplikaci.
    
    E-maily s upozorněním na denní limit jsou odesílány účtu, který je členem těchto rolí pro váš Application Insights prostředek: "ServiceAdmin", "AccountAdmin", "spolusprávce", "Owner".

    Při nastavování denního limitu postupujte opatrně. Váš záměr by neměl mít *nikdy žádný denní limit*. Pokud dosáhnete denního limitu, po zbytek dne budete ztrácet data a nebudete moct monitorovat svou aplikaci. Chcete-li změnit denní limit, použijte možnost **denní limit objemu** . K této možnosti můžete získat přístup v podokně **využití a odhadované náklady** (Tento postup je podrobněji popsán dále v článku).
    
    Odebrali jsme omezení pro některé typy předplatného, které mají kredit, který nebylo možné použít pro Application Insights. Pokud v minulosti má předplatné limit útraty, dialogové okno denní limit obsahuje pokyny pro odebrání limitu útraty a povolení denního limitu, který bude vyvolán až 32,3 MB za den.
    
* **Omezování**: omezování omezuje rychlost přenosu dat na 32 000 událostí za sekundu, průměr za 1 minutu na klíč instrumentace. Objem dat odesílaných vaší aplikací se vyhodnocuje každou minutu. Pokud překročí průměrnou sazbu za sekundu v průběhu minuty, server odmítne některé požadavky. Sada SDK ukládá data do vyrovnávací paměti a poté se pokusí ji znovu odeslat. Rozšíří nárůst během několika minut. Pokud vaše aplikace konzistentně odesílá data s více než frekvencí omezení, budou některá data vyřazena. (Sady SDK ASP.NET, Java a JavaScript se pokusí znovu odeslat data tímto způsobem; jiné sady SDK mohou jednoduše odtahovat omezená data.) Pokud dojde k omezování, upozornění oznámení vám upozorní, že k tomu došlo.

## <a name="manage-your-maximum-daily-data-volume"></a>Správa maximálního denního objemu dat

K omezení shromážděných dat můžete použít denní limit objemu. Pokud se ale limit splní, dojde ke ztrátě všech telemetrie odeslaných z vaší aplikace po zbytek dne. Není *vhodné* , aby vaše aplikace dosáhla denního limitu. Po dosažení denního limitu nemůžete sledovat stav a výkon vaší aplikace.

Místo používání denního limitu objemu použijte [vzorkování](./sampling.md) pro optimalizaci objemu dat na požadovanou úroveň. Pak použijte denní limit jenom jako "poslední" – pro případ, že vaše aplikace neočekávaně začne odesílat mnohem větší objem telemetrie.

### <a name="identify-what-daily-data-limit-to-define"></a>Určete, který denní limit dat se má definovat.

Přečtěte si Application Insights využití a odhadované náklady, abyste porozuměli trendům příjmu dat a jaký je denní zakončení pro definování. Mělo by se brát v úvahu opatrně, protože po dosažení limitu už nebudete moct monitorovat vaše prostředky.

### <a name="set-the-daily-cap"></a>Nastavení denního limitu

Pokud chcete změnit denní limit, v části **Konfigurace** prostředku Application Insights klikněte na stránce **využití a odhadované náklady** na  **denní limit**.

![Upravit denní limit telemetrie](./media/pricing/pricing-003.png)

Chcete-li [změnit denní limit pomocí Azure Resource Manager](./powershell.md), vlastnost, která se má změnit, je `dailyQuota` .  Prostřednictvím Azure Resource Manager můžete také nastavit `dailyQuotaResetTime` a denní limit `warningThreshold` .

### <a name="create-alerts-for-the-daily-cap"></a>Vytváření výstrah pro denní limit

Application Insights denní limit vytvoří událost v protokolu aktivit Azure, když přijímané datové svazky dosáhnou úrovně upozornění nebo úrovně denního limitu.  Můžete [vytvořit výstrahu na základě těchto událostí protokolu aktivit](../alerts/alerts-activity-log.md#create-with-the-azure-portal). Názvy signálů pro tyto události jsou:

* Dosažena prahová hodnota pro upozornění na denní limit součásti Application Insights

* Dosáhlo se denního limitu Application Insights komponenty.

## <a name="sampling"></a>Vzorkování
[vzorkování](./sampling.md) je metoda snížení míry, s jakou se telemetrie posílá do vaší aplikace, a přitom zachovává schopnost najít související události během vyhledávání diagnostiky. Zachováváte si také správné počty událostí.

Vzorkování je účinný způsob, jak snížit náklady a zůstat v rámci měsíční kvóty. Algoritmus vzorkování uchovává související položky telemetrie, takže například při použití vyhledávání můžete najít požadavek související s určitou výjimkou. Algoritmus také uchovává správné počty, takže se v Průzkumníkovi metriky zobrazí správné hodnoty pro míry požadavků, míry výjimek a další počty.

Existuje několik forem vzorkování.

* [Adaptivní vzorkování](./sampling.md) je výchozím nastavením pro sadu ASP.NET SDK. Adaptivní vzorkování se automaticky přizpůsobí objemu telemetrie, kterou vaše aplikace posílá. Funguje automaticky v sadě SDK ve vaší webové aplikaci, aby se snížil provoz telemetrie v síti. 
* *Vzorkování* ingestování je alternativou, která funguje v okamžiku, kdy telemetrie z vaší aplikace vstoupí do služby Application Insights. Vzorkování ingestování nemá vliv na objem telemetrie odeslané z vaší aplikace, ale snižuje objem, který služba zachovává. Pomocí vzorkování pro příjem dat můžete omezit kvótu, která se využívala telemetriem z prohlížečů a dalších sad SDK.

Pokud chcete nastavit vzorkování ingestování, použijte v podokně s  **cenami** :

![V podokně kvóta a ceny vyberte dlaždici ukázky a potom vyberte zlomek vzorkování.](./media/pricing/pricing-004.png)

> [!WARNING]
> Podokno **vzorkování dat** řídí pouze hodnotu vzorkování ingestování. Neodráží vzorkovací frekvenci, kterou sada Application Insights SDK používá ve vaší aplikaci. Pokud již byla v sadě SDK provedena ukázka příchozí telemetrie, vzorkování ingestování se nepoužije.
>

Pokud chcete zjistit skutečnou vzorkovací frekvenci, bez ohledu na to, kde se používá, použijte [dotaz Analytics](../logs/log-query-overview.md). Dotaz vypadá takto:

```kusto
requests | where timestamp > ago(1d)
| summarize 100/avg(itemCount) by bin(timestamp, 1h)
| render areachart
```

U každého uchovávaného záznamu `itemCount` označuje počet původních záznamů, které představuje. Rovná se 1 + počet předchozích vyřazených záznamů.

## <a name="change-the-data-retention-period"></a>Změna doby uchovávání dat

Výchozí doba uchování pro Application Insights prostředky je 90 dní. Pro každý prostředek Application Insights lze vybrat různá období uchování. Úplná sada dostupných dob uchovávání dat je 30, 60, 90, 120, 180, 270, 365, 550 nebo 730 dnů. [Přečtěte si další informace](https://azure.microsoft.com/pricing/details/monitor/) o cenách pro delší dobu uchovávání dat. 

Pokud chcete změnit dobu uchovávání, z prostředku Application Insights přejděte na stránku **využití a odhadované náklady** a vyberte možnost **uchování dat** :

![Snímek obrazovky, který ukazuje, kde změnit dobu uchovávání dat.](./media/pricing/pricing-005.png)

Když je doba uchování nižší, před odebráním nejstarší dat se zobrazí několik dní po dobu odkladu.

Uchování je také možné [nastavit pomocí parametru programově pomocí prostředí PowerShell](powershell.md#set-the-data-retention) `retentionInDays` . Pokud nastavíte uchovávání dat na 30 dní, můžete spustit okamžitou mazání starších dat pomocí `immediatePurgeDataOn30Days` parametru, který může být užitečný pro scénáře související s dodržováním předpisů. Tato funkce vyprázdnění se zveřejňuje jenom přes Azure Resource Manager a měla by se používat s mimořádnou péčí. Denní čas obnovení limitu objemu dat se dá nakonfigurovat pomocí Azure Resource Manager pro nastavení `dailyQuotaResetTime` parametru.

## <a name="data-transfer-charges-using-application-insights"></a>Poplatky za přenos dat pomocí Application Insights

Odesílání dat do Application Insights může mít za následek poplatky za šířku pásma dat. Jak je popsáno na [stránce ceny za Azure šířku pásma](https://azure.microsoft.com/pricing/details/bandwidth/), přenos dat mezi službami Azure v rámci dvou oblastí se v normální sazbě účtuje jako odchozí přenos dat. Příchozí přenos dat je zdarma. Tento poplatek je však velmi malý (několik%) v porovnání s náklady na Application Insights příjmu dat protokolu. V důsledku toho se řídí náklady na Log Analytics se musí soustředit na přijatý objem dat a máme vám Rady, jak to porozumět [.](#managing-your-data-volume)

## <a name="limits-summary"></a>Souhrn omezení

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Zakázat e-maily denního Cap

Pokud chcete zakázat e-maily denního limitu objemu, v části **Konfigurace** prostředku Application Insights v podokně **využití a odhadované náklady** vyberte  **denní limit**. K dispozici je nastavení pro odeslání e-mailu, když je dosaženo limitu, a také při dosažení nastavitelné úrovně upozornění. Pokud chcete zakázat všechny e-maily s velkým objemem, zrušte zaškrtněte obě políčka.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Cenová úroveň starší verze Enterprise (na jeden uzel)

Pro včasnou přihlášené služby Azure Application Insights stále existují dvě možné cenové úrovně: Basic a Enterprise. Cenová úroveň Basic je stejná, jak je popsáno výše, a je výchozí úroveň. Zahrnuje všechny funkce podnikové vrstvy bez dalších poplatků. Úroveň Basic se účtuje primárně na množství dat, která se ingestují.

Tyto starší cenové úrovně se přejmenovaly. Cenová úroveň Enterprise je nyní volána **na uzel** a cenová úroveň Basic je nyní volána **za GB**. Tyto nové názvy se používají níže a v Azure Portal.  

Úroveň na uzel (dříve Enterprise) má poplatek za uzel a každý uzel obdrží denní povolený objem dat. V cenové úrovni jednotlivých uzlů se vám budou účtovat data ingestovaná nad rámec zahrnutého příspěvku. Pokud používáte Operations Management Suite, měli byste zvolit vrstvu na jednu uzel. V dubnu 2018 jsme [zavedli](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) nový cenový model pro monitorování Azure. Tento model přijímá jednoduchý model průběžných plateb v rámci kompletního portfolia monitorovacích služeb. Přečtěte si další informace o [novém cenovém modelu](..//usage-estimated-costs.md).

Aktuální ceny v měně a oblasti najdete v tématu [Application Insights ceny](https://azure.microsoft.com/pricing/details/application-insights/).

### <a name="understanding-billed-usage-on-the-legacy-enterprise-per-node-tier"></a>Porozumění fakturovanému využití na úrovni starší verze Enterprise (na uzel) 

Jak je popsáno níže podrobněji, vrstva starší verze Enterprise (na uzlech) kombinuje využití napříč všemi Application Insightsmi prostředky v rámci předplatného pro výpočet počtu uzlů a překročení dat. Z důvodu tohoto kombinovaného procesu **se využití všech Application Insights prostředků v rámci předplatného hlásí pouze v jednom z prostředků**.  Díky tomu bude vaše [Fakturované využití](#viewing-application-insights-usage-on-your-azure-bill) sladěné s využitím, které sledujete u každého Application Insightsch prostředků velmi komplikované. 

> [!WARNING]
> Z důvodu složitosti sledování a porozumění využití Application Insightsch prostředků ve starší úrovni podniku (na úrovni uzlu) důrazně doporučujeme použít aktuální cenovou úroveň s průběžnými platbami. 

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>Nároky na úroveň a předplatné Operations Management Suite

Zákazníci, kteří si koupí Operations Management Suite E1 a E2, můžou získat Application Insights na jeden uzel jako další komponentu bez dalších poplatků, jak [už bylo oznámeno dřív](/archive/blogs/msoms/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription). Konkrétně každá jednotka Operations Management Suite E1 a E2 zahrnuje nárok na jeden uzel Application Insights na vrstvu uzlu. Každý uzel Application Insights zahrnuje až 200 MB dat, která se ingestují za den (oddělené od Log Analytics příjmu dat), s uchováváním dat za 90 dnů bez dalších nákladů. Úroveň je podrobněji popsána dále v článku.

Vzhledem k tomu, že tato úroveň platí jenom pro zákazníky s předplatným Operations Management Suite, zákazníci, kteří nemají předplatné Operations Management Suite, nevidí možnost výběru této úrovně.

> [!NOTE]
> Abyste se ujistili, že budete mít oprávnění, vaše prostředky Application Insights musí být v cenové úrovni podle počtu uzlů. Tato oprávnění se vztahují jenom na uzly. Application Insights prostředky na GB úrovně nevyužívají žádnou výhodu. Toto oprávnění není viditelné v odhadovaných nákladech uvedených v podokně **využití a odhadované náklady** . Pokud přesunete předplatné do nového cenového modelu Azure Monitoring v dubnu 2018, je jedinou dostupnou vrstvou úroveň na GB. Přesun předplatného na nový cenový model Azure Monitoring se nedoporučuje, pokud máte předplatné Operations Management Suite.

### <a name="how-the-per-node-tier-works"></a>Jak funguje vrstva na úrovni uzlu

* Platíte za každý uzel, který odesílá telemetrii pro všechny aplikace v rámci vrstvy na úrovni uzlů.
  * *Uzel* je fyzický nebo virtuální počítač nebo instance role platforma jako služba, která hostuje vaši aplikaci.
  * Vývojové počítače, klientské prohlížeče a mobilní zařízení se nepočítají jako uzly.
  * Pokud má vaše aplikace několik komponent, které odesílají telemetrii, jako je například webová služba a pracovní proces back-endu, komponenty se počítají samostatně.
  * Pro účely cen se nepočítají [Live Metrics Stream](./live-stream.md) data. V rámci předplatného jsou poplatky za uzel, ne pro jednotlivé aplikace. Pokud máte pět uzlů, které odesílají telemetrii pro 12 aplikací, účtuje se poplatky za pět uzlů.
* I když se účtují poplatky za měsíc, účtují se vám jenom hodiny, kdy uzel posílá telemetrii z aplikace. Hodinová sazba je cena za 744 měsíc uzavřenou v uvozovkách (počet hodin za 31. den v měsíci).
* Pro každý zjištěný uzel (s hodinovým rozlišením) je zadáno přidělení datových svazků 200 MB za den. Nevyužité přidělení dat se nepřevádí z jednoho dne na další.
  * Pokud zvolíte cenovou úroveň jednotlivých uzlů, každé předplatné získá denní příspěvek dat na základě počtu uzlů, které odesílají telemetrii do prostředků Application Insights v tomto předplatném. Takže pokud máte pět uzlů, které odesílají data každý den, budete mít na všech Application Insightsch prostředcích v tomto předplatném povolené 1 GB ve fondu. Nezáleží na tom, jestli některé uzly posílají více dat než jiné uzly, protože zahrnutá data se sdílejí napříč všemi uzly. Pokud v daném dni, Application Insights prostředky dostávají více dat, než je zahrnuto do denního přidělení dat pro toto předplatné, platí poplatky za nadlimitní využití dat za GB. 
  * Denní povolený objem dat se počítá jako počet hodin v den (pomocí UTC), který každý uzel odesílá telemetrie dělenou 24 vynásobeným 200 MB. Pokud tedy máte čtyři uzly, které odesílají telemetrii během 15 až 24 hodin v den, zahrnutá data pro daný den budou ((4 &#215; 15)/24) &#215; 200 MB = 500 MB. Za cenu 2,30 USD za GB za překročení limitu dat by se poplatek 1,15 USD, pokud uzly odešlou 1 GB dat za den.
  * Denní povolený počet na úrovni uzlu se nesdílí s aplikacemi, pro které jste zvolili úroveň na GB. Nevyužitý příspěvek se neprovádí od dnešního dne.

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Příklady určení počtu jedinečných uzlů

| Scenario                               | Celkový počet denních uzlů |
|:---------------------------------------|:----------------:|
| 1 aplikace s využitím 3 instancí Azure App Service a 1 virtuálního serveru | 4 |
| 3 aplikace běžící na 2 virtuálních počítačích; prostředky Application Insights pro tyto aplikace jsou ve stejném předplatném a na úrovni jednotlivých uzlů. | 2 | 
| 4 aplikace, jejichž prostředky Application Insights jsou ve stejném předplatném. Každá aplikace, která spouští 2 instance během 16 hodin špičky, a 4 instance během 8 hodin špičky | 13,33 |
| Cloudové služby s 1 rolí pracovního procesu a 1 webovou rolí, každá spuštěná 2 instance | 4 | 
| Cluster Azure Service Fabric s 5 uzly, který běží na mikroslužbách 50; Každá mikroslužba běžící na 3 instancích | 5|

* Přesné počítání uzlů závisí na tom, kterou sadu SDK Application Insights vaše aplikace používá. 
  * V sadě SDK verze 2,2 a novější Sestavte Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) i [Webová sada](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) SDK jako uzel pro každého hostitele aplikace. Příklady jsou názvy počítačů pro fyzického serveru a hostitele virtuálních počítačů nebo název instance pro cloudové služby.  Jedinou výjimkou je aplikace, která používá pouze [.NET Core](https://dotnet.github.io/) a sadu SDK Application Insights Core. V takovém případě je pro všechny hostitele hlášen pouze jeden uzel, protože název hostitele není k dispozici.
  * V případě starších verzí sady SDK se [Webová sada SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) chová jako novější verze sady SDK, ale [základní sada SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) hlásí pouze jeden uzel, bez ohledu na počet hostitelů aplikace.
  * Pokud vaše aplikace používá sadu SDK k nastavení **roleInstance** na vlastní hodnotu, je ve výchozím nastavení použita stejná hodnota k určení počtu uzlů.
  * Pokud používáte novou verzi sady SDK s aplikací, která se spouští z klientských počítačů nebo mobilních zařízení, počet uzlů může vracet velký počet (z důvodu velkého počtu klientských počítačů nebo mobilních zařízení).

## <a name="automation"></a>Automation

Pomocí správy prostředků Azure můžete napsat skript pro nastavení cenové úrovně. [Přečtěte si, jak](powershell.md#price).

## <a name="next-steps"></a>Další kroky

* [kontrol](./sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ./app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/