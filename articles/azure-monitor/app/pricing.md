---
title: Správa využití a nákladů pro Azure Application Insights | Dokumenty společnosti Microsoft
description: Spravujte telemetrické svazky a monitorujte náklady v Application Insights.
ms.topic: conceptual
author: DaleKoetke
ms.author: dalek
ms.date: 11/27/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 9ecd0ffd76650efff3a4c9f877522cba6f28d080
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81271110"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Správa využití a nákladů pro službu Application Insights

> [!NOTE]
> Tento článek popisuje, jak porozumět a řídit vaše náklady pro Application Insights.  Související článek [Sledování využití a odhadované náklady](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) popisuje, jak zobrazit využití a odhadované náklady napříč několika funkcemi monitorování Azure pro různé cenové modely.

Application Insights je navržený tak, aby získal vše, co potřebujete ke sledování dostupnosti, výkonu a využití vašich webových aplikací, ať už jsou hostované v Azure nebo místně. Application Insights podporuje oblíbené jazyky a architektury, jako jsou .NET, Java a Node.js, a integruje se s devOps procesy a nástroji, jako je Azure DevOps, Jira a PagerDuty. Je důležité pochopit, co určuje náklady na monitorování aplikací. V tomto článku zkontrolujeme, co řídí náklady na monitorování aplikací a jak je můžete proaktivně sledovat a řídit.

