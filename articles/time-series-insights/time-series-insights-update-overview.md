---
title: 'Přehled: Azure Time Series Insights ve verzi Preview | Dokumentace Microsoftu'
description: Přehled služby Azure čas Series Insights ve verzi Preview.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.workload: big-data
ms.topic: overview
ms.date: 12/05/2018
ms.custom: seodec18
ms.openlocfilehash: 847eddc78f8abc938e68e6fe383a773dadeaf779
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53557831"
---
# <a name="azure-time-series-insights-preview-overview"></a>Přehled služby Azure čas Series Insights ve verzi Preview

Azure čas Series Insights ve verzi Preview je nabídka platforma jako služba začátku do konce. Používá se k ingestování, zpracování, ukládání a dotazování dat IoT měřítku vysoce uvádí v kontextu, optimalizované řady čas. Time Series Insights je ideální pro ad-hoc zkoumání dat a provozní analýzy. Time Series Insights je služba jednoznačně rozšiřitelný a přizpůsobené nabídky, že splňuje širší potřebuje průmyslového IoT nasazení.

## <a name="video"></a>Video

Toto video přináší přehled Azure čas Series Insights ve verzi Preview, analytické platformy IoT založené na cloudu.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="define-iot-data"></a>Definování dat IoT

IoT data jsou všechna "průmyslové" data, která je k dispozici v organizacích asset náročné. Dat IoT je často vysoce nestrukturovaných, protože se odesílá z prostředků, které zaznamenávají poměrně hlučného měření. Tyto zahrnují teploty, pohybu a vlhkosti. Tyto datové proudy se často vyznačují významné mezery, poškozené zprávy a hodnoty false. Data z těchto datových proudů musí vyčistit, předtím, než může dojít, analýzu. IoT data často je smysluplná pouze v kontextu další datové výstupy, které pocházejí z první strany zdrojů, jako je například CRM a ERP. Vstupy pocházet také zdroje dat třetí strany, jako je například počasí nebo umístění.

Pro účely provozní a obchodní v důsledku toho se používá jenom část data. Taková data poskytuje konzistentní vzhledem k aplikacím, komplexní, aktuální a správné informace pro hlášení a analýzy. Zapnutí shromážděná data na užitečné přehledy vyžadují IoT:

* Zpracování dat k vyčištění, filtrovat, interpolovat, transformovat a připravit data pro analýzu.
* Struktura k procházení a pochopení dat, to znamená, abyste mohli normalizovat a contextualize data.
* Nákladově efektivní úložiště pro dlouhé nebo nekonečná doba uchování za zpracovaná nebo odvozené desítky let, data a nezpracovanými daty.

Typický tok dat IoT je znázorněno na následujícím obrázku.

  ![Tok dat IoT][1]

## <a name="azure-time-series-insights-for-industrial-iot"></a>Azure Time Series Insights pro průmyslového IoT

Aktuální na šířku IoT je různorodý. Zákazníci span výrobním průmyslu, energie, nástroje, Chytré budovy a konzultační odvětví. Scénáře zahrnují ad-hoc zkoumání dat kde obrazec dat neznámý. Scénáře zahrnují také provozní analýzy schematizovanými nebo explicitně modelové data na disku provozní efektivitu. Tyto scénáře obvykle existovat vedle sebe a podporovat různé případy použití. Funkce platformy, které jsou klíčem k úspěchu průmyslové podniky IoT a jejich digitální revolution patří:

- S více vrstvami úložiště, warm i cold. 
- Možnost ukládat data časových řad za desítky let. 
- Schopnost explicitně model a optimalizace dotazů pro informace o provozu na základě asset.

Time Series Insights je začátku do konce, komplexní platforma jako služba nabízí pro zkoumání dat IoT a operational insights. Time Series Insights nabízí plně spravované cloudové služby pro analýzu dat časových řad IoT měřítku.

Nezpracovaná data můžete ukládat v úložišti bez schématu, v paměti. Interaktivní ad-hoc dotazů můžete pak provádět prostřednictvím distribuovaného dotazu modul a rozhraní API. Ujistěte se, vizualizovat miliardy událostí během několika sekund pomocí bohaté možnosti uživatelského prostředí. Další informace o [funkce zkoumání dat](./time-series-insights-overview.md).

Time Series Insights také nabízí možnosti operational insights momentálně ve verzi preview. Společně s interaktivní zkoumání dat a provozní informace můžete použít Time Series Insights k odvození vyšší hodnotu z dat shromážděných z prostředků IoT. Ve verzi preview nabízí podporuje:

