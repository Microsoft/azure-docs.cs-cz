---
title: 'Přehled: Azure Time Series Insights Preview | Microsoft Docs'
description: Přehled Azure Time Series Insights Preview.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: dpalled
ms.workload: big-data
ms.topic: overview
ms.date: 09/23/2019
ms.custom: seodec18
ms.openlocfilehash: e4a57c6d4a9034d9145058e9137759c2cea19aa6
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978662"
---
# <a name="what-is-azure-time-series-insights-preview"></a>Co je Azure Time Series Insights ve verzi Preview?

Azure Time Series Insights Preview je ucelená nabídka typu platforma jako služba (PaaS). Používá se ke shromažďování, zpracování, ukládání, analýze a dotazování vysoce kontextových dat IoT škály, které jsou optimalizované pro časovou řadu. Time Series Insights je ideální pro zkoumání a provozní analýzu dat ad hoc. Time Series Insights je jedinečná rozšiřitelná a přizpůsobená nabídka služeb, která splňuje široké požadavky na nasazení v průmyslu IoT.

> [!TIP]
> Informace o funkcích, které jsou obecně dostupné (GA), najdete v [přehledu Azure Time Series Insights GA](time-series-insights-overview.md).

## <a name="video"></a>Video

### <a name="learn-more-about-azure-time-series-insights-preview-br"></a>Přečtěte si další informace o Azure Time Series Insights Preview. </br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="define-iot-data"></a>Definování dat IoT

Data IoT jsou jakákoli průmyslová data, která jsou k dispozici v organizacích náročných na prostředky. Data IoT jsou často vysoce nestrukturovaná, protože se odesílají z prostředků, které zaznamenávají poměrně vysokou míru vysokého měřítka. Mezi tato měření patří teplota, pohyb a vlhkost. Tyto datové proudy často vyznačují významné mezery, poškozené zprávy a nepravdivé čtení. Data z těchto datových proudů je třeba vyčistit, aby mohlo dojít k jakékoli analýze.

Data IoT jsou často smysluplná jenom v souvislosti s dalšími vstupy dat, které pocházejí ze zdrojů první strany, jako je například CRM nebo ERP. Vstupy také pocházejí ze zdrojů dat třetích stran, jako jsou například počasí nebo umístění.

V důsledku toho se pro provozní a obchodní účely použijí jenom zlomky dat. Tato data poskytují konzistentní, komplexní, aktuální a správné informace pro obchodní sestavy a analýzy. Zapínání shromážděných dat IoT do užitečných přehledů vyžaduje:

* Zpracování dat, které umožňuje vyčistit, filtrovat, interpolovat, transformovat a připravovat data pro analýzu.
* Struktura pro navigaci a pochopení dat, tedy pro normalizaci a dátí dat.
* Nákladově efektivní úložiště pro dlouhé nebo nekonečné uchovávání za desetiletí "množství zpracovaných dat", nebo odvozená, data a nezpracovaná data.

Na následujícím obrázku se zobrazí typický tok dat IoT.

  ![Tok dat IoT][1]

## <a name="azure-time-series-insights-for-industrial-iot"></a>Azure Time Series Insights pro průmyslový IoT

Aktuální prostředí IoT na šířku je odlišné. Zákazníci zahrnují výrobní, automobilové, energetické, pomůcky, inteligentní budovy a konzultační odvětví. Scénáře zahrnují průzkum dat ad hoc, kde je tvar dat neznámý. Mezi scénáře patří také provozní analýza přes schematized nebo explicitní modelovaná data pro zajištění provozní efektivity. Tyto scénáře obvykle existují souběžně a podporují různé případy použití. Mezi schopnosti platformy, které jsou klíčem k úspěchu podniků v oblasti průmyslu IoT a jejich digitální revoluce, patří:

- Vícevrstvé úložiště, teplé i studené.
- Možnost ukládání dat časových řad do desítkových let
- Možnost explicitního modelování a optimalizace dotazů pro provozní Intelligence na základě assetu.

Time Series Insights je ucelená a kompletní nabídka PaaS pro zkoumání dat IoT a Operational Insights. Time Series Insights nabízí plně spravovanou cloudovou službu pro analýzu dat časových řad IoT-Scale.

Nezpracované data můžete ukládat do úložiště v paměti bez schématu. V rámci distribuovaného dotazovacího stroje a rozhraní API pak můžete provádět interaktivní dotazy ad hoc. Využijte ucelené uživatelské prostředí a vizualizujte miliardy událostí v řádu sekund. Přečtěte si další informace o [možnostech zkoumání dat](./time-series-insights-overview.md).

Time Series Insights také nabízí možnosti Operational Insights, které jsou momentálně ve verzi Preview. Společně s interaktivním zkoumáním dat a provozními inteligentními funkcemi můžete použít Time Series Insights k odvození více hodnot dat shromážděných z prostředků IoT. Nabídka Preview podporuje:

