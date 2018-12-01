---
title: Správa cen a objemů dat pro Azure Application Insights | Dokumentace Microsoftu
description: Správa svazků telemetrii a sledování nákladů ve službě Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: Dale.Koetke
ms.date: 08/11/2018
ms.author: mbullwin
ms.openlocfilehash: c2aec4f48febb6ff8f7f7c14202011784288b200
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52729581"
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Správa cen a objemů dat ve službě Application Insights

> [!NOTE]
> Tento článek popisuje, jak analyzovat využití dat Application Insights.  Přečtěte si následující články související informace.
> - [Monitorování využití a odhadované náklady](../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) popisuje, jak zobrazit využití a odhadované náklady napříč více funkcí pro různé cenové modely pro monitorování Azure. Také popisuje, jak změnit cenový model.

Ceny za [Azure Application Insights] [ start] se podle objemu dat na aplikaci. Každý prostředek Application Insights se účtuje jako samostatná služba a přispívá k vyúčtování předplatného Azure.

Application Insights má dvě cenové plány: Basic a Enterprise. Základní cenový plán je výchozí plán. Zahrnuje všechny funkce plánu Enterprise, bez dalších poplatků. Základní plán účtuje především podle objemu dat, který se ingestuje. 

Plán Enterprise je poplatek za uzel a každý uzel přijme denní příděl dat. V podnikové síti cenový plán, bude vám účtována dat přijatých nad zahrnutý příspěvek. Pokud používáte Operations Management Suite, měli byste zvolit plán Enterprise. 

Pokud máte dotazy týkající se ukázky cen pro službu Application Insights, můžete odeslat dotaz v našich [fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights).

## <a name="pricing-plans"></a>Cenové plány

Aktuální ceny měny a oblasti, naleznete v tématu [ceny za Application Insights][pricing].

> [!NOTE]
> V dubnu 2018 jsme [zavedené](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) nový cenový model pro monitorování Azure. Tento model přijímá mezi kompletní portfolio služeb monitorování jednoduchý model "s průběžnými platbami". Další informace o [nový cenový model](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), jak k [posoudit dopad přechodu na tento model](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) založené na vaše vzorce používání a [tom, jak začít používat nový model](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model).

### <a name="basic-plan"></a>Základní plán

Základní plán je výchozí cenový plán, když se vytvoří nový prostředek Application Insights. Základní plán je optimální pro všechny zákazníky s výjimkou těch, kteří mají předplatné Operations Management Suite.

* V plánu Basic se účtují za objem dat. Objem dat je počet bajtů přijatých službou Application Insights telemetrická data. Objem dat se měří jako velikost nekomprimovaných balíček dat JSON, které se získaly službou Application Insights z vaší aplikace. Pro [tabulková data importovat do analýzy](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), objem dat se měří jako velikost nekomprimovaných souborů, které se odesílají do Application Insights.
* Poplatky za objem dat vaší aplikace jsou teď hlášeny na nové účtování počítat s názvem **Ingestování** od dubna 2018. Toto je nový měřič sdílet mezi technologií, jako jsou aplikace Insights a Log Analytics pro monitorování a v části název služby je aktuálně **App Services** (a brzy změnu na **Log Analytics**). 
* [Live Metrics Stream](app-insights-live-stream.md) dat se nepočítá ceny účely.
* [Průběžný export](app-insights-export-telemetry.md) a [Azure Log Analytics connector](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409) jsou k dispozici bez dalších poplatků v plánu Basic od dubna 2018.

### <a name="enterprise-plan-and-operations-management-suite-subscription-entitlements"></a>Podnikový plán a nároky na Operations Management Suite předplatné

