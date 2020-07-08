---
title: 'Přehled: co je Azure Time Series Insights Preview? -Azure Time Series Insights | Microsoft Docs'
description: Přečtěte si o změnách, vylepšeních a funkcích v Azure Time Series Insights Preview.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: overview
ms.date: 04/13/2020
ms.custom: seodec18
ms.openlocfilehash: 20ab78aa67fa5d59dac774559b3c43561cf75fbc
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86040703"
---
# <a name="what-is-azure-time-series-insights-preview"></a>Co je Azure Time Series Insights ve verzi Preview?

Azure Time Series Insights Preview je ucelená nabídka typu platforma jako služba (PaaS). Můžete ji použít ke shromažďování, zpracování, analýze a dotazování dat v Internet věcí (IoT) Scale--data, která jsou vysoce kontextovaná a optimalizovaná pro časové řady. 

Time Series Insights je navržená pro zkoumání a provozní analýzu dat ad hoc. Je to rozšiřitelná a přizpůsobená nabídka služeb, která splňuje široké požadavky na nasazení v průmyslu IoT.

## <a name="video"></a>Video

Přečtěte si další informace o Azure Time Series Insights Preview.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="definition-of-iot-data"></a>Definice dat IoT

Data v průmyslových IoT v organizacích náročných na prostředky často postrádají strukturální konzistenci z důvodu proměnlivé povahy zařízení a senzorů v oblasti průmyslového nastavení. Data z těchto datových proudů jsou charakterizována značnými nedostatky a někdy poškozenými zprávami a nepravdivými čteními. Data IoT jsou často smysluplná v souvislosti s dalšími vstupy dat, které pocházejí od první strany nebo z jiných zdrojů, jako je například CRM nebo ERP, které přidávají kontext pro koncové pracovní postupy. Vstupy ze zdrojů dat třetích stran, jako jsou data o počasí, můžou přispět k rozšíření datových proudů telemetrie v dané instalaci. 

To vše předpokládá, že se pro provozní a obchodní účely použijí jenom zlomky dat a analýza vyžaduje kontext. Průmyslová data jsou často historická v průběhu delšího časového rozsahu pro pochopení a korelaci trendů. Zapínání shromážděných dat IoT do užitečných přehledů vyžaduje: 

* Zpracování dat, které umožňuje vyčistit, filtrovat, interpolovat, transformovat a připravovat data pro analýzu.
* Struktura pro procházení a pochopení dat, tedy pro normalizaci a dátí dat.
* Nákladově efektivní úložiště pro dlouhé nebo nekonečné uchovávání zpracovaných dat a nezpracovaných dat.

Tato data poskytují konzistentní, komplexní, aktuální a správné informace pro obchodní analýzy a vytváření sestav.

Následující obrázek znázorňuje typický tok dat IoT.

