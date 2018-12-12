---
title: Přehled služby Azure Time Series Insights | Dokumentace Microsoftu
description: Přehled služby Azure Time Series Insights
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.workload: big-data
ms.topic: overview
ms.date: 12/05/2018
ms.openlocfilehash: 9843a01ed3c96b362e17718e9035c378da6c3cf2
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53083389"
---
# <a name="azure-time-series-insights-overview"></a>Přehled služby Azure Time Series Insights

Azure Time Series Insights (TSI) je začátku do konce Platform-As-A-Service k ingestování a zpracování, ukládání a dotazování vysoce uvádí v kontextu, optimalizované řady čas dat IoT měřítku, která je ideální pro ad-hoc zkoumání dat, jakož i provozní analýzy. Azure TSI je služba jednoznačně rozšiřitelný a přizpůsobené nabídky, že splňuje širší potřebuje průmyslového IoT nasazení.

## <a name="video"></a>Video

Toto video přináší přehled služby Azure Time Series Insights (Preview), analytické platformy IoT založené na cloudu.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="defining-iot-data"></a>Definování dat IoT

IoT data jsou všechna "průmyslové" data k dispozici v organizacích náročné asset. IoT data je vysoce nestrukturovaných často, protože byl zaslán celou řadu prostředků, které zaznamenávají poměrně hlučného měření, jako je například teploty, vlhkosti a pohybu. Kromě toho těchto datových proudů se často vyznačují významné mezery, poškozené zprávy a hodnoty false. Data z těchto datových proudů musí vyčistit, předtím, než může dojít, analýzu. Dat IoT, často má smysl pouze v kontextu vstupy další data přicházející z první třetích stran, jako je například CRM a ERP) nebo zdroje dat třetí strany (například počasí nebo umístění).

V důsledku toho zanedbatelný část těchto dat se používá pro účely provozní a obchodní. Taková data poskytuje konzistentní vzhledem k aplikacím, komplexní, aktuální a správné informace pro hlášení a analýzy. IoT shromažďují měnící data na užitečné přehledy vyžaduje, aby několik klíčových funkcí:

* Zpracování dat k vyčištění, filtrovat, interpolovat, transformovat a připravit data pro analýzu
* Struktura k procházení a pochopení dat (a normalizovat contextualize data)
* Nákladově efektivní úložiště pro dlouhé / nekonečná doba uchování desítky za zpracovaná (odvozené) i nezpracovaná data

Typický tok dat IoT může být použité v následujícím způsobem:

  ![Tok dat IoT][1]

## <a name="azure-time-series-insights-for-industrial-iot"></a>Azure Time Series Insights pro průmyslového IoT

Aktuální na šířku IoT je různorodý. Zahrnuje zákazníkům pokrývání uzlů výroby, automobilový průmysl, energie, nástroje, Chytré budovy a konzultační odvětví. Scénáře zahrnují ad-hoc zkoumání dat kde struktuře dat je neznámý, jakož i provozní analýzy schematizovanými (explicitně modelové) data na disku provozní efektivitu. Tyto scénáře obvykle existovat vedle sebe a podporovat různé případy použití. Funkce platformy, jako je více vrstvách úložiště (warm i cold), možnost ukládat za desítky dat časových řad a schopnost explicitně model a optimalizace dotazů pro informace o provozu na základě prostředku se stávají klíče pro úspěch průmyslové podniky IoT a jejich digitální revolution.

Azure TSI je komplexní nabídka Platform-As-A-Service začátku do konce pro zkoumání dat IoT, jak operational insights. TSI nabízí plně spravované cloudové služby pro analýzu dat časových řad IoT měřítku.

Zákazníky můžete ukládat nezpracovaných dat v úložišti bez schématu, v paměti. Zákazníky můžete provést interaktivní ad-hoc dotazy prostřednictvím modulu distribuovaných dotazů a rozhraní API využití naší bohaté uživatelské prostředí pro vizualizaci miliardy událostí v řádu sekund. Další informace o našich [funkce zkoumání dat](./time-series-insights-overview.md).

TSI také nabízí možnosti operational insights momentálně ve verzi Preview. TSI společně s interaktivní zkoumání dat a informace o provozu, umožňuje zákazníkům lépe zhodnotit data shromážděná z prostředků IoT, které jsou odvozeny. Konkrétně nabídka verze Preview podporuje následující klíčové funkce:

