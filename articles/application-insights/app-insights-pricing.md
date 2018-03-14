---
title: "Správa svazku ceny a dat pro služby Azure Application Insights | Microsoft Docs"
description: "Správa telemetrie svazků a sledování nákladů ve službě Application Insights."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: mbullwin
ms.openlocfilehash: 95c5195ac2ea832586211cce37eb2094e06eaf03
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2018
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Správa svazku ceny a data ve službě Application Insights


Ceny pro [Azure Application Insights] [ start] je založena na datový svazek na aplikaci. Nízké využití během vývoje nebo pro malé aplikaci je pravděpodobně volné, protože není na 1 GB měsíční příspěvek telemetrická data.

Každý prostředek Application Insights je účtován jako samostatná služba a přispívá k fakturovaná částka u předplatného Azure.

Existují dva cenovou plány. Výchozí plán se nazývá Basic. Pro plán Enterprise, který má denní poplatků, ale umožňuje některé další funkce, jako se můžete rozhodnout [průběžné export](app-insights-export-telemetry.md).

Pokud máte dotazy týkající se fungování ceny pro službu Application Insights, nebojte se odeslat dotaz v našem [fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights). 

## <a name="the-price-plans"></a>Plány cena

Najdete v článku [Application Insights stránce s cenami] [ pricing] pro aktuální ceny ve vaší měny.

### <a name="basic-plan"></a>Základní plán

Základní plán je výchozí, když je vytvořen nový prostředek Application Insights a postačí pro většinu zákazníků.

* V základní plán, budou se vám účtovat podle objemu dat: počet bajtů přijatých Application Insights telemetrie. Datový svazek se měří jako velikost nekomprimovaných dat balíčku JSON přijatých Application Insights z vaší aplikace.
Pro [tabulková data importovat Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), datový svazek se měří jako velikost nekomprimovaných souborů odeslaných do služby Application Insights.  
* Vaše první 1 GB pro každou aplikaci je zdarma, takže pokud jste právě experimentování nebo vývoj, budete pravděpodobně muset platit.
* [Živý datový proud metriky](app-insights-live-stream.md) dat není počítá o cenách pro účely.
* [Průběžné Export](app-insights-export-telemetry.md) je k dispozici pro poplatek za navíc za GB v základní plán.

### <a name="enterprise-plan"></a>Plán Enterprise

* V plánu podnikového aplikace můžete použít všechny funkce Application Insights. [Průběžné Export](app-insights-export-telemetry.md) a 

[Protokolu konektor Analytics](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409) v plánu podnikového jsou k dispozici bez jakýchkoli dalších poplatků.
* Platíte za uzlu, který odesílá telemetrická data pro všechny aplikace v plánu podnikového. 
 * A *uzlu* je počítač fyzický nebo virtuální server nebo instanci role platforma jako služba, který je hostitelem vaší aplikace.
 * Vývoj pro počítače, klientský prohlížeč a mobilní zařízení nejsou počítají jako uzly.
 * Pokud vaše aplikace obsahuje několik komponent, které odesílají telemetrická data, jako jsou webové služby a pracovní back-end, se počítají samostatně.
 * [Živý datový proud metriky](app-insights-live-stream.md) dat není součet ceny purposes.* napříč předplatné, jsou vaše poplatky na uzel a ne aplikace. Pokud máte pět uzlů odesílání telemetrie 12 aplikace a pak zřizování je pro pět uzlů.
* I když poplatky jsou v uvozovkách za měsíc, se vám účtovat pouze pro všechny hodinu, ve kterém uzlu odesílá telemetrická data z aplikace. Hodinové poplatků je uvozovkách měsíčních poplatků / 744 (počet hodin v měsíci 31 dnů).
* Pro každý uzel zjistil (se hodinách) je zadané přidělení objemu dat 200 MB za den. Přidělení nevyužité dat není přenesené z jednoho dne na další.
 * Pokud si zvolíte možnost cenovou Enterprise, každé předplatné získá denní příspěvek dat na základě počtu uzlů odesílání telemetrie do zdroje Application Insights v tomto předplatném. Takže pokud máte 5 uzly odeslání dat celý den, budete mít ve fondu příspěvek ve výši 1 GB použito pro všechny prostředky Application Insights v tomto předplatném. Pokud některé uzly jsou odesílá více dat než jiné uzly, protože zahrnuté data sdílena pro všechny uzly nezáleží. Pokud určitý den zobrazí prostředky Application Insights více dat, než je součástí denní přidělení data pro toto předplatné, použít Nadlimitní dat poplatků za GB. 
 * Denní data příspěvek se počítá jako počtem hodin za den (pomocí UTC), každý uzel odesílá telemetrii dělený 24 časy 200 MB. Takže pokud máte 4 uzly odesílat telemetrii během 15 za 24 hodin dne, budou zahrnuty údaje pro daný den ((4 x 15) / 24) x 200 MB = 500 MB. Za cenu 2.30 USD za GB za nadměrné využívání dat bude zdarma pro rovný 1,15 USD Pokud uzly odeslat 1 GB dat daný den.
 * Všimněte si, že denní příspěvek plánu podnikového není sdílený s aplikací, pro které jste vybrali možnost Základní a nepoužívané povoleného užívání nepřenáší z každodenní. 