* A škálovatelné, výkonu a dat časové náklady na paměťově optimalizované řady úložiště. Toto řešení IoT založené na cloudu můžete trend za let dat časových řad v řádu sekund.
* Podpora sémantického modelu, který popisuje domény a metadata přidružená k odvozené a neodvozený signály ze zařízení a prostředky.
* Vylepšené uživatelské prostředí, které kombinuje přehledy dat na základě asset s bohatou, analýzy dat ad hoc. Tato kombinace pohání obchodní a provozní informace.
* Integrací pokročilých machine learning a analytických nástrojů. Nástroje zahrnují Azure Databricks, Apache Spark, Azure Machine Learning, poznámkové bloky Jupyter a Power BI. Tyto nástroje umožňují řešit čas vyzve data řady a posílení provozní efektivitu.

Nabízíme společně, operational insights a zkoumání dat pomocí jednoduchého modelu s průběžnými platbami cen pro zpracování dat, ukládání a dotazování. Tento model fakturace je vhodné pro vaše měnící se potřeby organizace.

Tato základní data vývojový diagram zobrazuje aktualizace.

  ![Klíčové funkce][2]

Po zavedení těchto klíčových funkcí průmyslového IoT Time Series Insights poskytuje následující klíčové výhody.

| | |
| ---| ---|
| **Víceúrovňová úložiště pro data časových řad IoT měřítku** | Pomocí běžných kanál zpracování dat pro příjem dat můžete ukládat data v záložním úložišti pro interaktivní dotazy. Také můžete ukládat data do studeného úložiště pro velké objemy dat. Využijte výhod vysoce výkonné na základě asset [dotazy](./time-series-insights-update-tsq.md). |
| **Model časové řady contextualize nezpracovaná telemetrická data a vyvoďte z nich na základě prostředků** | Contextualize nezpracovaná telemetrická data s popisné [modelu časové řady](./time-series-insights-update-tsm.md). Odvození bohaté provozní informace pomocí vysoce optimalizovaný výkon a náklady na dotazy na základě zařízení. |
| **Hladký a průběžná integrace s jinými řešeními pro data** |  Data v Time Series Insights jsou [uložené](./time-series-insights-update-storage-ingress.md) v open source Apache Parquet soubory. Tato integrace s jinými řešeními pro data, zda první nebo třetí strany, je to jednoduché scénáře začátku do konce. Mezi tyto scénáře patří, business intelligence, pokročilé strojového učení a prediktivní analýzy. |
| **Téměř zkoumání dat v reálném čase** | [Průzkumníka Azure čas Series Insights ve verzi Preview](./time-series-insights-update-explorer.md) činnost koncového uživatele obsahuje vizualizace pro všechna data prostřednictvím kanálu ingestování datových proudů. Krátce po připojíte zdroj událostí, zobrazení, prozkoumání a dotazování na data události. Tímto způsobem můžete ověřit, jestli zařízení posílá data podle očekávání. Také můžete monitorovat prostředek IoT pro stav, produktivitu a celkovou efektivitu. |
| **Detekce anomálií a analýzy původních příčin** | [Průzkumníka Azure čas Series Insights ve verzi Preview](./time-series-insights-update-explorer.md) podporuje model a Perspektiva zobrazení chování a uložit vícekrokových, hlavní příčinu analýzy. V kombinaci s Azure Stream Analytics vám pomůže Time Series Insights zjišťovat upozornění a anomálie v reálném čase. |
| **Vlastní aplikace založené na platformě Time Series Insights** | Time Series Insights podporuje [JavaScript SDK](./tutorial-explore-js-client-lib.md). Sada SDK poskytuje bohaté ovládací prvky a zjednodušený přístup na dotazy. Pomocí sady SDK můžete vytvářet vlastní aplikace IoT nad Time Series Insights tak, aby vyhovovala vašim konkrétním obchodním potřebám. Můžete také použít Time Series Insights [API pro dotazy](./time-series-insights-update-tsq.md) přímo na jednotky data do vlastních aplikací IoT. |

## <a name="next-steps"></a>Další postup

Začínáme s Azure čas Series Insights Preview:

> [!div class="nextstepaction"]
> [Přečtěte si příručku rychlý start](./time-series-insights-update-quickstart.md)

Další informace o případech použití:

> [!div class="nextstepaction"]
> [Případy použití Azure čas Series Insights ve verzi Preview](./time-series-insights-update-use-cases.md)

<!-- Images -->
[1]: media/v2-update-overview/overview_one.png
[2]: media/v2-update-overview/overview_two.png