[![Tok dat IoT](media/v2-update-overview/overview-one.png)](media/v2-update-overview/overview-one.png#lightbox)

## <a name="azure-time-series-insights-for-industrial-iot"></a>Azure Time Series Insights pro průmyslový IoT

Technologie IoT na šířku je odlišná se zákazníky, kteří se týkají nejrůznějších průmyslových segmentů, včetně výroby, automobilového prostředí, energie, nástrojů, inteligentních budov a konzultací. V rámci této široké škály podnikového trhu IoT jsou nativní řešení cloudu, která poskytují komplexní analýzy zaměřené na rozsáhlá data IoT, stále vyvíjí. 

Azure Time Series Insights tento trh řeší tím, že poskytuje komplexní řešení IoT Analytics s bohatou sémantikou pro vytváření kontextu dat časových řad, přehledy založené na prostředcích a špičkové uživatelské prostředí pro zjišťování, trendy, detekci anomálií a provozní poznatky. 

Bohatou platformu pro provozní analýzu v kombinaci s našimi interaktivními funkcemi pro zkoumání dat můžete použít Time Series Insights k odvození více hodnot dat shromážděných z prostředků IoT. Nabídka Preview podporuje: 

* Řešení úložiště s více vrstvami s dlouhodobou a studenou analýzou, která zákazníkům poskytuje možnost směrovat data mezi teplou a studenou pro interaktivní analýzy prostřednictvím teplého data a také provozními poznatky za desetiletí historických dat. 

    *    Vysoce interaktivní řešení teplé analýzy, které provádí časté a velké množství dotazů za kratší data časového rozsahu 
    *    Škálovatelná, výkonná a nákladově optimalizovaná data řady v závislosti na Azure Storage, která zákazníkům umožňují trend let dat časových řad v řádu sekund. 

* Podpora sémantického modelu, která popisuje doménu a Metadata přidružená k odvozeným a nezpracovaným signálům z prostředků a zařízení.

* Flexibilní analytická platforma pro ukládání historických dat časových řad do účtu Azure Storage ve vlastnictví zákazníka, takže zákazníci budou mít vlastnictví svých dat IoT. Data jsou uložená v open source formátu Apache Parquet, který umožňuje konektivitu a spolupráci napříč různými scénáři dat, včetně prediktivních analýz, strojového učení a dalších vlastních výpočtů, a to s využitím známých technologií, jako jsou Spark, datacihly a Jupyter.

* Bohaté analýzy s využitím pokročilých rozhraní API pro dotazy a uživatelského prostředí, které kombinuje přehledy dat na základě assetů s bohatou a ad hoc analýzou dat s podporou interpolace, skalárních a agregačních funkcí, proměnných kategorií, bodových grafů a času posunování signálů časových řad pro podrobnou analýzu.

*    Platforma podnikové úrovně pro podporu škálování, výkonu, zabezpečení a spolehlivosti potřeb našich zákazníků v podniku IoT.

* Podpora rozšiřitelnosti a integrace pro komplexní analýzy. Time Series Insights poskytuje rozšiřitelnou analytickou platformu pro nejrůznější scénáře dat. Konektor Time Series Insights Power BI umožňuje zákazníkům přebírat dotazy v Time Series Insights přímo do Power BI a získat tak jednotný přehled o analýzách BI a časových řad v jednom podokně skla.

Následující diagram znázorňuje tok dat na nejvyšší úrovni.

  [![Klíčové funkce](media/v2-update-overview/overview-two.png)](media/v2-update-overview/overview-two.png#lightbox)

Azure Time Series Insights poskytuje škálovatelný cenový model průběžných plateb pro zpracování dat, úložiště (data a metadata) a dotaz, který zákazníkům umožňuje vyladit jejich používání podle svých obchodních potřeb. 
 
Díky zavedení těchto klíčových funkcí pro průmyslové účely IoT Time Series Insights také nabízí následující klíčové výhody.  

| | |
| ---| ---|
| Vícevrstvé úložiště pro data časových řad IoT-Scale | Pomocí sdíleného kanálu zpracování dat pro ingestování dat můžete ingestovat data do teplého i mrazírenského úložiště. Používejte záložní úložiště pro interaktivní dotazy a chladírenské úložiště pro ukládání velkých objemů dat. Další informace o tom, jak využít výhod vysoce výkonných dotazů založených na prostředcích, najdete v tématu [dotazy](./concepts-query-overview.md). |
| Model časové řady pro dátí nezpracované telemetrie a odvození přehledů na základě assetů | Model časové řady můžete použít k vytvoření instancí, hierarchií, typů a proměnných pro data časových řad. Další informace o modelu časových řad najdete v tématu [model časových řad](./concepts-model-overview.md).  |
| Plynulá a kontinuální integrace s jinými datovými řešeními | Data v Time Series Insights chladírenských skladů se [ukládají](concepts-storage.md) do souborů Apache Parquet v otevřeném zdroji. To umožňuje integraci dat s jinými datovými řešeními, 1. nebo třetí stranou, pro scénáře, které zahrnují business intelligence, pokročilé Machine Learning a prediktivní analýzy. |
| Zkoumání dat téměř v reálném čase | Prostředí [Preview aplikace Azure Time Series Insights Explorer](./time-series-insights-update-explorer.md) poskytuje vizualizaci pro všechna streamování dat prostřednictvím kanálu přijímání. Po připojení zdroje událostí můžete zobrazit, prozkoumat a dotazovat data událostí. Tímto způsobem můžete ověřit, jestli zařízení vysílá data podle očekávání. Můžete také monitorovat prostředek IoT pro stav, produktivitu a celkovou efektivitu. | 
| Rozšiřitelnost a integrace | Integrace konektoru Azure Time Series Insights Power BI je k dispozici přímo v uživatelském prostředí Průzkumníka časových řad prostřednictvím možnosti **exportovat** , což zákazníkům umožňuje exportovat dotazy časových řad, které vytvářejí v uživatelském prostředí, přímo do Power BI plochy a zobrazovat grafy časových řad spolu s dalšími analýzami BI. Tím se otevře dvířka nové třídy scénářů pro podniky IoT v průmyslu, které investovaly do Power BI tím, že v různých zdrojích dat, včetně časových řad IoT, poskytneme jedno podokno ze skleněných zdrojů. | 
| Vlastní aplikace založené na Time Series Insights platformě | Time Series Insights podporuje [sadu JavaScript SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). Sada SDK poskytuje bohatý ovládací prvky a zjednodušený přístup k dotazům. Pomocí sady SDK můžete sestavovat vlastní aplikace IoT nad Time Series Insights tak, aby vyhovovaly vašim obchodním potřebám. [Rozhraní API pro Time Series Insights dotazování](./concepts-query-overview.md) můžete použít také přímo k pohonu dat do vlastních aplikací IoT. |

## <a name="next-steps"></a>Další kroky

Začínáme s Azure Time Series Insights Preview:

> [!div class="nextstepaction"]
> [Příručka Rychlý start](./time-series-insights-update-quickstart.md)

Další informace o případech použití:

> [!div class="nextstepaction"]
> [Případy použití ve verzi Preview pro Azure Time Series Insights](./time-series-insights-update-use-cases.md)