Pokud máte otázky ohledně toho, jak ceny pro Application Insights fungují, můžete na našem [fóru](https://social.msdn.microsoft.com/Forums/home?forum=ApplicationInsights&filter=alltypes&sort=lastpostdesc)odeslat dotaz .

## <a name="pricing-model"></a>Cenový model

Ceny pro [Azure Application Insights][start] je model s **průběžným platbou** založený na objemu dat, který je ingedován a volitelně pro delší uchovávání dat. Každý prostředek Application Insights se účtuje jako samostatná služba a přispívá k účtovce za vaše předplatné Azure. Objem dat se měří jako velikost nekomprimovaného balíčku dat JSON, který obdrží Application Insights z vaší aplikace. Za použití [streamu živých metrik](../../azure-monitor/app/live-stream.md)se neúčtuje žádný poplatek za objem dat .

[Za vícestupňové webové testy](../../azure-monitor/app/availability-multistep.md) se účtuje příplatek. Vícekrokové webové testy jsou webové testy, které provádějí posloupnost akcí. Neexistuje žádný samostatný poplatek za *ping testy* jedné stránky. Telemetrie z ping testů a vícekrokových testů se účtuje stejně jako ostatní telemetrie z vaší aplikace.

Možnost Application Insights [povolit výstrahy na vlastní dimenze metriky](https://docs.microsoft.com/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics#custom-metrics-dimensions-and-pre-aggregation) můžete také generovat dodatečné náklady, protože to může mít za následek vytvoření další chod metriky předběžné agregace. [Další informace] o protokolech založené a předem agregované metriky v Application Insights a o [cenách](https://azure.microsoft.com/pricing/details/monitor/) pro vlastní metriky Azure Monitoru.

## <a name="estimating-the-costs-to-manage-your-application"></a>Odhad nákladů na správu aplikace

Pokud ještě nepoužíváte Application Insights, můžete pomocí [cenové kalkulačky Azure Monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) odhadnout náklady na používání Application Insights. Začněte zadáním "Azure Monitor" do vyhledávacího pole a kliknutím na výslednou dlaždici Azure Monitor. Posuňte se dolů po stránce na Azure Monitor a v rozevíracím seznamu Typ vyberte Přehledy aplikací.  Zde můžete zadat počet GB dat, které očekáváte, že shromáždíte za měsíc, takže otázkou je, kolik dat bude Application Insights shromažďovat sledování vaší aplikace.

Existují dva přístupy k řešení tohoto: použití výchozí monitorování a adaptivní vzorkování, který je k dispozici v ASP.NET SDK nebo odhadnout vaše pravděpodobné požití dat na základě toho, co ostatní podobné zákazníky viděli.

### <a name="data-collection-when-using-sampling"></a>Sběr dat při použití vzorkování

Díky [adaptivnímu vzorkování](sampling.md#adaptive-sampling)sady ASP.NET SDK je objem dat automaticky upraven tak, aby byl pro výchozí monitorování application insights nastaven tak, aby byl v rámci zadané maximální rychlosti provozu. Pokud aplikace vytváří nízké množství telemetrie, například při ladění nebo z důvodu nízké využití, položky nebudou vynechány vzorkovací procesor, pokud je svazek pod nakonfigurované události za sekundu úrovně. U aplikace s velkým objemem s výchozí prahovou hodnotou pět událostí za sekundu adaptivní vzorkování omezí počet denních událostí na 432 000. Použití typické průměrné velikosti události 1 KB to odpovídá 13,4 GB telemetrie za 31denní měsíc na uzel hostující vaši aplikaci (protože vzorkování se provádí místně pro každý uzel.) 

Pro sady SDK, které nepodporují adaptivní vzorkování, můžete použít [vzorkování ingestování](https://docs.microsoft.com/azure/azure-monitor/app/sampling#ingestion-sampling), které vzorkování, které vzorkování při přijetí dat application insights na základě procenta dat zachovat, nebo [s pevnou frekvencí vzorkování pro ASP.NET, ASP.NET Core a Java webové stránky](sampling.md#fixed-rate-sampling) ke snížení provozu odeslaného z webového serveru a webových prohlížečů

### <a name="learn-from-what-similar-customers-collect"></a>Zjistěte, z čeho podobní zákazníci sbírají

Pokud v kalkulačce cen monitorování Azure pro application insights povolíte funkci Odhad objemu dat na základě aktivity aplikace, můžete poskytnout vstupy o vaší aplikaci (požadavky za měsíc a zobrazení stránek za měsíc, v případě, že budete shromažďovat telemetrii na straně klienta) a pak vám kalkulačka sdělí střední a 90% množství dat shromážděných podobnými aplikacemi. Tyto aplikace pokrývají rozsah konfigurace Application Insights (např. některé mají výchozí [vzorkování](../../azure-monitor/app/sampling.md), některé nemají vzorkování atd.), takže stále máte ovládací prvek pro snížení objemu dat, která ingestujete hluboko pod střední úroveň pomocí vzorkování. Ale to je výchozí bod pochopit, co ostatní, podobné zákazníci vidí.

## <a name="understand-your-usage-and-estimate-costs"></a>Pochopení využití a odhad nákladů

Application Insights usnadňuje pochopení toho, jaké jsou vaše náklady pravděpodobně na základě nedávných vzorců použití. Pokud chcete začít, na webu Azure portal, pro prostředek Application Insights, přejděte na stránku **Využití a odhadované náklady:**

![Výběr cen](./media/pricing/pricing-001.png)

A. Zkontrolujte objem dat za měsíc. To zahrnuje všechna data, která jsou přijata a uchovávána (po každém [vzorkování)](../../azure-monitor/app/sampling.md)ze serveru a klientských aplikací a z testů dostupnosti.  
B. Pro [vícestupňové webové testy](../../azure-monitor/app/availability-multistep.md)se účtuje zvláštní poplatek . (To nezahrnuje jednoduché testy dostupnosti, které jsou zahrnuty v poplatku za objem dat.)  
C. Zobrazení trendů objemu dat za poslední měsíc.  
D. Povolit [vzorkování](../../azure-monitor/app/sampling.md)při požití dat .
E. Nastavte denní limit objemu dat.  

(Všimněte si, že všechny ceny zobrazené na snímcích obrazovky v tomto článku jsou pouze pro účely. Aktuální ceny v měně a oblasti najdete v tématu [Application Insights pricing][pricing].)

Chcete-li prozkoumat využití přehledů aplikací hlouběji, otevřete stránku **Metriky,** přidejte metriku s názvem "Objem datového bodu" a pak vyberte možnost *Použít rozdělení,* chcete-li rozdělit data podle typu položky Telemetrie.

Poplatky za přehledy aplikací se přidají k vaší vyúčtování Azure. Podrobnosti o účtu Azure najdete v části **Fakturace** na webu Azure Portal nebo na [fakturačním portálu Azure](https://account.windowsazure.com/Subscriptions).

![V levé nabídce vyberte Fakturace](./media/pricing/02-billing.png)

### <a name="using-data-volume-metrics"></a>Použití metrik objemu dat
<a id="understanding-ingested-data-volume"></a>

Chcete-li získat další informace o svazcích dat, když vyberete **metriky** pro váš prostředek Application Insights, přidejte nový graf. Pro metriku grafu vyberte v části **Metriky založené na protokolu** **objem datových bodů**. Klepněte na **tlačítko Použít rozdělení**a vyberte skupinu podle ** `Telemetryitem` typu**.

![Použití metrik k zobrazení objemu dat](./media/pricing/10-billing.png)

### <a name="queries-to-understand-data-volume-details"></a>Dotazy k pochopení podrobností svazku dat

Existují dva přístupy k zkoumání svazků dat pro Application Insights. První používá agregované `systemEvents` informace v tabulce a `_BilledSize` druhý používá vlastnost, která je k dispozici na každé události ingestované.

#### <a name="using-aggregated-data-volume-information"></a>Použití informací o agregovaném objemu dat

Tabulku můžete například `systemEvents` použít k zobrazení objemu dat, který byl s dotazem poveden za posledních 24 hodin:

```kusto
systemEvents
| where timestamp >= ago(24h)
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

Chcete-li zobrazit graf objemu dat (v bajtů) podle typu dat za posledních 30 dní, můžete použít:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes) by BillingTelemetryType, bin(timestamp, 1d) | render barchart  
```

Všimněte si, že tento dotaz lze použít v [protokolu Azure Alert](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log) nastavit výstrahy na svazcích dat.  

Chcete-li získat další informace o změnách telemetrických dat, můžeme získat počet událostí podle typu pomocí dotazu:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| summarize count() by BillingTelemetryType, bin(timestamp, 1d)
| render barchart  
```

#### <a name="using-data-size-per-event-information"></a>Použití velikosti dat pro informace o události

Chcete-li získat další podrobnosti o zdroji svazků `_BilledSize` dat, můžete použít vlastnost, která je k dispozici na každé události požití.

Chcete-li například podívat, které operace generují nejvíce objemu `_BilledSize` dat za posledních 30 dní, můžeme součet pro všechny události závislostí:

```kusto
dependencies
| where timestamp >= startofday(ago(30d))
| summarize sum(_BilledSize) by operation_Name
| render barchart  
```

## <a name="viewing-application-insights-usage-on-your-azure-bill"></a>Zobrazení využití přehledů aplikací na vaší vyúčtování Azure

Azure poskytuje velké množství užitečných funkcí v centru [Azure Cost Management + Billing.](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) Například funkce "Analýza nákladů" umožňuje zobrazit vaše výdaje za prostředky Azure. Přidání filtru podle typu prostředku (do microsoft.insights/components for Application Insights) vám umožní sledovat výdaje.

Větší pochopení vašeho využití lze získat [stažením využití z webu Azure Portal](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal).
Ve stažené tabulce uvidíte využití na prostředek Azure za den. V této tabulce aplikace Excel lze využití z prostředků Application Insights najít nejprve filtrováním ve sloupci Kategorie měřiče, který zobrazuje "Application Insights" a "Log Analytics", a přidáním filtru do sloupce Instanci, který obsahuje microsoft.insights/komponenty.  Většina application insights využití se hlásí na měřiče s kategorie měřiče Log Analytics, protože existuje jeden back-end protokolů pro všechny součásti Azure Monitor.  Pouze prostředky Application Insights na starších cenových úrovních a vícekrokových webových testech jsou hlášeny s kategorií měřičů application insights.  Využití je zobrazeno ve sloupci "Spotřebované množství" a jednotka pro každou položku je zobrazena ve sloupci Měrná jednotka.  K dispozici jsou další podrobnosti, které vám pomohou [pochopit vaši fakturu za Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-understand-your-bill).

## <a name="managing-your-data-volume"></a>Správa objemu dat

Objem odesílaných dat lze spravovat pomocí následujících technik:

* **Vzorkování**: Vzorkování můžete použít ke snížení množství telemetrie, která je odeslána z vašeho serveru a klientských aplikací, s minimálním zkreslením metrik. Vzorkování je primární nástroj, který můžete použít k vyladění množství odesílaných dat. Přečtěte si další informace o [funkcích vzorkování](../../azure-monitor/app/sampling.md).

* **Limit Ajax volání**: Můžete [omezit počet hovorů Ajax, které mohou být hlášeny](../../azure-monitor/app/javascript.md#configuration) v každém zobrazení stránky, nebo vypnout Ajax hlášení.

* **Zakázat nepotřebné moduly**: [Úprava applicationinsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) chcete-li vypnout moduly kolekce, které nepotřebujete. Můžete například rozhodnout, že čítače výkonu nebo data závislostí jsou nepodstatné.

* **Předběžné agregace metriky**: Pokud v aplikaci zavoláte na TrackMetric, můžete snížit provoz pomocí přetížení, které přijímá výpočet průměrné a směrodatné odchylky dávky měření. Nebo můžete použít [balíček předběžné agregace](https://www.myget.org/gallery/applicationinsights-sdk-labs).
 
* **Denní limit:** Když na webu Azure Portal vytvoříte prostředek Application Insights, denní limit se nastaví na 100 GB/den. Při vytváření prostředku Application Insights v sadě Visual Studio je výchozí hodnota malá (pouze 32,3 MB/den). Výchozí denní limit je nastaven pro usnadnění testování. Je určena, že uživatel zvýší denní limit před nasazením aplikace do produkčního prostředí. 

    Maximální limit je 1 000 GB/den, pokud nepožadujete vyšší maximum pro aplikaci s vysokým provozem.
    
    Upozornění e-maily o denní limit jsou odesílány na účet, které jsou členy těchto rolí pro váš prostředek Application Insights: "ServiceAdmin", "AccountAdmin", "CoAdmin", "Vlastník".

    Při nastavovat denní limit se opatrně zaměňovat. Vaším záměrem by mělo být *nikdy hit denní limit*. Pokud stisknete denní limit, ztratíte data po zbytek dne a nemůžete sledovat aplikaci. Chcete-li změnit denní limit, použijte možnost **Denní limit hlasitosti.** K této možnosti můžete přistupovat v podokně **Využití a odhadované náklady** (to je podrobněji popsáno dále v článku).
    
    Odebrali jsme omezení u některých typů předplatného, které mají kredit, který se nedají použít pro Přehledy aplikací. Dříve, pokud má předplatné limit útraty, obsahuje dialogové okno denního limitu pokyny k odebrání limitu útraty a povolení zvýšení denního limitu nad 32,3 MB/den.
    
* **Omezení**: Omezení omezuje rychlost dat na 32 000 událostí za sekundu, zprůměrované přes 1 minutu na klíč instrumentace. Objem dat, která vaše aplikace odesílá, se vyhodnocuje každou minutu. Pokud překročí průměrnou sazbu za sekundu za minutu, server odmítne některé požadavky. Sada SDK uskladní data a pokusí se je znovu odeslat. To se šíří z nárůstu v průběhu několika minut. Pokud vaše aplikace konzistentně odesílá data rychlostí, než je rychlost omezení, některá data se vymažou. (Sady SDK ASP.NET, Java a JavaScript se pokoušejí znovu odeslat data tímto způsobem; jiné sady SDK mohou jednoduše omezit omezená data.) Pokud dojde k omezení, upozornění upozornění, že k tomu došlo.

## <a name="manage-your-maximum-daily-data-volume"></a>Správa maximálního denního objemu dat

Pomocí denního limitu hlasitosti můžete shromažďovat shromažďovaná data. Pokud je však splněna limit, dojde ke ztrátě všech telemetrie odeslaných z vaší aplikace pro zbytek dne. Není *vhodné,* aby vaše aplikace hit denní limit. Po dosažení denního limitu nelze sledovat stav a výkon aplikace.

Místo použití denního limitu hlasitosti použijte [vzorkování](../../azure-monitor/app/sampling.md) k vyladění objemu dat na požadovanou úroveň. Potom použijte denní limit pouze jako "poslední možnost" v případě, že vaše aplikace neočekávaně začne odesílat mnohem vyšší objemy telemetrie.

### <a name="identify-what-daily-data-limit-to-define"></a>Určení denního datového limitu, který chcete definovat

Zkontrolujte využití přehledů aplikací a odhadované náklady, abyste pochopili trend ingestování dat a jaký je denní limit svazku, který chcete definovat. Mělo by být zváženo s opatrností, protože nebudete moci sledovat své prostředky po dosažení limitu.

### <a name="set-the-daily-cap"></a>Nastavení denního limitu

Chcete-li změnit denní limit, vyberte v části **Konfigurace** prostředku Application Insights na stránce **Využití a odhadované náklady** položku Denní **limit**.

![Nastavení denního limitu hlasitosti telemetrie](./media/pricing/pricing-003.png)

Chcete-li [změnit denní limit prostřednictvím Správce prostředků Azure](../../azure-monitor/app/powershell.md), vlastnost změnit je . `dailyQuota`  Pomocí Správce prostředků Azure můžete `dailyQuotaResetTime` také nastavit a `warningThreshold`denní limit .

### <a name="create-alerts-for-the-daily-cap"></a>Vytváření upozornění pro denní limit

Denní zakončení Application Insights vytvoří událost v protokolu aktivit Azure, když svazky ingace dat narazí na úroveň upozornění nebo na úroveň denního limitu.  Můžete [vytvořit výstrahu na základě těchto událostí protokolu aktivit](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log#create-with-the-azure-portal). Názvy signálů pro tyto události jsou:

* Dosažena prahová hodnota upozornění na denní limit komponenty Application Insights

* Dosaženo denního limitu komponenty Application Insights

## <a name="sampling"></a>Vzorkování
[Vzorkování](../../azure-monitor/app/sampling.md) je metoda snížení rychlosti, jakou je telemetrie odesílána do vaší aplikace, při zachování možnosti najít související události během diagnostického vyhledávání. Můžete také zachovat správné počty událostí.

Vzorkování je účinný způsob, jak snížit poplatky a zůstat v rámci své měsíční kvóty. Algoritmus vzorkování zachovává související položky telemetrie, takže například při použití funkce Hledat můžete najít požadavek související s určitou výjimkou. Algoritmus také zachová správné počty, takže se v Průzkumníku metrik zobrazí správné hodnoty pro sazby požadavků, míry výjimek a další počty.

Existuje několik forem odběru vzorků.

* [Adaptivní vzorkování](../../azure-monitor/app/sampling.md) je výchozí pro ASP.NET SDK. Adaptivní vzorkování se automaticky přizpůsobí objemu telemetrie, kterou vaše aplikace odesílá. Pracuje automaticky v sdk ve webové aplikaci tak, aby telemetrické provozy v síti je snížena. 
* *Vzorkování ingestování* je alternativa, která funguje v místě, kde telemetrie z vaší aplikace zadá službu Application Insights. Vzorkování ingestování nemá vliv na objem telemetrie odeslané z vaší aplikace, ale snižuje svazek, který je zachován službou. Vzorkování ingestování můžete použít ke snížení kvóty, která se používá telemetrie z prohlížečů a dalších sad SDK.

Chcete-li nastavit vzorkování při požití, přejděte do podokna **Ceny:**

![V podokně Kvóta a ceny vyberte dlaždici Ukázky a pak vyberte vzorkovací zlomek.](./media/pricing/pricing-004.png)

> [!WARNING]
> Podokno **vzorkování dat** řídí pouze hodnotu vzorkování připožití. Neodráží vzorkovací rychlost, kterou používá sada Application Insights SDK ve vaší aplikaci. Pokud příchozí telemetrie již byla vzorkována v sdk, odběr vzorků ingestování není použita.
>

Chcete-li zjistit skutečnou vzorkovací frekvenci bez ohledu na to, kde byla použita, použijte [dotaz Analytics](analytics.md). Dotaz vypadá takto:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

V každém uchovávaný záznam `itemCount` označuje počet původních záznamů, které představuje. Rovná se 1 + počtu předchozích zahozených záznamů.

## <a name="change-the-data-retention-period"></a>Změna doby uchovávání dat

Výchozí uchovávání informací pro prostředky Application Insights je 90 dní. Pro každý prostředek Application Insights lze vybrat různá období uchovávání informací. Úplná sada dostupných retenčních období je 30, 60, 90, 120, 180, 270, 365, 550 nebo 730 dní.

Chcete-li změnit uchovávání informací, přejděte ze zdroje Application Insights na stránku **Využití a odhadované náklady** a vyberte možnost Uchovávání **dat:**

![Nastavení denního limitu hlasitosti telemetrie](./media/pricing/pricing-005.png)

Uchovávání lze také [nastavit programově pomocí prostředí PowerShell](powershell.md#set-the-data-retention) pomocí parametru. `retentionInDays` Pokud je uchování sníženo, existuje několik dní odkladu před odebráním nejstarších dat. Pokud nastavíte uchovávání dat na 30 dní, můžete aktivovat `immediatePurgeDataOn30Days` okamžité vymazání starších dat pomocí parametru, což může být užitečné pro scénáře související s dodržováním předpisů. Tato funkce vymazání je kontumačně zpřístupněna jenom prostřednictvím Správce prostředků Azure a měla by se používat s maximální opatrností. Denní čas obnovení limitu svazku dat lze nakonfigurovat `dailyQuotaResetTime` pomocí Správce prostředků Azure k nastavení parametru.

## <a name="data-transfer-charges-using-application-insights"></a>Poplatky za přenos dat pomocí přehledů aplikací

Odesílání dat do Application Insights může být účtovánpoplatek za šířku pásma dat. Jak je popsáno na [stránce s cenami šířky pásma Azure](https://azure.microsoft.com/pricing/details/bandwidth/), přenos dat mezi službami Azure umístěnými ve dvou oblastech se účtuje jako odchozí přenos dat normální masovou rychlostí. Příchozí přenos dat je zdarma. Tento poplatek je však velmi malý (málo %) v porovnání s náklady na ingestování dat protokolu Application Insights. V důsledku toho řízení nákladů pro Log Analytics musí zaměřit na svazek ingestovaných dat a máme pokyny, které vám pomohou pochopit, že [zde](https://docs.microsoft.com/azure/azure-monitor/app/pricing#managing-your-data-volume).

## <a name="limits-summary"></a>Souhrn limitů

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Zakázání e-mailů denního limitu

Chcete-li zakázat e-maily s denním limitem svazku, vyberte v podokně Využití a odhadované náklady v části **Konfigurovat** prostředek Přehledy aplikací v podokně **Využití a odhadované náklady** **položku Denní omezení**. Existují nastavení pro odesílání e-mailů po dosažení limitu, stejně jako při dosažení nastavitelné úrovně upozornění. Pokud chcete zakázat všechny denní e-maily související s limitem, zaškrtněte obě políčka.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Cenová úroveň Starší verze rozlehlé sítě (na uzel)

Pro první uživatele Azure Application Insights stále existují dvě možné cenové úrovně: Základní a Enterprise. Základní cenová úroveň je stejná, jak je popsáno výše a je výchozí úroveň. Obsahuje všechny funkce úrovně Enterprise bez dalších nákladů. Základní úroveň účty především na objem dat, která je ingestována.

> [!NOTE]
> Tyto starší cenové úrovně byly přejmenovány. Cenová úroveň Enterprise se nyní nazývá **Per Node** a základní cenová úroveň se nyní nazývá **za GB**. Tyto nové názvy se používají níže a na webu Azure Portal.  

Úroveň Na uzel (dříve Enterprise) má poplatek za uzel a každý uzel obdrží denní datový příspěvek. V cenové úrovni podle uzlu se vám budou účtovat data požitá nad zahrnutou povolenku. Pokud používáte Operations Management Suite, měli byste zvolit úroveň Na uzel.

Aktuální ceny v měně a oblasti najdete v [tématu Application Insights pricing](https://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> V dubnu 2018 jsme [představili](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) nový cenový model pro monitorování Azure. Tento model využívá jednoduchý model průběžných plateb v celém portfoliu monitorovacích služeb. Další informace o [novém cenovém modelu](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), jak [posoudit dopad přechodu na tento model](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#understanding-your-azure-monitor-costs) na základě vašich vzorců použití a jak se [přihlásit do nového modelu](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#azure-monitor-pricing-model)

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>Nároky předplatného na úroveň uzlové a Operations Management Suite

Zákazníci, kteří si zakoupí sady Operations Management Suite E1 a E2, mohou získat přehledy aplikací na uzel jako další součást bez dalších nákladů, jak [bylo dříve oznámeno](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/). Konkrétně každá jednotka Operations Management Suite E1 a E2 obsahuje nárok na jeden uzel aplikační chkazatelna na úroveň uzlu. Každý uzel Application Insights obsahuje až 200 MB dat počinených za den (odděleně od ingestování dat log analytics) s 90denním uchováváním dat bez dalších nákladů. Úroveň je popsána podrobněji dále v článku.

Vzhledem k tomu, že tato úroveň je použitelná pouze pro zákazníky s předplatným Sady Operations Management Suite, zákazníci, kteří nemají předplatné Sady Operations Management Suite, nevidí možnost vybrat tuto úroveň.

> [!NOTE]
> Chcete-li zajistit, že získáte tento nárok, vaše aplikace Insights prostředky musí být v cenové úrovni podle uzlu. Toto oprávnění platí pouze jako uzly. Prostředky Application Insights na úrovni Za GB si neuvědomují žádnou výhodu. Toto oprávnění není viditelné v odhadovaných nákladech uvedených v podokně **Využití a odhadované náklady.** Pokud také přesunete předplatné na nový cenový model monitorování Azure v dubnu 2018, úroveň na GB je jediná dostupná úroveň. Přesunutí předplatného na nový cenový model monitorování Azure není vhodné, pokud máte předplatné Sady Operations Management Suite.

### <a name="how-the-per-node-tier-works"></a>Jak funguje úroveň Na uzel

* Platíte za každý uzel, který odesílá telemetrická data pro všechny aplikace na úrovni Per Node.
  * *Uzel* je fyzický nebo virtuální serverový počítač nebo instance role platformy jako služby, která hostuje vaši aplikaci.
  * Vývojové počítače, klientské prohlížeče a mobilní zařízení se nepočítají jako uzly.
  * Pokud vaše aplikace má několik součástí, které odesílají telemetrii, jako je například webová služba a back-endový pracovník, součásti se počítají samostatně.
  * [Data živého streamování metrik](../../azure-monitor/app/live-stream.md) se pro účely stanovení cen nezapočítávají. V předplatném se poplatky účtují za uzel, ne za aplikaci. Pokud máte pět uzlů, které odesílají telemetrii pro 12 aplikací, poplatek je pro pět uzlů.
* Přestože poplatky jsou uvedeny za měsíc, jsou účtovány pouze za každou hodinu, ve které uzel odesílá telemetrii z aplikace. Hodinový poplatek je citovaný měsíční poplatek vydělený 744 (počet hodin v 31denním měsíci).
* Přidělení objemu dat 200 MB za den je uveden pro každý uzel, který je zjištěn (s hodinovou rozlišovací schopnost). Nevyužité přidělení dat se nepřenáší ze dne na den.
  * Pokud zvolíte cenovou úroveň podle uzlu, každé předplatné získá denní příspěvek dat na základě počtu uzlů, které odesílají telemetrii do prostředků Application Insights v tomto předplatném. Takže pokud máte pět uzlů, které odesílají data celý den, budete mít sdružený příspěvek 1 GB použít na všechny prostředky Application Insights v tomto předplatném. Nezáleží na tom, jestli některé uzly odesílají více dat než jiné uzly, protože zahrnutá data jsou sdílena ve všech uzlech. Pokud v daný den, application insights prostředky získat více dat, než je zahrnuto v denní přidělení dat pro toto předplatné, poplatky za gb nadlimitní přenos dat. 
  * Denní datový příspěvek se vypočítá jako počet hodin v den (pomocí UTC), který každý uzel odešle telemetrii děleno 24 vynásobené 200 MB. Takže pokud máte čtyři uzly, které odesílají telemetrii během 15 z 24 hodin v den, zahrnutá data pro tento den by ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB. Za cenu 2,30 USD za GB za překročení dat by poplatek byl 1.15 USD, pokud uzly odešlou 1 GB dat v ten den.
  * Denní příspěvek na úroveň uzlu se nesdílí s aplikacemi, pro které jste zvolili úroveň za GB. Nevyužitý příspěvek se nepřenáší ze dne na den.

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Příklady určení odlišného počtu uzlů

| Scénář                               | Celkový počet denních uzlů |
|:---------------------------------------|:----------------:|
| 1 aplikace používající 3 instance služby Azure App Service a 1 virtuální server | 4 |
| 3 aplikace spuštěné na 2 virtuálních počítačích; Prostředky Application Insights pro tyto aplikace jsou ve stejném předplatném a na úrovni Per Node | 2 | 
| 4 aplikace, jejichž prostředky Aplikace Insights jsou ve stejném předplatném; každá aplikace spuštěná 2 instance během 16 mimo špičku a 4 instance během 8 špičkových hodin | 13.33 |
| Cloudové služby s 1 rolí pracovního procesu a 1 webovou rolí, z nichž každá používá 2 instance | 4 | 
| Cluster Azure Service Fabric s 50 uznami se systémem Azure Service Fabric se systémem 50 mikroslužeb; každá mikroslužba se systémem 3 instancí | 5|

* Přesné počítání uzlů závisí na tom, které application insights SDK vaše aplikace používá. 
  * Ve verzích Sady SDK 2.2 a novějších hlásí sada Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) i [webová sada SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) jako uzel každého hostitele aplikace. Příkladem je název počítače pro fyzické servery a hostitele virtuálních počítačů nebo název instance pro cloudové služby.  Jedinou výjimkou je aplikace, která používá pouze [.NET Core](https://dotnet.github.io/) a Application Insights Core SDK. V takovém případě je pro všechny hostitele hlášen pouze jeden uzel, protože název hostitele není k dispozici.
  * Pro starší verze sady SDK [se sada Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) chová jako novější verze sady SDK, ale sada Core [SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) hlásí pouze jeden uzel bez ohledu na počet hostitelů aplikace.
  * Pokud vaše aplikace používá sadu SDK k nastavení **instance role** na vlastní hodnotu, ve výchozím nastavení se stejná hodnota používá k určení počtu uzlů.
  * Pokud používáte novou verzi sady SDK s aplikací, která běží z klientských počítačů nebo mobilních zařízení, počet uzlů může vrátit číslo, které je velké (z důvodu velkého počtu klientských počítačů nebo mobilních zařízení).

## <a name="automation"></a>Automation

Můžete napsat skript pro nastavení cenové úrovně pomocí Azure Resource Management. [Zjistěte jak](powershell.md#price).

## <a name="next-steps"></a>Další kroky

* [Vzorkování](../../azure-monitor/app/sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ../../azure-monitor/app/app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/