* A škálovatelné, výkonu a úložiště dat řady optimalizovat náklady na čas, který umožňuje založené na cloudu IoT řešení za trend let dat časových řad v řádu sekund.
* Podpora sémantického modelu pro popis domény a metadat souvisejících s odvozenými a neodvozenými signály z prostředků a zařízení.
* Vylepšené uživatelské prostředí, které kombinuje přehledy dat na základě asset s funkcemi nabitý analýzy dat ad hoc za zvyšování obchodní a provozní intelligence
* Integrace s rozšířené strojové učení a analytické nástroje, jako je Azure Databricks, Apache Spark, Azure Machine Learning, poznámkové bloky Jupyter, Power BI, atd. pomáhá zákazníkům řešit čas vyzve data řady a jednotka provozní efektivitu.

Společně operational insights a zkoumání dat jsou k dispozici s jednoduchou s průběžnými platbami cenový model pro zpracování dat, úložiště a dotaz, a tím zákazníkům poskytují mnohem větší škálovatelnost model tak, aby vyhovovaly jejich měnícím se potřebám organizace.

Tady je diagram toku vysoké úrovně dat, který znázorňuje aktualizované možnosti:

  ![Klíčové funkce][2]

Po zavedení těchto klíčových funkcí průmyslového IoT Azure TSI poskytuje následující klíčové výhody:

| | |
| ---| ---|
| **Víceúrovňová úložiště pro data časových řad IoT měřítku** | Běžné kanál zpracování dat pro příjem dat zákazníci mají možnost ukládání dat v záložním úložišti pro interaktivní dotazy a/nebo studeného úložiště pro ukládání velkých objemů dat. Zákazníci můžou využít výhod vysoce výkonný, na základě asset, [dotazy](./time-series-insights-update-tsq.md). |
| **Model časové řady pro contextualizing nezpracovaná telemetrická data a odvozování přehledy na základě prostředků** | Zákazníci můžou contextualize nezpracovaná telemetrická data s popisné [modelu časové řady](./time-series-insights-update-tsm.md) a získávat z nich bohaté provozní informace pomocí vysoce optimalizovaný výkon a náklady na dotazy na základě zařízení. |
| **Bezproblémová integrace s jinými řešeními pro data** | Data ve službě Azure TSI [uložené](./time-series-insights-update-storage-ingress.md) v open source soubory Apache Parquet zákazníkům umožní snadnou integraci s jinými řešeními pro data (první nebo třetí strana) pro scénáře začátku do konce včetně business intelligence, pokročilé počítače učení, Prediktivní analýza, atd. |
| **Téměř zkoumání dat v reálném čase** | [Průzkumník služby Azure TSI](./time-series-insights-update-explorer.md) činnost koncového uživatele obsahuje vizualizace pro všechna data prostřednictvím kanálu ingestování datových proudů. Krátce po připojení zdroje událostí se zákazníci zobrazení, prozkoumání a dotazování na data události pro ověření, zda je generování dat podle očekávání a monitorovat prostředek IoT pro stav, produktivitu a celkovou efektivitu zařízení. |
| **Detekce anomálií a analýzy původních příčin** | [Průzkumník služby Azure TSI](./time-series-insights-update-explorer.md) podporuje model a Perspektiva zobrazení chování a uložte vícekrokového, hlavní příčinu, analýzy. Zákazníci mohou ke zjištění výstrahy a anomálie v téměř v reálném čase používat v kombinaci s Azure Stream Analytics, Azure TSI. |
| **Vytváření vlastních aplikací na platformě TSI** | Podporuje Azure TSI [JavaScript SDK](./tutorial-explore-js-client-lib.md). Sada SDK poskytuje bohaté ovládací prvky a zjednodušený přístup na dotazy. Sada SDK umožňuje zákazníkům vytvářet vlastní aplikace IoT založené na Azure TSI tak, aby vyhovovaly jejich konkrétní obchodní potřeby. Zákazníci mohou používat také Azure TSI [API pro dotazy](./time-series-insights-update-tsq.md) přímo na jednotky data do vlastních aplikací IoT. |

## <a name="next-steps"></a>Další postup

Jste připravení začít s Azure TSI (Preview):

> [!div class="nextstepaction"]
> [Přečtěte si příručku rychlý start](./time-series-insights-update-quickstart.md)

Další informace o případech použití:

> [!div class="nextstepaction"]
> [Azure TSI případy použití](./time-series-insights-update-use-cases.md)

<!-- Images -->
[1]: media/v2-update-overview/overview_one.png
[2]: media/v2-update-overview/overview_two.png