* Škálovatelné a finančně optimalizované úložiště dat časových řad. Toto cloudové řešení IoT může v sekundách vycházet z dat časových řad do trendů.
* Podpora sémantického modelu, která popisuje doménu a Metadata přidružená k odvozeným a neodvozeným signálům z prostředků a zařízení.
* Vylepšené uživatelské prostředí, které kombinuje datové přehledy založené na assetech s bohatou a ad hoc datovou analýzou. Tato kombinace jednotek poskytuje obchodní a provozní inteligentní funkce.
* Integrace s pokročilým nástrojem Machine Learning a analytickými nástroji. Mezi tyto nástroje patří Azure Databricks, Apache Spark, Azure Machine Learning, poznámkové bloky Jupyter a Power BI. Tyto nástroje vám pomůžou řešit problémy s daty řady času a řídit provozní efektivitu.

Provozní přehledy a zkoumání dat jsou společně nabízeny s jednoduchým cenovým modelem průběžných plateb pro zpracování, ukládání a dotazování dat. Tento model fakturace je vhodný pro vaše měnící se potřeby vaší firmy.

Tento diagram vysoké úrovně toku dat zobrazuje aktualizace.

  ![Klíčové funkce][2]

Díky zavedení těchto klíčových funkcí pro průmyslové účely IoT Time Series Insights poskytuje následující klíčové výhody.

| | |
| ---| ---|
| Vícevrstvé úložiště pro data časových řad IoT-Scale | S běžným kanálem pro zpracování dat pro ingestování dat můžete ukládat data do teplého úložiště pro interaktivní dotazy. Data můžete také ukládat do studeného úložiště pro velké objemy dat. Využijte výhod vysoce výkonných [dotazů](./time-series-insights-update-tsq.md)založených na prostředcích. |
| Model časové řady pro dátí nezpracované telemetrie a odvození přehledů na základě assetů | Dát nezpracovaná data telemetrie s popisným [modelem časové řady](./time-series-insights-update-tsm.md). Vyvozuje bohatou provozní logiku s vysoce výkonnými a cenově optimalizovanými dotazy založenými na zařízeních. |
| Plynulá a kontinuální integrace s jinými datovými řešeními | Data v Time Series Insights se [ukládají](./time-series-insights-update-storage-ingress.md) do souborů Apache Parquet v open source. Tato integrace s jinými datovými řešeními, ať už první nebo třetí stranou, je snadná pro komplexní scénáře. Mezi tyto scénáře patří business intelligence, pokročilé Machine Learning a prediktivní analýzy. |
| Zkoumání dat téměř v reálném čase | Prostředí [Preview aplikace Azure Time Series Insights Explorer](./time-series-insights-update-explorer.md) poskytuje vizualizaci pro všechna streamování dat prostřednictvím kanálu přijímání. Krátce po připojení zdroje událostí můžete zobrazit, prozkoumat a dotazovat data událostí. Tímto způsobem můžete ověřit, jestli zařízení vysílá data podle očekávání. Můžete také monitorovat prostředek IoT pro stav, produktivitu a celkovou efektivitu. |
| Analýza hlavních příčin a detekce anomálií | [Průzkumník Preview aplikace Azure Time Series Insights](./time-series-insights-update-explorer.md) podporuje jak zobrazení vzorků, tak zobrazení perspektiv pro provádění a ukládání více kroků při analýze hlavní příčiny. V kombinaci s Azure Stream Analytics můžete pomocí Time Series Insights detekovat výstrahy a anomálie téměř v reálném čase. |
| Vlastní aplikace založené na Time Series Insights platformě | Time Series Insights podporuje [sadu JavaScript SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). Sada SDK poskytuje bohatý ovládací prvky a zjednodušený přístup k dotazům. Pomocí sady SDK můžete sestavovat vlastní aplikace IoT nad Time Series Insights tak, aby vyhovovaly vašim konkrétním obchodním potřebám. [Rozhraní API pro Time Series Insights dotazování](./time-series-insights-update-tsq.md) můžete použít také přímo k pohonu dat do vlastních aplikací IoT. |

## <a name="next-steps"></a>Další kroky

Začínáme s Azure Time Series Insights Preview:

> [!div class="nextstepaction"]
> [Přečtěte si příručku pro rychlý Start.](./time-series-insights-update-quickstart.md)

Další informace o případech použití:

> [!div class="nextstepaction"]
> [Případy použití ve verzi Preview pro Azure Time Series Insights](./time-series-insights-update-use-cases.md)

<!-- Images -->
[1]: media/v2-update-overview/overview-one.png
[2]: media/v2-update-overview/overview-two.png
