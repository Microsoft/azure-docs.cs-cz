---
title: 'Přehled: co je Azure Time Series Insights Gen2? -Azure Time Series Insights Gen2 | Microsoft Docs'
description: Přečtěte si o změnách, vylepšeních a funkcích Azure Time Series Insights Gen2.
ms.service: time-series-insights
services: time-series-insights
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.topic: overview
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: fa0416db440e6433829b8077b6988eeaa6a596ad
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91667057"
---
# <a name="what-is-azure-time-series-insights-gen2"></a>Co je Azure Time Series Insights Gen2

Azure Time Series Insights Gen2 je otevřená a škálovatelná komplexní analytická služba IoT, která nabízí špičkové uživatelské prostředí a rozsáhlá rozhraní API pro integraci svých výkonných funkcí do stávajícího pracovního postupu nebo aplikace.

Můžete ji použít ke shromažďování, zpracování, dotazování a vizualizaci dat v Internet věcí (IoT) Scale--data, která jsou vysoce kontextovaná a optimalizovaná pro časové řady.

Azure Time Series Insights Gen2 je navržená pro zkoumání a provozní analýzu dat ad hoc, což vám umožní odhalit skryté trendy, hledání anomálie a provádět analýzy hlavních příčin. Je to otevřená a pružná nabídka, která splňuje široké požadavky na nasazení v průmyslu IoT.

## <a name="video"></a>Video

Přečtěte si další informace o Azure Time Series Insights Gen2.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Using-Azure-Time-Series-Insights-to-create-an-Industrial-IoT-analytics-platform/player]

## <a name="definition-of-iot-data"></a>Definice dat IoT

Data v průmyslových IoT v organizacích náročných na prostředky často postrádají strukturální konzistenci z důvodu proměnlivé povahy zařízení a senzorů v oblasti průmyslového nastavení. Data z těchto datových proudů jsou charakterizována značnými nedostatky a někdy poškozenými zprávami a nepravdivými čteními. Data IoT jsou často smysluplná v souvislosti s dalšími vstupy dat, které pocházejí od první strany nebo z jiných zdrojů, jako je například CRM nebo ERP, které přidávají kontext pro koncové pracovní postupy. Vstupy ze zdrojů dat třetích stran, jako jsou data o počasí, můžou přispět k rozšíření datových proudů telemetrie v dané instalaci.

To vše předpokládá, že se pro provozní a obchodní účely použijí jenom zlomky dat a analýza vyžaduje kontext. Průmyslová data jsou často historická v průběhu delšího časového rozsahu pro pochopení a korelaci trendů. Zapínání shromážděných dat IoT do užitečných přehledů vyžaduje:

* Zpracování dat, které umožňuje vyčistit, filtrovat, interpolovat, transformovat a připravovat data pro analýzu.
* Struktura pro procházení a pochopení dat, tedy pro normalizaci a dátí dat.
* Nákladově efektivní úložiště pro dlouhé nebo nekonečné uchovávání zpracovaných dat a nezpracovaných dat.

Tato data poskytují konzistentní, komplexní, aktuální a správné informace pro obchodní analýzy a vytváření sestav.

Následující obrázek znázorňuje typický tok dat IoT.

