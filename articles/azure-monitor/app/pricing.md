---
title: Správa využití a nákladů pro Azure Application Insights | Microsoft Docs
description: Spravujte svazky telemetrie a sledujte náklady na Application Insights.
services: application-insights
documentationcenter: ''
author: DaleKoetke
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/29/2019
ms.author: dalek
ms.openlocfilehash: f2ea87f237fa5cca8a4b1f6773d2b47729cfb5b0
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147557"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Správa využití a nákladů pro Application Insights

> [!NOTE]
> Tento článek popisuje, jak analyzovat využití dat Application Insights.  Přečtěte si následující články související informace.
> - [Monitorování využití a odhadované náklady](../../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) popisuje, jak zobrazit využití a odhadované náklady napříč více funkcí pro různé cenové modely pro monitorování Azure. Také popisuje, jak změnit cenový model.

Pokud máte dotazy ohledně toho, jak ceny fungují pro Application Insights, můžete na našem [fóru](https://social.msdn.microsoft.com/Forums/home?forum=ApplicationInsights)odeslat dotaz.

## <a name="pricing-model"></a>Cenový model

Ceny za [Azure Application Insights][start] jsou založené na ingestování objemu dat. Každý prostředek Application Insights se účtuje jako samostatná služba a přispívá vám k fakturaci za vaše předplatné Azure.

### <a name="data-volume-details"></a>Podrobnosti o objemu dat

* Objem dat je počet bajtů telemetrie obdržených Application Insights. Objem dat se měří jako velikost balíčku nekomprimovaného data JSON, který přijímá Application Insights z vaší aplikace. Pro [tabulková data importovaná do analýz](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import)se objem dat měří jako nekomprimovaná velikost souborů, které se odesílají do Application Insights.
* Poplatky za datové objemy dat vaší aplikace se teď hlásí na novém měřiči fakturace s názvem ingestování **dat** od dubna 2018. Tento nový měřič se sdílí mezi technologiemi monitorování, jako jsou aplikace Insights a Log Analytics a je v současnosti pod názvem služby **Log Analytics**. 
* Pro účely cen se nepočítají [Live Metrics Stream](../../azure-monitor/app/live-stream.md) data.

> [!NOTE]
> Všechny ceny zobrazené na snímcích obrazovky v tomto článku jsou třeba jenom pro účely. Aktuální ceny v měně a oblasti najdete v tématu [Application Insights ceny][pricing].

### <a name="multi-step-web-tests"></a>Vícekrokové webové testy

U [více kroků webové testy](../../azure-monitor/app/availability-multistep.md) se účtují další poplatky. Webové testy s více kroky jsou webové testy, které provádějí posloupnost akcí.

Pro *testy pomocí příkazů testování* jedné stránky se neúčtují žádné samostatné poplatky. Telemetrie z testů příkazového testu a testů pro více kroků se účtují stejně jako jiné telemetrie z vaší aplikace.

## <a name="review-usage-and-estimate-costs"></a>Kontrola nákladů na využití a odhad

Application Insights usnadňuje pochopení toho, jaké náklady budou pravděpodobně založeny na nedávných vzorech použití. Začněte tím, že v Azure Portal pro prostředek Application Insights přejdete na stránku **využití a odhadované náklady** :

![Zvolit ceny](./media/pricing/pricing-001.png)

A. Prohlédněte si svůj objem dat za měsíc. To zahrnuje všechna data, která jsou přijatá a zachovaná (po případném [vzorkování](../../azure-monitor/app/sampling.md)) z vašich serverových a klientských aplikací, a z testů dostupnosti.  
B. Pro [webové testy s více kroky](../../azure-monitor/app/availability-multistep.md)se provede samostatný poplatek. (Nezahrnuje jednoduché testy dostupnosti, které jsou zahrnuté do poplatků za objem dat.)  
C. Zobrazení trendů objemu dat za minulý měsíc.  
D. Povolit [vzorkování](../../azure-monitor/app/sampling.md)přijímání dat   
E. Nastavte limit denního objemu dat.  

Pokud chcete prozkoumat využití Application Insightsější, otevřete stránku **metriky** , přidejte metriku s názvem "svazek datových bodů" a pak vyberte možnost *použít rozdělení* pro rozdělení dat podle typu položky telemetrie. 

Do faktury Azure se přidají poplatky za Application Insights. Podrobnosti o fakturaci Azure najdete v části **fakturace** Azure Portal nebo na [portálu fakturace Azure](https://account.windowsazure.com/Subscriptions). 

![V nabídce vlevo vyberte fakturace.](./media/pricing/02-billing.png)

## <a name="managing-your-data-volume"></a>Správa objemu dat 

Pokud chcete zjistit, kolik dat vaše aplikace posílá, můžete:

* Přejděte na podokno **využití a odhadované náklady** , kde se zobrazí graf denního objemu dat. 
* V Průzkumník metrik přidejte nový graf. U metriky grafu vyberte **svazek datového bodu**. Zapněte **seskupování**a pak proveďte seskupení podle **datového typu**.
* `systemEvents` Použijte datový typ. Chcete-li například zobrazit objem dat zpracovaných za poslední den, dotaz by byl:

```kusto
systemEvents 
| where timestamp >= ago(1d)
| where type == "Billing" 
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

Tento dotaz se dá použít v [upozornění protokolu Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log) k nastavení výstrah na datových svazcích. 

Objem dat, která odesíláte, můžete spravovat třemi způsoby:

* **Vzorkování**: Vzorkování můžete použít ke snížení objemu telemetrie, která je odesílána z vašeho serveru a klientských aplikací s minimálním zkreslením metrik. Vzorkování je primární nástroj, který můžete použít k optimalizaci množství dat, která odesíláte. Přečtěte si další informace o [funkcích vzorkování](../../azure-monitor/app/sampling.md).
 
* **Denní limit**: Při vytváření prostředku Application Insights v Azure Portal je denní limit nastavený na 100 GB za den. Při vytváření prostředku Application Insights v aplikaci Visual Studio je výchozí hodnota malá (pouze 32,3 MB/den). Hodnota denní limit je nastavená tak, aby se usnadnilo testování. Je určeno, že uživatel si před nasazením aplikace do produkčního prostředí zvýší denní limit. 

    Maximální limit je 1 000 GB za den, pokud nepožadujete vyšší maximum pro vysokou přenosovou aplikaci. 
    
    E-maily s upozorněním na denní limit jsou odesílány účtu, který je členem těchto rolí pro váš Application Insights prostředek: "ServiceAdmin", "AccountAdmin", "spolusprávce", "Owner".

    Při nastavování denního limitu postupujte opatrně. Váš záměr by neměl mít *nikdy žádný denní limit*. Pokud získáte denní limit, ztratíte data po zbytek dne a nemůžete monitorovat svoji aplikaci. Chcete-li změnit denní limit, použijte možnost **denní limit objemu** . K této možnosti můžete získat přístup v podokně **využití a odhadované náklady** (Tento postup je podrobněji popsán dále v článku).
    
    Odebrali jsme omezení pro některé typy předplatného, které mají kredit, který nebylo možné použít pro Application Insights. Pokud v minulosti má předplatné limit útraty, dialogové okno denní limit obsahuje pokyny pro odebrání limitu útraty a povolení denního limitu, který bude vyvolán až 32,3 MB za den.
    
* **Omezování**: Omezení omezuje přenosovou rychlost na 32 000 událostí za sekundu, průměrně za 1 minutu na klíč instrumentace. Objem dat odesílaných vaší aplikací se vyhodnocuje každou minutu. Pokud překročí průměrnou sazbu za sekundu v průběhu minuty, server odmítne některé požadavky. Sada SDK ukládá data do vyrovnávací paměti a poté se pokusí ji znovu odeslat. Rozšíří nárůst během několika minut. Pokud vaše aplikace konzistentně odesílá data s více než frekvencí omezení, budou některá data vyřazena. (Sady SDK ASP.NET, Java a JavaScript se pokusí znovu odeslat data tímto způsobem; jiné sady SDK mohou jednoduše odtahovat omezená data.) Pokud dojde k omezování, upozornění oznámení vám upozorní, že k tomu došlo.

## <a name="reduce-your-data-volume"></a>Zmenšení objemu dat

Tady je několik věcí, které můžete udělat ke snížení objemu dat:

* Použijte [vzorkování](../../azure-monitor/app/sampling.md). Tato technologie omezuje vaši rychlost přenosu dat bez toho, aby se vaše metrika zkrátila. Neztratíte možnost navigace mezi souvisejícími položkami v hledání. V serverových aplikacích vzorkování funguje automaticky.
* [Omezte počet volání AJAX, která mohou být uvedena](../../azure-monitor/app/javascript.md#configuration) v každém zobrazení stránky, nebo vypněte vytváření sestav AJAX.
* [Úpravou souboru ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) vypněte moduly shromažďování, které nepotřebujete. Můžete se třeba rozhodnout, že čítače výkonu nebo data závislostí jsou nepostradatelná.
* Rozdělení telemetrie mezi samostatné klíče instrumentace. 
* Předem agregované metriky. Pokud do své aplikace vložíte volání TrackMetric, můžete snížit provoz pomocí přetížení, které přijímá výpočet průměrné a směrodatné odchylky dávky měření. Nebo můžete použít předagregaci [balíčku](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="manage-the-maximum-daily-data-volume"></a>Správa maximální denní objem dat

K omezení shromážděných dat můžete použít denní limit objemu. Pokud se ale limit splní, dojde ke ztrátě všech telemetrie odeslaných z vaší aplikace po zbytek dne. Není *vhodné* , aby vaše aplikace dosáhla denního limitu. Po dosažení denního limitu nemůžete sledovat stav a výkon vaší aplikace.

Místo používání denního limitu objemu použijte [vzorkování](../../azure-monitor/app/sampling.md) pro optimalizaci objemu dat na požadovanou úroveň. Pak použijte denní limit jenom jako "poslední" – pro případ, že vaše aplikace neočekávaně začne odesílat mnohem větší objem telemetrie.

Pokud chcete změnit denní limit, v části **Konfigurace** prostředku Application Insights klikněte na stránce **využití a odhadované náklady** na **denní limit**.

![Upravit denní limit telemetrie](./media/pricing/pricing-003.png)

## <a name="sampling"></a>Vzorkování
[Vzorkování](../../azure-monitor/app/sampling.md) je metoda snížení míry, s jakou se telemetrie posílá do vaší aplikace, a přitom zachovává schopnost najít související události během vyhledávání diagnostiky. Zachováváte si také správné počty událostí.

Vzorkování je účinný způsob, jak snížit náklady a zůstat v rámci měsíční kvóty. Algoritmus vzorkování uchovává související položky telemetrie, takže například při použití vyhledávání můžete najít požadavek související s určitou výjimkou. Algoritmus také uchovává správné počty, takže se v Průzkumníkovi metriky zobrazí správné hodnoty pro míry požadavků, míry výjimek a další počty.

Existuje několik forem vzorkování.

* [Adaptivní vzorkování](../../azure-monitor/app/sampling.md) je výchozím nastavením pro sadu ASP.NET SDK. Adaptivní vzorkování se automaticky přizpůsobí objemu telemetrie, kterou vaše aplikace posílá. Funguje automaticky v sadě SDK ve vaší webové aplikaci, aby se snížil provoz telemetrie v síti. 
* *Vzorkování* ingestování je alternativou, která funguje v okamžiku, kdy telemetrie z vaší aplikace vstoupí do služby Application Insights. Vzorkování ingestování nemá vliv na objem telemetrie odeslané z vaší aplikace, ale snižuje objem, který služba zachovává. Pomocí vzorkování pro příjem dat můžete omezit kvótu, která se využívala telemetriem z prohlížečů a dalších sad SDK.

Pokud chcete nastavit vzorkování ingestování, použijte v podokně s **cenami** :

![V podokně kvóta a ceny vyberte dlaždici ukázky a potom vyberte zlomek vzorkování.](./media/pricing/pricing-004.png)

> [!WARNING]
> Podokno **vzorkování dat** řídí pouze hodnotu vzorkování ingestování. Neodráží vzorkovací frekvenci, kterou sada Application Insights SDK používá ve vaší aplikaci. Pokud již byla v sadě SDK provedena ukázka příchozí telemetrie, vzorkování ingestování se nepoužije.
>

Pokud chcete zjistit skutečnou vzorkovací frekvenci, bez ohledu na to, kde se používá, použijte [dotaz Analytics](analytics.md). Dotaz vypadá takto:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

U každého uchovávaného záznamu `itemCount` označuje počet původních záznamů, které představuje. Rovná se 1 + počet předchozích vyřazených záznamů. 

## <a name="change-the-data-retention-period"></a>Změnit dobu uchování dat

> [!NOTE]
> Tuto funkci jsme dočasně odebrali, zatímco řešíme možný problém.  Budeme to mít zpátky od poloviny 2019.

Výchozí doba uchování pro Application Insights prostředky je 90 dní. Pro každý prostředek Application Insights lze vybrat různá období uchování. Úplná sada dostupných dob uchovávání dat je 30, 60, 90, 120, 180, 270, 365, 550 nebo 730 dnů. 

Pokud chcete změnit dobu uchovávání, z prostředku Application Insights přejděte na stránku **využití a odhadované náklady** a vyberte možnost **uchování dat** :

![Upravit denní limit telemetrie](./media/pricing/pricing-005.png)

Když je fakturace povolená pro delší dobu uchování, data uchovávaná déle než 90 dní se budou účtovat jako stejná sazba, která se aktuálně účtuje za Azure Log Analytics uchovávání dat. Další informace najdete na [stránce s cenami Azure monitor](https://azure.microsoft.com/pricing/details/monitor/). [Pro tento návrh](https://feedback.azure.com/forums/357324-azure-monitor-application-insights/suggestions/17454031)můžete mít přehled o průběhu proměnlivého uchovávání dat. 

## <a name="limits-summary"></a>Souhrn omezení

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Zakázat e-maily denního Cap

Pokud chcete zakázat e-maily denního limitu objemu, v části **Konfigurace** prostředku Application Insights v podokně **využití a odhadované náklady** vyberte **denní limit**. K dispozici je nastavení pro odeslání e-mailu, když je dosaženo limitu, a také při dosažení nastavitelné úrovně upozornění. Pokud chcete zakázat všechny e-maily s denním limitem, zrušte u obou polí všechna.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Cenová úroveň starší verze Enterprise (na jeden uzel)

Pro včasnou přihlášené služby Azure Application Insights stále existují dvě možné cenové úrovně: Basic a Enterprise. Cenová úroveň Basic je stejná, jak je popsáno výše, a je výchozí úroveň. Zahrnuje všechny funkce podnikové vrstvy bez dalších poplatků. Úroveň Basic se účtuje primárně na množství dat, která se ingestují. 

> [!NOTE]
> Tyto starší cenové úrovně se přejmenovaly. Cenová úroveň Enterprise je nyní volána **na uzel** a cenová úroveň Basic je nyní volána **za GB**. Tyto nové názvy se používají níže a v Azure Portal.  

Úroveň na uzel (dříve Enterprise) má poplatek za uzel a každý uzel obdrží denní povolený objem dat. V cenové úrovni jednotlivých uzlů se vám budou účtovat data ingestovaná nad rámec zahrnutého příspěvku. Pokud používáte Operations Management Suite, měli byste zvolit vrstvu na jednu uzel. 

Aktuální ceny v měně a oblasti najdete v tématu [Application Insights ceny](https://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> V dubnu 2018 jsme zavedli nový cenový model pro monitorování Azure. [](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) Tento model přijímá jednoduchý model průběžných plateb v rámci kompletního portfolia monitorovacích služeb. Přečtěte si další informace o [novém cenovém modelu](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), jak [posoudit dopad přechodu na tento model](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) na základě vašich způsobů používání a [jak se vyjádřit k novému modelu](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model) .

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>Nároky na úroveň a předplatné Operations Management Suite

Zákazníci, kteří si koupí Operations Management Suite E1 a E2, můžou získat Application Insights na jeden uzel jako další komponentu bez dalších poplatků, jak [už bylo oznámeno dřív](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/). Konkrétně každá jednotka Operations Management Suite E1 a E2 zahrnuje nárok na jeden uzel Application Insights na vrstvu uzlu. Každý uzel Application Insights zahrnuje až 200 MB dat, která se ingestují za den (oddělené od Log Analytics příjmu dat), s uchováváním dat za 90 dnů bez dalších nákladů. Úroveň je podrobněji popsána dále v článku. 

Vzhledem k tomu, že tato úroveň platí jenom pro zákazníky s předplatným Operations Management Suite, zákazníci, kteří nemají předplatné Operations Management Suite, nevidí možnost výběru této úrovně.

> [!NOTE]
> Abyste se ujistili, že budete mít oprávnění, vaše prostředky Application Insights musí být v cenové úrovni podle počtu uzlů. Tato oprávnění se vztahují jenom na uzly. Application Insights prostředky na GB úrovně nevyužívají žádnou výhodu. Toto oprávnění není viditelné v odhadovaných nákladech uvedených v podokně **využití a odhadované náklady** . Pokud přesunete předplatné do nového cenového modelu Azure Monitoring v dubnu 2018, je jedinou dostupnou vrstvou úroveň na GB. Přesun předplatného na nový cenový model Azure Monitoring se nedoporučuje, pokud máte předplatné Operations Management Suite.

### <a name="how-the-per-node-tier-works"></a>Jak funguje vrstva na úrovni uzlu

* Platíte za každý uzel, který odesílá telemetrii pro všechny aplikace v rámci vrstvy na úrovni uzlů.
  * *Uzel* je fyzický nebo virtuální počítač nebo instance role platforma jako služba, která hostuje vaši aplikaci.
  * Vývojové počítače, klientské prohlížeče a mobilní zařízení se nepočítají jako uzly.
  * Pokud má vaše aplikace několik komponent, které odesílají telemetrii, jako je například webová služba a pracovní proces back-endu, komponenty se počítají samostatně.
  * Pro účely cen se nepočítají [Live Metrics Stream](../../azure-monitor/app/live-stream.md) data. V rámci předplatného jsou poplatky za uzel, ne pro jednotlivé aplikace. Pokud máte pět uzlů, které odesílají telemetrii pro 12 aplikací, účtuje se poplatky za pět uzlů.
* I když se účtují poplatky za měsíc, účtují se vám jenom hodiny, kdy uzel posílá telemetrii z aplikace. Hodinová sazba je cena za 744 měsíc uzavřenou v uvozovkách (počet hodin za 31. den v měsíci).
* Pro každý zjištěný uzel (s hodinovým rozlišením) je zadáno přidělení datových svazků 200 MB za den. Nevyužité přidělení dat se nepřevádí z jednoho dne na další.
  * Pokud zvolíte cenovou úroveň jednotlivých uzlů, každé předplatné získá denní příspěvek dat na základě počtu uzlů, které odesílají telemetrii do prostředků Application Insights v tomto předplatném. Takže pokud máte pět uzlů, které odesílají data každý den, budete mít na všech Application Insightsch prostředcích v tomto předplatném povolené 1 GB ve fondu. Nezáleží na tom, jestli některé uzly posílají více dat než jiné uzly, protože zahrnutá data se sdílejí napříč všemi uzly. Pokud v daném dni, Application Insights prostředky dostávají více dat, než je zahrnuto do denního přidělení dat pro toto předplatné, platí poplatky za nadlimitní využití dat za GB. 
  * Denní povolený objem dat se počítá jako počet hodin v den (pomocí UTC), který každý uzel odesílá telemetrie dělenou 24 vynásobeným 200 MB. Pokud tedy máte čtyři uzly, které odesílají telemetrii během 15 až 24 hodin v den, zahrnutá data pro daný den budou ((4 &#215; 15)/24) &#215; 200 MB = 500 MB. Za cenu 2,30 USD za GB za překročení limitu dat by se poplatek 1,15 USD, pokud uzly odešlou 1 GB dat za den.
  * Denní povolený počet na úrovni uzlu se nesdílí s aplikacemi, pro které jste zvolili úroveň na GB. Nevyužitý příspěvek se neprovádí od dnešního dne. 

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Příklady určení počtu jedinečných uzlů

| Scénář                               | Celkový počet denních uzlů |
|:---------------------------------------|:----------------:|
| 1 aplikace s využitím 3 instancí Azure App Service a 1 virtuálního serveru | 4 |
| 3 aplikace běžící na 2 virtuálních počítačích; prostředky Application Insights pro tyto aplikace jsou ve stejném předplatném a na úrovni jednotlivých uzlů. | 2 | 
| 4 aplikace, jejichž prostředky Application Insights jsou ve stejném předplatném. Každá aplikace, která spouští 2 instance během 16 hodin špičky, a 4 instance během 8 hodin špičky | 13.33 | 
| Cloudové služby s 1 rolí pracovního procesu a 1 webovou rolí, každá spuštěná 2 instance | 4 | 
| Cluster Azure Service Fabric s 5 uzly, který běží na mikroslužbách 50; Každá mikroslužba běžící na 3 instancích | 5|

* Přesné počítání uzlů závisí na tom, kterou sadu SDK Application Insights vaše aplikace používá. 
  * V sadě SDK verze 2,2 a novější Sestavte Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) i [Webová sada](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) SDK jako uzel pro každého hostitele aplikace. Příklady jsou názvy počítačů pro fyzického serveru a hostitele virtuálních počítačů nebo název instance pro cloudové služby.  Jedinou výjimkou je aplikace, která používá pouze [.NET Core](https://dotnet.github.io/) a sadu SDK Application Insights Core. V takovém případě je pro všechny hostitele hlášen pouze jeden uzel, protože název hostitele není k dispozici. 
  * V případě starších verzí sady SDK se [Webová sada SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) chová jako novější verze sady SDK, ale [základní sada SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) hlásí pouze jeden uzel, bez ohledu na počet hostitelů aplikace. 
  * Pokud vaše aplikace používá sadu SDK k nastavení **roleInstance** na vlastní hodnotu, je ve výchozím nastavení použita stejná hodnota k určení počtu uzlů. 
  * Pokud používáte novou verzi sady SDK s aplikací, která běží z klientských počítačů nebo mobilních zařízení, počet uzlů může vracet velký počet velmi velkých (z důvodu velkého počtu klientských počítačů nebo mobilních zařízení). 

## <a name="automation"></a>Automation

Pomocí správy prostředků Azure můžete napsat skript pro nastavení cenové úrovně. [Zjistěte jak](powershell.md#price).


## <a name="next-steps"></a>Další kroky

* [Vzorkování](../../azure-monitor/app/sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ../../azure-monitor/app/app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/