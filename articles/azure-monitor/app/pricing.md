---
title: Správa nákladů a využití pro službu Azure Application Insights | Dokumentace Microsoftu
description: Správa svazků telemetrii a sledování nákladů ve službě Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: Dale.Koetke
ms.date: 12/21/2018
ms.author: mbullwin
ms.openlocfilehash: 7fc6e415494399be12d8de8810b0551fb0b0d988
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54022576"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Správa nákladů a využití pro službu Application Insights

> [!NOTE]
> Tento článek popisuje, jak analyzovat využití dat Application Insights.  Přečtěte si následující články související informace.
> - [Monitorování využití a odhadované náklady](../../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) popisuje, jak zobrazit využití a odhadované náklady napříč více funkcí pro různé cenové modely pro monitorování Azure. Také popisuje, jak změnit cenový model.

Pokud máte dotazy týkající se ukázky cen pro službu Application Insights, můžete odeslat dotaz v našich [fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights).

## <a name="pricing-model"></a>Cenový model

Ceny za [Azure Application Insights] [ start] se podle objemu dat přijatých. Každý prostředek Application Insights se účtuje jako samostatná služba a přispívá k vyúčtování předplatného Azure.

### <a name="data-volume-details"></a>Podrobné informace o objemu dat

* Objem dat je počet bajtů přijatých službou Application Insights telemetrická data. Objem dat se měří jako velikost nekomprimovaných balíček dat JSON, které se získaly službou Application Insights z vaší aplikace. Pro [tabulková data importovat do analýzy](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), objem dat se měří jako velikost nekomprimovaných souborů, které se odesílají do Application Insights.
* Poplatky za objem dat vaší aplikace jsou teď hlášeny na nové účtování počítat s názvem **Ingestování** od dubna 2018. Toto je nový měřič sdílet mezi technologií, jako jsou aplikace Insights a Log Analytics pro monitorování a v části název služby je aktuálně **Log Analytics**. 
* [Live Metrics Stream](../../azure-monitor/app/live-stream.md) dat se nepočítá ceny účely.

Aktuální ceny měny a oblasti, naleznete v tématu [ceny za Application Insights][pricing].

### <a name="multi-step-web-tests"></a>Vícekrokové webové testy

[Vícekrokové webové testy](../../azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests) účtovat další poplatky. Vícekrokové webové testy jsou webové testy, které provádějí posloupnost akcí.

Neexistuje žádný samostatný poplatek za *odešlete zprávu ping testy* o jednu stránku. Telemetrie z testy a testy vícekrokového je účtované stejně jako další telemetrie z vaší aplikace.

## <a name="review-usage-and-estimate-costs"></a>Kontrola využití a odhadované náklady

Application Insights umožňuje snadno pochopit, jaké jsou náklady pravděpodobně možné na základě nedávné způsobů využití. Začínáme na portálu Azure pro prostředek služby Application Insights, přejděte **využití a odhadované náklady** stránky:

![Výběr cenové](./media/pricing/pricing-001.png)

A. Zkontrolujte objem dat za měsíc. To zahrnuje všechna data, které obdržel a uchovávají (po všech [vzorkování](../../azure-monitor/app/sampling.md)) ze serveru a klientské aplikace a z testů dostupnosti.  
B. Samostatný poplatek za provedení [vícekrokové webové testy](../../azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests). (Nejsou obsaženy jednoduché dostupnost testů, které jsou součástí poplatku za objem dat.)  
C. Zobrazit trendy objemu dat za poslední měsíc.  
D. Povolení přijímání dat [vzorkování](../../azure-monitor/app/sampling.md).   
E. Nastavte denní limit objemu dat.  

K hlubšímu prošetření využití Application Insights, otevřete **metriky** stránce, přidejte metriky s názvem "datový bod svazek" a potom vyberte *použít rozdělení* můžete rozdělit data podle "Telemetrie Typ položky". 