Zákazníci, kteří si koupí Operations Management Suite E1 a E2 mohou získat Application Insights Enterprise jako další komponentu bez dalších poplatků jako [jsme oznámili už dřív](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/). Konkrétně každá jednotka Operations Management Suite E1 a E2 zahrnuje nárok na jeden uzel Application Insights Enterprise plánu. Každý uzel Application Insights zahrnuje až 200 MB dat přijatých za den (oddělené od Log Analytics ingestování), s uchováním dat 90 dnů bez dalších poplatků. Tento plán je popsaná v podrobnější později v tomto článku. 

Vzhledem k tomu, že tento plán je určené jenom pro zákazníky s předplatným Operations Management Suite, zákazníky, kteří nemají předplatného služby Operations Management Suite nezobrazí možnost vybrat tento plán.

> [!NOTE]
> Aby bylo zajištěno, že dostanete tento nárok, musí být prostředky Application Insights v podnikové síti cenový plán. Tato oprávnění se vztahuje jenom jako uzly. Prostředky Application Insights v plánu Basic ani neuvědomujete žádnou výhodu. Tato oprávnění nejsou viditelná v ukazuje odhadované náklady **využití a odhadované náklady** podokně. Navíc pokud přesunete předplatné pro nový Azure sledování cenový model platný od dubna 2018, základní plán je k dispozici pouze plán. Přesun předplatného na nový cenový model sledování Azure není žádoucí, pokud máte předplatné Operations Management Suite.

Další informace o plánu Enterprise najdete v tématu [podnikové informace o cenách](app-insights-pricing-enterprise-details.md).

### <a name="multi-step-web-tests"></a>Vícekrokové webové testy