[![Tok dat IoT](media/v2-update-overview/overview-one.png)](media/v2-update-overview/overview-one.png#lightbox)

## <a name="azure-time-series-insights-gen2-for-industrial-iot"></a>Azure Time Series Insights Gen2 pro průmyslové IoT

Technologie IoT na šířku je odlišná se zákazníky, kteří se týkají nejrůznějších průmyslových segmentů, včetně výroby, automobilového prostředí, energie, nástrojů, inteligentních budov a konzultací. V rámci této široké škály podnikového trhu IoT jsou nativní řešení cloudu, která poskytují komplexní analýzy zaměřené na rozsáhlá data IoT, stále vyvíjí.

Azure Time Series Insights Gen2 tuto potřebu trhu tím, že poskytuje komplexní řešení IoT Analytics s bohatou sémantikou pro vytváření kontextu dat časových řad, přehledy založené na prostředcích a špičkové uživatelské prostředí pro zjišťování, vývoj, detekci anomálií a provozní poznatky.

Bohatou platformu pro provozní analýzu v kombinaci s našimi interaktivními funkcemi pro zkoumání dat můžete použít Azure Time Series Insights Gen2 k odvození více hodnot dat shromážděných z prostředků IoT. Nabídka Gen2 podporuje:

* Řešení úložiště s více vrstvami s dlouhodobou a studenou analýzou, která zákazníkům poskytuje možnost směrovat data mezi teplou a studenou pro interaktivní analýzy prostřednictvím teplého data a také provozními poznatky za desetiletí historických dat.

  * Vysoce interaktivní řešení teplé analýzy, které provádí časté a velké množství dotazů za kratší data časového rozsahu
  * Škálovatelná, výkonná a nákladově optimalizovaná data řady v závislosti na Azure Storage, která zákazníkům umožňují trend let dat časových řad v řádu sekund.

* Podpora sémantického modelu, která popisuje doménu a Metadata přidružená k odvozeným a nezpracovaným signálům z prostředků a zařízení.

* Flexibilní analytická platforma pro ukládání historických dat časových řad do účtu Azure Storage ve vlastnictví zákazníka, takže zákazníci budou mít vlastnictví svých dat IoT. Data jsou uložená v open source formátu Apache Parquet, který umožňuje připojení a spolupráci v různých scénářích dat, včetně prediktivních analýz, strojového učení a dalších vlastních výpočtů, a to s využitím známých technologií, jako jsou Spark a datacihly.

* Bohaté analýzy s využitím pokročilých rozhraní API pro dotazy a uživatelského prostředí, které kombinuje přehledy dat na základě assetů s bohatou a ad hoc analýzou dat s podporou interpolace, skalárních a agregačních funkcí, proměnných kategorií, bodových grafů a času posunování signálů časových řad pro podrobnou analýzu.

* Platforma podnikové úrovně pro podporu škálování, výkonu, zabezpečení a spolehlivosti potřeb našich zákazníků v podniku IoT.

* Podpora rozšiřitelnosti a integrace pro komplexní analýzy. Azure Time Series Insights Gen2 poskytuje rozšiřitelnou analytickou platformu pro nejrůznější scénáře dat. Konektor Power BI umožňuje zákazníkům přebírat dotazy v Azure Time Series Insights Gen2 přímo do Power BI a získat tak jednotný přehled o analýzách BI a časových řad v jednom podokně skla.

Následující diagram znázorňuje tok dat na nejvyšší úrovni.

  [![Klíčové funkce](media/v2-update-overview/overview-two.png)](media/v2-update-overview/overview-two.png#lightbox)

Azure Time Series Insights Gen2 poskytuje škálovatelný cenový model průběžných plateb pro zpracování dat, úložiště (data a metadata) a dotaz, který zákazníkům umožňuje vyladit jejich používání, aby vyhovovaly vašim obchodním požadavkům.

Díky zavedení těchto klíčových funkcí pro průmyslové účely IoT Azure Time Series Insights Gen2 také nabízí následující klíčové výhody:  

| Schopnost | Výhoda |
| ---| ---|
| Vícevrstvé úložiště pro data časových řad IoT-Scale | Pomocí sdíleného kanálu zpracování dat pro ingestování dat můžete ingestovat data do teplého i mrazírenského úložiště. Používejte záložní úložiště pro interaktivní dotazy a chladírenské úložiště pro ukládání velkých objemů dat. Další informace o tom, jak využít výhod vysoce výkonných dotazů založených na prostředcích, najdete v tématu [dotazy](./concepts-query-overview.md). |
| Model časové řady pro dátí nezpracované telemetrie a odvození přehledů na základě assetů | Model časové řady můžete použít k vytvoření instancí, hierarchií, typů a proměnných pro data časových řad. Další informace o modelu časových řad najdete v tématu [model časových řad](./concepts-model-overview.md).  |
| Plynulá a kontinuální integrace s jinými datovými řešeními | Data v Azure Time Series Insights Gen2 chladírenský sklad se [ukládají](./concepts-storage.md) do Open Source souborů Apache Parquet. To umožňuje integraci dat s jinými datovými řešeními, 1. nebo třetí stranou, pro scénáře, které zahrnují business intelligence, pokročilé Machine Learning a prediktivní analýzy. |
| Zkoumání dat téměř v reálném čase | Prostředí [Azure Time Series Insights Průzkumník Gen2](./time-series-insights-update-explorer.md) poskytuje vizualizaci pro všechna streamování dat prostřednictvím kanálu přijímání. Po připojení zdroje událostí můžete zobrazit, prozkoumat a dotazovat data událostí. Tímto způsobem můžete ověřit, jestli zařízení vysílá data podle očekávání. Můžete také monitorovat prostředek IoT pro stav, produktivitu a celkovou efektivitu. |
| Rozšiřitelnost a integrace | Integrace konektoru Power BI je k dispozici přímo v uživatelském prostředí Průzkumníka časových řad prostřednictvím možnosti **exportovat** , což zákazníkům umožňuje exportovat dotazy časových řad, které vytvoří v prostředí uživatele přímo do Power BI plochy, a zobrazit grafy časových řad společně s dalšími analýzami BI. Tím se otevře dvířka nové třídy scénářů pro podniky IoT v průmyslu, které investovaly do Power BI tím, že v různých zdrojích dat, včetně časových řad IoT, poskytneme jedno podokno ze skleněných zdrojů. |
| Vlastní aplikace založené na Azure Time Series Insights platformě Gen2 | Azure Time Series Insights Gen2 podporuje [sadu JavaScript SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). Sada SDK poskytuje bohatý ovládací prvky a zjednodušený přístup k dotazům. Pomocí sady SDK můžete sestavovat vlastní aplikace IoT nad Azure Time Series Insights Gen2 tak, aby vyhovovaly vašim obchodním potřebám. Můžete také použít [rozhraní API](./concepts-query-overview.md) pro Azure Time Series Insights Gen2 pro dotazy přímo na data na vlastní aplikace IoT. |

## <a name="next-steps"></a>Další kroky

Začínáme s Azure Time Series Insights Gen2:

> [!div class="nextstepaction"]
> [Příručka Rychlý start](./time-series-insights-update-quickstart.md)

Další informace o případech použití:

> [!div class="nextstepaction"]
> [Případy použití Azure Time Series Insights Gen2](./time-series-insights-update-use-cases.md)