Poplatky za Application Insights se přidají do vašeho vyúčtování služeb Azure. Můžete zobrazit podrobnosti o Azure fakturovat v **fakturace** části webu Azure portal, nebo v [fakturačním portálu Azure](https://account.windowsazure.com/Subscriptions). 

![V nabídce vlevo vyberte fakturace](./media/pricing/02-billing.png)

## <a name="data-rate"></a>Přenosová rychlost
Objem dat odesílaných je omezená třemi způsoby:

* **Vzorkování**: Vzorkování můžete snížit objem telemetrických dat, která je odeslána ze serveru a klientské aplikace s minimální narušení metrik. Vzorkování je primárním nástrojem, který můžete použít k vyladění objemu dat, které odesíláte. Další informace o [vzorkování funkce](../../azure-monitor/app/sampling.md). 
* **Denní limit**: Když vytvoříte prostředek Application Insights na webu Azure Portal, denní limit je nastavený na 100 GB za den. Když vytvoříte prostředek Application Insights v sadě Visual Studio, použije se malé (pouze 32,3 MB/den). Denní limit výchozí nastavení je usnadnit testování. Předpokládá se, že uživatel vyvolá denního limitu před nasazením aplikace do produkčního prostředí. 

    Maximální limit je 1 000 GB za den, pokud požadujete vyšší maximum pro aplikace s vysokým provozem. 

    Péče o použijte, pokud nastavíte denní limit. Máte v úmyslu by mělo být *nikdy dosaženo denního limitu*. Když dosáhnou denního limitu můžete přijít o data po zbytek dne a není možné sledovat vaše aplikace. Chcete-li změnit denního limitu, použijte **denní limit objemu** možnost. Této možnosti v můžete přistupovat **využití a odhadované náklady** podokně (to je podrobněji popsaný dále v tomto článku).
    Odebrali jsme omezení pro některé typy předplatných, které mají kredit, který nelze použít pro službu Application Insights. Dříve Pokud předplatné limit útraty, denní limit dialogové okno obsahuje pokyny k odebrání limitu útraty a povolit denní limit zapříčinil nad 32,3 MB za den.
* **Omezení šířky pásma**: Omezení rychlost přenosu dat do 32 000 událostí za sekundu, byla více než 1 minuty na jeden Instrumentační klíč.

*Co když Moje aplikace překračuje omezení frekvence?*

* Objem dat, která vaše aplikace odesílá, u kterého probíhá hodnocení každou minutu. Pokud se překročí sazba za sekundu zprůměrované za minutu, server odmítá některé požadavky. Sada SDK ukládá data do vyrovnávací paměti a poté se pokusí znovu poslat. Nárůst se rozkládá za několik minut. Pokud vaše aplikace konzistentně odesílá data na více než míru omezení, některá data se zahodí. (ASP.NET, Java a sady SDK pro jazyk JavaScript pokusí odeslat data tímto způsobem, dalších sad SDK může jednoduše rozevírací omezené data) Pokud dojde k omezení šířky pásma, upozornění oznámení vás upozorní, že k nim došlo.

*Jak zjistím, kolik dat Moje aplikace odesílá?*

Jeden z následujících možností můžete zobrazit, kolik dat vaše aplikace odesílá:

* Přejděte **využití a odhadované náklady** podokně se zobrazí denní objem graf data. 
* V Průzkumníku metrik přidejte nový graf. Metriky grafu vyberte **datového bodu svazku**. Zapnout **seskupení**a pak skupinu podle **datový typ**.

## <a name="reduce-your-data-rate"></a>Snížit rychlost vašich dat
Zde jsou některé kroky, které vám pomůžou snížit objem dat:

* Použití [vzorkování](../../azure-monitor/app/sampling.md). Tato technologie snižuje rychlost vašich dat bez zkosení metriky. Neztraťte možnost navigace mezi související položky v hledání. V serverové aplikace automaticky funguje vzorkování.
* [Omezit počet volání Ajax, které mohou být hlášeny](../../azure-monitor/app/javascript.md#detailed-configuration) v každé zobrazení stránky, nebo přepněte vypnout generování sestav Ajax.
* [Upravit soubor ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) vypnout kolekci modulů, které nepotřebujete. Například můžete rozhodnout, že jsou nedůležitých čítače výkonu nebo data závislostí.
* Rozdělte telemetrických dat mezi samostatnou instrumentačních klíčů. 
* Předem agregovaná metrika. Pokud volání TrackMetric vložíte do vaší aplikace, můžete zkrátit provoz pomocí přetížení, které přijímá výpočet průměru a směrodatné odchylky poměru dávku měření. Nebo můžete použít [předem agregovat balíčku](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="manage-the-maximum-daily-data-volume"></a>Správa maximální denní objem dat

Denní limit objemu můžete použít k omezení shromážděná data. Však při splnění zakončení dochází ke ztrátě všech telemetrická data odesílaná z vaší aplikace pro zbytek dne. Je *není vhodné* mít vaše aplikace dosažení denního limitu. Po dosažení denního limitu nelze sledovat stav a výkon vaší aplikace.

Namísto použití denní limit objemu, použijte [vzorkování](../../azure-monitor/app/sampling.md) k vyladění objemu dat na úrovni chcete. Potom použijte denního limitu pouze jako "poslední možnost" v případě, že vaše aplikace začíná neočekávaně k odesílání mnohem vyšší objemy telemetrických dat

Postup změny denní limit **konfigurovat** oddílu prostředku Application Insights v **využití a odhadované náklady** vyberte **denního limitu**.

![Upravit denní limit objemu telemetrických dat](./media/pricing/pricing-003.png)

## <a name="sampling"></a>Vzorkování
[Vzorkování](../../azure-monitor/app/sampling.md) je metoda snížení míry, ve kterém se odesílají telemetrická data do vaší aplikace při zachování schopnost najít související události během diagnostické vyhledávání. Je také zachovat počty správné událostí.

Vzorkování je účinný způsob, jak snížit náklady a nepřekročíte s měsíční kvóty. Algoritmus vzorkování tak zachová související položky telemetrických dat, například při použití vyhledávání můžete vyhledat žádosti související s konkrétní výjimce. Algoritmus také zachovává správný počet takže se zobrazí v Průzkumníku metrik správné hodnoty pro požadavků, výjimek sazby a dalšími počty.

Existuje několik tvarů vzorkování.

* [Adaptivní vzorkování](../../azure-monitor/app/sampling.md) je výchozí nastavení sady SDK technologie ASP.NET. Adaptivního vzorkování automaticky přizpůsobí objemu telemetrických dat, která vaše aplikace odesílá. Funguje automaticky v sadě SDK ve webové aplikaci tak, aby snížení provozu telemetrie v síti. 
* *Vzorkování příjmu* představuje alternativu, která pracuje v místě, kde telemetrie z vaší aplikace do služby Application Insights. Vzorkování příjmu nemá vliv na množství telemetrie odesílané z vaší aplikace, ale zmenšuje svazek, který je uchován služby. Vzorkování příjmu můžete použít ke snížení kvótu, která je používané telemetrie z prohlížeče a dalších sad SDK.

Nastavení vzorkování příjmu, přejděte **ceny** podokna:

![Kvóty a cenové podokně vyberte dlaždici ukázky a pak vyberte zlomek vzorkování](./media/pricing/pricing-004.png)

> [!WARNING]
> **Vzorkování dat** podokně řídí pouze hodnotu vzorkování příjmu. Neodráží vzorkovací frekvenci, který je používán jako sadu SDK Application Insights ve vaší aplikaci. Pokud již byly vzorkovány příchozích telemetrických dat v sadě SDK, se nepoužije vzorkování příjmu.
>

Chcete-li zjistit skutečné vzorkovací frekvenci, bez ohledu na to, kde je byla uplatněna, použijte [dotazu Analytics](analytics.md). Dotaz vypadá takto:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

V každém uchovávají záznam, `itemCount` označuje počet původní záznamy, které představuje. Je rovno 1 + Počet zrušených předchozí záznamů. 

## <a name="automation"></a>Automation

Můžete napsat skript nastavení cenový plán s použitím Azure Resource Manageru. [Zjistěte jak](powershell.md#price).

## <a name="limits-summary"></a>Souhrn omezení

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Zakázat denní limit e-mailů

V části zakázání e-mailů denní objem zakončení **konfigurovat** oddílu prostředku Application Insights v **využití a odhadované náklady** vyberte **denního limitu** . Obsahují nastavení pro odeslání e-mailu, když je dosaženo zakončení, i když bylo dosaženo měnitelné úroveň upozornění. Pokud budete chtít zakázat všechny denní limit objemu související s e-mailů, zrušte zaškrtnutí políčka obou polí.

## <a name="legacy-enterprise-pricing-plan"></a>Starší cenový tarif Enterprise

Pro první uživatele služby Azure Application Insights stále existují dva možné dvě cenové plány: Basic a Enterprise. Základní cenový plán je stejný, jak je popsáno výše a je výchozí plán. Zahrnuje všechny funkce plánu Enterprise, bez dalších poplatků. Základní plán účtuje především podle objemu dat, který se ingestuje. 

Plán Enterprise je poplatek za uzel a každý uzel přijme denní příděl dat. V podnikové síti cenový plán, bude vám účtována dat přijatých nad zahrnutý příspěvek. Pokud používáte Operations Management Suite, měli byste zvolit plán Enterprise. 

Aktuální ceny měny a oblasti, naleznete v tématu [ceny za Application Insights](https://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> V dubnu 2018 jsme [zavedené](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) nový cenový model pro monitorování Azure. Tento model přijímá mezi kompletní portfolio služeb monitorování jednoduchý model "s průběžnými platbami". Další informace o [nový cenový model](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), jak k [posoudit dopad přechodu na tento model](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) založené na vaše vzorce používání a [tom, jak začít používat nový model](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model)

### <a name="enterprise-plan-and-operations-management-suite-subscription-entitlements"></a>Podnikový plán a nároky na Operations Management Suite předplatné

Zákazníci, kteří si koupí Operations Management Suite E1 a E2 mohou získat Application Insights Enterprise jako další komponentu bez dalších poplatků jako [jsme oznámili už dřív](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/). Konkrétně každá jednotka Operations Management Suite E1 a E2 zahrnuje nárok na jeden uzel Application Insights Enterprise plánu. Každý uzel Application Insights zahrnuje až 200 MB dat přijatých za den (oddělené od Log Analytics ingestování), s uchováním dat 90 dnů bez dalších poplatků. Tento plán je popsaná v podrobnější později v tomto článku. 

Vzhledem k tomu, že tento plán je určené jenom pro zákazníky s předplatným Operations Management Suite, zákazníky, kteří nemají předplatného služby Operations Management Suite nezobrazí možnost vybrat tento plán.

> [!NOTE]
> Aby bylo zajištěno, že dostanete tento nárok, musí být prostředky Application Insights v podnikové síti cenový plán. Tato oprávnění se vztahuje jenom jako uzly. Prostředky Application Insights v plánu Basic ani neuvědomujete žádnou výhodu. Tato oprávnění nejsou viditelná v ukazuje odhadované náklady **využití a odhadované náklady** podokně. Navíc pokud přesunete předplatné pro nový Azure sledování cenový model platný od dubna 2018, základní plán je k dispozici pouze plán. Přesun předplatného na nový cenový model sledování Azure není žádoucí, pokud máte předplatné Operations Management Suite.

### <a name="how-the-enterprise-plan-works"></a>Jak funguje plánu Enterprise

* Platíte za každý uzel, který odesílá telemetrická data pro všechny aplikace v plánu Enterprise.
 * A *uzlu* je počítač fyzický nebo virtuální server nebo instance platformy jako služby role, který je hostitelem vaší aplikace.
 * Vývoj pro počítače, klientský prohlížeč a mobilní zařízení se nepočítají jako uzly.
 * Pokud má vaše aplikace několik komponent, které odesílají telemetrická data, jako jsou webové služby a back-end pracovní proces, komponenty se počítá samostatně.
 * [Live Metrics Stream](../../azure-monitor/app/live-stream.md) dat se nepočítá ceny účely. V předplatném poplatky se podle počtu uzlů, ne podle aplikací. Pokud máte pět uzlů, které odesílají telemetrii pro 12 aplikací, platí se za pět uzlů.
* I když poplatky se zaokrouhlují za měsíc, platíte jenom pro všechny hodiny, kdy uzel odesílá telemetrická data z aplikace. Hodinová sazba se v uvozovkách měsíční poplatek vydělí 744 (počet hodin za měsíc s 31 dny).
* Pro každý uzel, který je zjištěn (s hodinovou členitost) je zadána přidělení dat svazku alespoň 200 MB za den. Přidělení nepoužívaná data se přenesou z jeden den na další.
 * Pokud se rozhodnete podniku cenový plán, každé předplatné získá denní příděl dat na základě počtu uzlů, které posílají telemetrická data do prostředky Application Insights v tomto předplatném. Ano Pokud máte pět uzlů, které posílají data celý den, budete mít ve fondu příděl 1 GB použito pro všechny prostředky Application Insights v tomto předplatném. Nebude vadit, když některé uzly posílají více dat než jiné uzly, protože poskytovaná data se sdílejí napříč všemi uzly. Pokud v daném dni prostředky Application Insights zobrazí více dat, než je součástí denní přidělování dat pro toto předplatné, vztahují poplatky za Nadlimitní data úrovně za GB. 
 * Denní příděl dat se počítá jako počtu hodin za den (pomocí času UTC), že každý uzel odesílá telemetrii, dělený 24, vynásobený 200 MB. Ano, pokud máte čtyři uzly, které odesílají telemetrii během 15 z 24 hodin za den, zahrnutých dat pro tento den by ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB. Za cenu 2.30 USD za GB za překročení limitu dat náklady by rovný 1,15 USD uzly odeslání 1 GB dat daný den.
 * Denní příděl plánu Enterprise se nesdílí s aplikacemi, pro které jste zvolili základní plán. Nepoužité příspěvek není přenesou v detailech. 

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Příklady toho, jak určit počet různých uzlů

| Scénář                               | Celková denní počet uzlů |
|:---------------------------------------|:----------------:|
| aplikace: 1 pomocí 3 instancí služby Azure App Service a 1 virtuální server | 4 |
| běžící na virtuálních počítačích 2; 3 aplikace prostředky Application Insights pro tyto aplikace jsou ve stejném předplatném a v plánu Enterprise | 2 | 
| 4 aplikace, jehož prostředky aplikace Insights je ve stejném předplatném; každou aplikaci spuštěnou 2 instance 16 špičku a 4 instance během 8 hodin ve špičce | 13.33 | 
| Cloudové služby s 1 Role pracovního procesu a 1 webová Role, každé spuštění 2 instancí | 4 | 
| Clusteru Azure Service Fabric 5 uzly se systémem 50 mikroslužeb; Každá mikroslužba spuštěné instance 3 | 5|

* Uzly přesné, počítací závisí, na které Application Insights SDK aplikace používá. 
  * V SDK verze 2.2 a novější, Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) a [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) sestavy každého hostitele aplikace jako uzel. Příklady jsou název počítače pro fyzický server a hostitelů virtuálních počítačů nebo název instance pro cloud services.  Jedinou výjimkou je aplikace, která se použije pouze [.NET Core](https://dotnet.github.io/) a sadu SDK Application Insights Core. V takovém případě je pro všechny hostitele hlášených pouze jeden uzel, protože název hostitele není k dispozici. 
  * Pro starší verze sady SDK [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) se chová jako novější verze sady SDK, ale [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) hlásí pouze jeden uzel bez ohledu na počet hostitelů aplikací. 
  * Pokud vaše aplikace používá k nastavení sady SDK **instance role** vlastní hodnoty, ve výchozím nastavení, stejnou hodnotu se používá k určení počtu uzlů. 
  * Pokud používáte novou verzi sady SDK aplikace, která se spouští z klientských počítačů nebo mobilních zařízení, počet uzlů může vrátit číslo, které je moc velká (z důvodu velký počet klientských počítačů nebo mobilních zařízení). 

## <a name="next-steps"></a>Další postup

* [Vzorkování](../../azure-monitor/app/sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ../../application-insights/app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/