[Vícekrokové webové testy](app-insights-monitor-web-app-availability.md#multi-step-web-tests) účtovat další poplatky. Vícekrokové webové testy jsou webové testy, které provádějí posloupnost akcí.

Neexistuje žádný samostatný poplatek za *odešlete zprávu ping testy* o jednu stránku. Telemetrie z testy a testy vícekrokového je účtované stejně jako další telemetrie z vaší aplikace.

## <a name="review-pricing-plans-and-estimate-costs"></a>Projděte si cenové plány a odhad nákladů

Application Insights usnadňuje pochopení věnovaného cenovým plánům, které jsou k dispozici a jaké náklady budou pravděpodobně možné na základě nedávné způsobů využití. Začínáme na portálu Azure pro prostředek služby Application Insights, přejděte **využití a odhadované náklady** podokna:

![Výběr cenové](./media/app-insights-pricing/pricing-001.png)

A. Zkontrolujte objem dat za měsíc. To zahrnuje všechna data, které obdržel a uchovávají (po všech [vzorkování](app-insights-sampling.md)) ze serveru a klientské aplikace a z testů dostupnosti.  
B. Samostatný poplatek za provedení [vícekrokové webové testy](app-insights-monitor-web-app-availability.md#multi-step-web-tests). (Nejsou obsaženy jednoduché dostupnost testů, které jsou součástí poplatku za objem dat.)  
C. Zobrazit trendy objemu dat za poslední měsíc.  
D. Povolení přijímání dat [vzorkování](app-insights-sampling.md).   
E. Nastavte denní limit objemu dat.  

Poplatky za Application Insights se přidají do vašeho vyúčtování služeb Azure. Můžete zobrazit podrobnosti o Azure fakturovat v **fakturace** části webu Azure portal, nebo v [fakturačním portálu Azure](https://account.windowsazure.com/Subscriptions). 

![V nabídce vlevo vyberte fakturace](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Přenosová rychlost
Objem dat odesílaných je omezená třemi způsoby:

* **Vzorkování**: vzorkování můžete použít ke snížení množství telemetrie odesílané z serverových a klientských aplikací, s minimální narušení metrik. Vzorkování je primárním nástrojem, který můžete použít k vyladění objemu dat, které odesíláte. Další informace o [vzorkování funkce](app-insights-sampling.md). 
* **Denní limit**: Když vytvoříte prostředek Application Insights na webu Azure Portal, denní limit je nastavený na 100 GB za den. Když vytvoříte prostředek Application Insights v sadě Visual Studio, použije se malé (pouze 32,3 MB/den). Denní limit výchozí nastavení je usnadnit testování. Předpokládá se, že uživatel vyvolá denního limitu před nasazením aplikace do produkčního prostředí. 

    Maximální limit je 1 000 GB za den, pokud požadujete vyšší maximum pro aplikace s vysokým provozem. 

    Péče o použijte, pokud nastavíte denní limit. Máte v úmyslu by mělo být *nikdy dosaženo denního limitu*. Když dosáhnou denního limitu můžete přijít o data po zbytek dne a není možné sledovat vaše aplikace. Chcete-li změnit denního limitu, použijte **denní limit objemu** možnost. Této možnosti v můžete přistupovat **využití a odhadované náklady** podokně (to je podrobněji popsaný dále v tomto článku).
    Odebrali jsme omezení pro některé typy předplatných, které mají kredit, který nelze použít pro službu Application Insights. Dříve Pokud předplatné limit útraty, denní limit dialogové okno obsahuje pokyny k odebrání limitu útraty a povolit denní limit zapříčinil nad 32,3 MB za den.
* **Omezení šířky pásma**: omezení rychlost dat na 32 000 událostí za sekundu, byla více než 1 minuty na jeden Instrumentační klíč.

*Co když Moje aplikace překračuje omezení frekvence?*

* Objem dat, která vaše aplikace odesílá, u kterého probíhá hodnocení každou minutu. Pokud se překročí sazba za sekundu zprůměrované za minutu, server odmítá některé požadavky. Sada SDK ukládá data do vyrovnávací paměti a poté se pokusí znovu poslat. Nárůst se rozkládá za několik minut. Pokud vaše aplikace konzistentně odesílá data na více než míru omezení, některá data se zahodí. (ASP.NET, Java a sady SDK pro jazyk JavaScript pokusí odeslat data tímto způsobem, dalších sad SDK může jednoduše rozevírací omezené data) Pokud dojde k omezení šířky pásma, upozornění oznámení vás upozorní, že k nim došlo.

*Jak zjistím, kolik dat Moje aplikace odesílá?*

Jeden z následujících možností můžete zobrazit, kolik dat vaše aplikace odesílá:

* Přejděte **využití a odhadované náklady** podokně se zobrazí denní objem graf data. 
* V Průzkumníku metrik přidejte nový graf. Metriky grafu vyberte **datového bodu svazku**. Zapnout **seskupení**a pak skupinu podle **datový typ**.

## <a name="reduce-your-data-rate"></a>Snížit rychlost vašich dat
Zde jsou některé kroky, které vám pomůžou snížit objem dat:

* Použití [vzorkování](app-insights-sampling.md). Tato technologie snižuje rychlost vašich dat bez zkosení metriky. Neztraťte možnost navigace mezi související položky v hledání. V serverové aplikace automaticky funguje vzorkování.
* [Omezit počet volání Ajax, které mohou být hlášeny](app-insights-javascript.md#detailed-configuration) v každé zobrazení stránky, nebo přepněte vypnout generování sestav Ajax.
* [Upravit soubor ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) vypnout kolekci modulů, které nepotřebujete. Například můžete rozhodnout, že jsou nedůležitých čítače výkonu nebo data závislostí.
* Rozdělte telemetrických dat mezi samostatnou instrumentačních klíčů. 
* Předem agregovaná metrika. Pokud volání TrackMetric vložíte do vaší aplikace, můžete zkrátit provoz pomocí přetížení, které přijímá výpočet průměru a směrodatné odchylky poměru dávku měření. Nebo můžete použít [předem agregovat balíčku](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="manage-the-maximum-daily-data-volume"></a>Správa maximální denní objem dat

Denní limit objemu můžete použít k omezení shromážděná data. Však při splnění zakončení dochází ke ztrátě všech telemetrická data odesílaná z vaší aplikace pro zbytek dne. Je *není vhodné* mít vaše aplikace dosažení denního limitu. Po dosažení denního limitu nelze sledovat stav a výkon vaší aplikace.

Namísto použití denní limit objemu, použijte [vzorkování](app-insights-sampling.md) k vyladění objemu dat na úrovni chcete. Potom použijte denního limitu pouze jako "poslední možnost" v případě, že vaše aplikace začíná neočekávaně k odesílání mnohem vyšší objemy telemetrických dat

Postup změny denní limit **konfigurovat** oddílu prostředku Application Insights v **využití a odhadované náklady** vyberte **denního limitu**.

![Upravit denní limit objemu telemetrických dat](./media/app-insights-pricing/pricing-003.png)

## <a name="sampling"></a>Vzorkování
[Vzorkování](app-insights-sampling.md) je metoda snížení míry, ve kterém se odesílají telemetrická data do vaší aplikace při zachování schopnost najít související události během diagnostické vyhledávání. Je také zachovat počty správné událostí.

Vzorkování je účinný způsob, jak snížit náklady a nepřekročíte s měsíční kvóty. Algoritmus vzorkování tak zachová související položky telemetrických dat, například při použití vyhledávání můžete vyhledat žádosti související s konkrétní výjimce. Algoritmus také zachovává správný počet takže se zobrazí v Průzkumníku metrik správné hodnoty pro požadavků, výjimek sazby a dalšími počty.

Existuje několik tvarů vzorkování.

* [Adaptivní vzorkování](app-insights-sampling.md) je výchozí nastavení sady SDK technologie ASP.NET. Adaptivního vzorkování automaticky přizpůsobí objemu telemetrických dat, která vaše aplikace odesílá. Funguje automaticky v sadě SDK ve webové aplikaci tak, aby snížení provozu telemetrie v síti. 
* *Vzorkování příjmu* představuje alternativu, která pracuje v místě, kde telemetrie z vaší aplikace do služby Application Insights. Vzorkování příjmu nemá vliv na množství telemetrie odesílané z vaší aplikace, ale zmenšuje svazek, který je uchován služby. Vzorkování příjmu můžete použít ke snížení kvótu, která je používané telemetrie z prohlížeče a dalších sad SDK.

Nastavení vzorkování příjmu, přejděte **ceny** podokna:

![Kvóty a cenové podokně vyberte dlaždici ukázky a pak vyberte zlomek vzorkování](./media/app-insights-pricing/pricing-004.png)

> [!WARNING]
> **Vzorkování dat** podokně řídí pouze hodnotu vzorkování příjmu. Neodráží vzorkovací frekvenci, který je používán jako sadu SDK Application Insights ve vaší aplikaci. Pokud již byly vzorkovány příchozích telemetrických dat v sadě SDK, se nepoužije vzorkování příjmu.
>

Chcete-li zjistit skutečné vzorkovací frekvenci, bez ohledu na to, kde je byla uplatněna, použijte [dotazu Analytics](app-insights-analytics.md). Dotaz vypadá takto:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

V každém uchovávají záznam, `itemCount` označuje počet původní záznamy, které představuje. Je rovno 1 + Počet zrušených předchozí záznamů. 

## <a name="automation"></a>Automation

Můžete napsat skript nastavení cenový plán s použitím Azure Resource Manageru. [Zjistěte jak](app-insights-powershell.md#price).

## <a name="limits-summary"></a>Souhrn omezení

[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Zakázat denní limit e-mailů

V části zakázání e-mailů denní objem zakončení **konfigurovat** oddílu prostředku Application Insights v **využití a odhadované náklady** vyberte **denního limitu** . Obsahují nastavení pro odeslání e-mailu, když je dosaženo zakončení, i když bylo dosaženo měnitelné úroveň upozornění. Pokud budete chtít zakázat všechny denní limit objemu související s e-mailů, zrušte zaškrtnutí políčka obou polí.

## <a name="next-steps"></a>Další postup

* [Vzorkování](app-insights-sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/