* Tady jsou některé příklady určení počet jedinečných uzlů:
| Scénář                               | Celkový počet uzlů na každý den |
|:---------------------------------------|:----------------:|
| aplikace: 1 používá 3 instancí služby Azure App Service a 1 virtuálního serveru | 4 |
| 3 aplikací běžících na 2 virtuální počítače a prostředky Application Insights pro tyto aplikace jsou ve stejném předplatném a v plánu podnikového | 2 | 
| 4 aplikace, jejíž statistiky aplikace prostředky jsou ve stejném předplatném. Každá aplikace spouští 2 instance 16 špičku a 4 instancí dobu ve špičce 8. | 13.33 | 
| Cloudové služby s 1 Role pracovního procesu a 1 webové Role, všechny spuštěné instance 2 | 4 | 
| Cluster Service Fabric 5 uzlu službou 50 micro-services, každý micro-spuštěné instance 3 | 5|

* Přesné uzlu počítání chování závisí, na kterém je pomocí Application Insights SDK aplikace. 
  * Ve verzích sady SDK, 2.2 a a vyšší, Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) nebo [sady SDK webové](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) sestava bude každý hostitel aplikace jako uzel, například název počítače pro fyzického serveru a hostitelé virtuálních počítačů nebo Název instance v případě cloudové služby.  Jedinou výjimkou je aplikace jen pomocí [.NET Core](https://dotnet.github.io/) a Application Insights Core SDK, ve kterém případu jenom jeden uzel se ohlásí pro všechny hostitele, protože název hostitele není k dispozici. 
  * Pro starší verze sady SDK [sady SDK webové](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) budou chovat stejně jako novější verze sady SDK, ale [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) oznámí jenom jeden uzel bez ohledu na počet hostitelů skutečné aplikací. 
  * Všimněte si, že pokud vaše aplikace používá sady SDK k nastavení roleInstance na vlastní hodnotu, ve výchozím nastavení stejné hodnoty se použije k určení počtu uzlů. 
  * Pokud používáte novou verzi sady SDK k aplikaci, která se spouští z klientských počítačů nebo mobilních zařízení, je možné, že počet uzlů může vrátit číslo, které je moc velké (z velký počet klientských počítačů nebo mobilních zařízení). 

### <a name="multi-step-web-tests"></a>Vícekrokové webové testy

Je dalších poplatků pro [vícekrokové webové testy](app-insights-monitor-web-app-availability.md#multi-step-web-tests). Vztahuje se k webové testy, které provádějí posloupnost akcí. 

Je bezplatná samostatné pro příkaz ping testy jedné stránky. Telemetrie z testů pomocí příkazu ping a testy vícekrokový je účtován spolu s další telemetrie z vaší aplikace.
 
## <a name="operations-management-suite-subscription-entitlement"></a>Nárocích předplatné Operations Management Suite

Jako [nedávno vydal](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/), zákazníci, kteří zakoupili E1 Microsoft Operations Management Suite a E2 je možné získat Application Insights Enterprise jako další komponentu bez dalších poplatků. Jednotky služby Operations Management Suite E1 a E2 konkrétně obsahuje nároku na 1 uzel Enterprise plánu Application Insights. Jak jsme uvedli výše, obsahuje každý uzel Application Insights na 200 MB dat požity za den (oddělené od analýzy protokolů přijímání dat), s dobou uchování dat 90 dnů bez dalších poplatků. 

> [!NOTE]
> Aby se zajistilo, že dostanete tento nárok, musí mít vašich prostředků Application Insights v podnikové síti ceny plánu. Toto oprávnění platí pouze jako uzly, takže prostředky Application Insights v základní plán nebude potřeba si uvědomit, nějaké výhody. Všimněte si, že tento nárok nebudou viditelné na odhadované náklady zobrazený na funkce + cenovou okno. 
>
 
## <a name="review-pricing-plans-and-estimate-costs"></a>Zkontrolujte cenovou plány a odhadnout náklady

Application Insights snadno pochopit cenovou plány, které jsou k dispozici a je pravděpodobné, jaké nákladů být založen na poslední vzorce používání. Začněte otevřením **funkce + cenová** okno v prostředku Application Insights na portálu Azure:

![Zvolte ceny.](./media/app-insights-pricing/01-pricing.png)

**a.** Zkontrolujte datový svazek pro daný měsíc. To zahrnuje všechna data přijme a uchovávají (po všech [vzorkování](app-insights-sampling.md) ze serveru a klientské aplikace a z testy dostupnosti.

**b.** Účtována samostatně se provádí [vícekrokové webové testy](app-insights-monitor-web-app-availability.md#multi-step-web-tests). (To neobsahuje testy jednoduché dostupnosti, které jsou součástí poplatků svazku data.)

**c.** Povolte plán Enterprise.

**d.** Proklikejte se k datům možnosti správy zobrazení Datový svazek pro poslední měsíc, nastavit denní zakončení nebo nastavení přijímání vzorkování.

Application Insights poplatky budou přidány do faktury Azure. Zobrazí podrobnosti o vašem Azure účtovat pošle v oddílu fakturace na portálu Azure nebo v [Azure Billing Portal](https://account.windowsazure.com/Subscriptions). 

![V nabídce straně zvolte fakturace.](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Rychlost přenosu dat
Existují tři způsoby, ve kterých je omezená svazku odesílat data:

* **Vzorkování:** lze použít tento mechanismus snížíte telemetrické zprávy odesílané z vašeho serveru a klienta aplikace s minimální narušení metrik. Toto je primárním nástrojem budete muset vyladit množství dat. Další informace o [vzorkování funkce](app-insights-sampling.md). 
* **Denního limitu:** při vytváření prostředek Application Insights z portálu Azure, to je nastavena na 100 GB a den. Výchozí hodnota při vytváření prostředek Application Insights v sadě Visual Studio je malý (pouze 32,3 MB/den), který je určen pouze k testování faciliate. V takovém případě je určena, bude uživatel vyvolat denního limitu před nasazením aplikace do produkčního prostředí. Maximální hodnota je 1 000 GB a den, pokud požadujete vyšší maximum pro intenzivní provoz aplikace. Použít pozor při nastavování denního limitu podle vašich představ by měla být **nikdy k volání denního limitu**, protože se pak dojít ke ztrátě dat pro zbytek dne a nebude možné do monitorování vaší aplikace. Chcete ho změnit, použijte okno cap denní svazku odkazované z okna Správa objem dat (viz níže). Všimněte si, že některé typy předplatného mají kreditu, který nelze použít pro službu Application Insights. Pokud předplatné má limitu útraty a automaticky, bude se okně denního limitu mít pokyny jak ho odebrat a povolit denního limitu má být aktivována nad rámec 32,3 MB a den.  
* **Omezení šířky pásma:** toto omezení rychlost přenosu dat do 32 kB událostí za sekundu, průměr více než 1 minuta. 


*Co se stane, když aplikace my překračuje míru omezení?*

* Objem dat, která vaše aplikace odesílá se hodnotí každou minutu. Pokud překročí rychlost za sekundu průměr za minutu, server odmítá některých požadavků. Sada SDK ukládá data do vyrovnávací paměti a potom se pokusí o odeslání, rozšíří nárůst za několik minut. Pokud vaše aplikace konzistentně odesílá data na vyšší míru omezení, některá data se zahodí. (ASP.NET, Java a JavaScript SDK pokusí odeslat znovu tímto způsobem, dalších sadách SDK může jednoduše rozevírací omezeny data.) Pokud dojde k omezení šířky pásma, uvidíte oznámení upozornění, že byla odepřena.

*Jak poznám, kolik dat Moje aplikace odesílá?*

* Otevřete **Správa svazku dat** okno se zobrazí denní svazku graf data. 
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

Chcete-li změnit denního limitu, v části Konfigurace prostředku Application Insights, klikněte na tlačítko **Správa objem dat** pak **denního limitu**.

![Úprava denní svazku cap telemetrie](./media/app-insights-pricing/daily-cap.png) 

## <a name="sampling"></a>Vzorkování
[Vzorkování](app-insights-sampling.md) je metoda snižuje rychlost, kdy je odesílání telemetrie do vaší aplikace a zároveň zachovat vyhledání související události během diagnostických hledání a počty stále zachování správné událostí. 

Vzorkování je efektivní způsob, jak zůstat v rámci měsíční kvóta a snížit náklady. Algoritmus vzorkování zachová související položky telemetrie, takže například při použití hledání můžete najít žádosti související s konkrétní výjimka. Algoritmus také zachová správné počty, abyste viděli správné hodnoty v Průzkumníku Metrika pro požadavků, výjimka frekvenci a dalších položek.

Existuje několik formy vzorkování.

* [Adaptivního vzorkování](app-insights-sampling.md) je výchozí hodnota pro sady SDK technologie ASP.NET, který automaticky upraví, aby svazek telemetrii, kterou vaše aplikace odesílá. Funguje automaticky v sadě SDK ve vaší webové aplikaci, tak, aby se snižuje telemetrie provoz v síti. 
* *Přijímání vzorkování* představuje alternativu, která funguje v místě, kde telemetrie z vaší aplikace do služby Application Insights. Neovlivní objem telemetrická data odesílaná z vaší aplikace, ale snižuje svazku uchována pomocí služby. Můžete ji ke snížení kvótu používané telemetrie z prohlížečů a dalších sadách SDK.

Přijímání vzorkování, nastavte ovládací prvek v okně Cenová:

![Z kvóty a cenovou okno klikněte na dlaždici ukázky a vyberte zlomek vzorkování.](./media/app-insights-pricing/04.png)

> [!WARNING]
> V okně vzorkování dat je řízeno jen hodnotu přijímání vzorkování. Ho neodráží vzorkovací frekvenci, které se uplatňuje Application Insights SDK ve vaší aplikaci. Pokud příchozí telemetrii má již byla odebírána data v sadě SDK, není použita přijímání vzorkování.
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

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/

