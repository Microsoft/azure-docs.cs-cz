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
ms.date: 11/28/2018
ms.openlocfilehash: 5400d8eeba1983d3137b5145c2f885faee036417
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52853044"
---
# <a name="azure-time-series-insights-overview"></a>Přehled služby Azure Time Series Insights

Azure Time Series Insights (TSI) je začátku do konce Platform-As-A-Service k ingestování, zpracování, ukládání a dotazování dat IoT měřítku vysoce uvádí v kontextu, optimalizované řady čas. V důsledku toho je ideální pro ad-hoc zkoumání dat, jakož i provozní analýzy Azure TSI. TSI je jednoznačně rozšiřitelný, přizpůsobené, služba, že splňuje širší potřebuje průmyslového IoT nasazení.

## <a name="what-is-iot-data"></a>Co je IoT dat?

IoT data jsou všechna "průmyslové" data k dispozici v organizacích náročné asset.
IoT data je vysoce nestrukturovaných často, protože byl zaslán celou řadu prostředků, které zaznamenávají poměrně hlučného měření, jako je například teploty, vlhkosti a pohybu.

Kromě toho těchto datových proudů se často vyznačují významné mezery, poškozené zprávy a hodnoty false. Data z těchto datových proudů musí vyčistit, předtím, než může dojít, analýzu. Dat IoT často má smysl pouze v kontextu vstupy další data přicházející z první-(jako je ERP a CRM) nebo jiného zdroje dat (například počasí nebo umístění).

V důsledku toho zanedbatelný část těchto dat se používá pro účely provozní a obchodní. Taková data poskytuje konzistentní vzhledem k aplikacím, komplexní, aktuální a správné informace pro hlášení a analýzy. IoT shromažďují měnící data na užitečné přehledy proto vyžadují některé z klíčových funkcí:

* Zpracování dat k vyčištění, filtrovat, interpolovat, transformovat a připravit data pro analýzu
* Struktura k procházení a pochopení dat (a normalizovat contextualize data)
* Nákladově efektivní úložiště pro dlouhé / nekonečná doba uchování desítky za zpracovaná (odvozené) i nezpracovaná data

Typický tok dat IoT může být použité v následujícím způsobem:

  ![Tok dat IoT][1]

## <a name="azure-time-series-insights-for-industrial-iot"></a>Azure Time Series Insights pro průmyslového IoT

Aktuální na šířku IoT je různorodý. Zahrnuje zákazníkům pokrývání uzlů výrobní, automobile, ropy a plynu, power & nástroj, Chytré budovy a poradenství. Scénáře zahrnují ad-hoc zkoumání dat kde struktuře dat je neznámý, jakož i provozní analýzy schematizovanými (explicitně modelové) data na disku provozní efektivitu. Tyto scénáře obvykle existovat vedle sebe a podporovat různé případy použití. Funkce platformy, jako je více vrstvách úložiště (warm i cold), možnost ukládat za desítky dat časových řad a schopnost explicitně model a optimalizace dotazů pro informace o provozu na základě prostředku se stávají klíče pro úspěch průmyslové podniky IoT a jejich digitální revolution.

Azure TSI je komplexní nabídka Platform-As-A-Service začátku do konce pro zkoumání dat IoT i operational insights. Time Series Insights nabízí plně spravované cloudové služby pro analýzu dat časových řad IoT měřítku.

Zákazníky můžete ukládat nezpracovaných dat v úložišti bez schématu, v paměti a provádět interaktivní ad-hoc dotazů pomocí distribuovaných dotazů modul a rozhraní API také využívat naše bohaté uživatelské prostředí pro vizualizaci miliardy událostí v řádu sekund. Další informace o našich [funkce zkoumání dat](./time-series-insights-overview.md).

TSI také nabízí možnosti operational insights aktuálně ve verzi Public Preview. Společně s interaktivní zkoumání dat a provozní informace Time Series Insights umožňuje zákazníkům lépe zhodnotit data shromážděná z prostředků IoT, které jsou odvozeny. Konkrétně nabídka veřejné verze Preview podporuje následující klíčové funkce:

* A škálovatelné, výkonu a úložiště dat řady optimalizovat náklady na čas, který umožňuje založené na cloudu IoT řešení za trend let dat časových řad v řádu sekund.
* Podpora sémantického modelu k popisu domény a metadata přidružená k odvozené a neodvozený signály ze zařízení a prostředky.
* Vylepšené uživatelské prostředí, které kombinuje přehledy dat na základě asset s funkcemi nabitý analýzy dat ad hoc za zvyšování obchodní a provozní intelligence
* Integrace s rozšířené strojové učení a analytické nástroje, jako je Azure Databricks, Apache Spark, Azure Machine Learning, poznámkové bloky Jupyter, Power BI, atd. pomáhá zákazníkům řešit čas vyzve data řady a jednotka provozní efektivitu.

Společně operational insights a zkoumání dat jsou k dispozici s jednoduchou s průběžnými platbami cenový model pro zpracování dat, úložiště a dotaz, a tím zákazníkům poskytují mnohem větší škálovatelnost model tak, aby vyhovovaly jejich měnícím se potřebám organizace.

Tady je diagram toku vysoké úrovně dat, který znázorňuje aktualizované možnosti:

  ![Klíčové funkce][2]

Po zavedení těchto klíčových funkcí průmyslového IoT Azure TSI poskytuje následující klíčové výhody.

* Víceúrovňová úložiště pro data časových řad IoT měřítku

  * Pomocí běžných kanál zpracování dat pro příjem dat zákazníci mají možnost ukládat data do horké úložiště pro interaktivní dotazy nebo studeného úložiště pro ukládání velkých objemů dat a využijte výhod vysoce výkonný, dotazy pro asset.

  * Dynamické směrování ve vrstvách úložiště je již brzy.

* Model časové řady pro contextualizing nezpracovaná telemetrická data a odvozování přehledy na základě prostředků

  * Zákazníci mohou contextualize nezpracovaná telemetrická data pomocí modelu popisný časové řady a odvodit bohaté provozní informace pomocí vysoce optimalizovaný výkon a náklady na dotazy na základě zařízení.

* Bezproblémová integrace s jinými řešeními pro data
  
  * Vzhledem k tomu, že data v Azure Time Series Insights je uložena v open source soubory Apache Parquet, zákazníkům umožní snadnou integraci s jinými řešeními pro data (první nebo třetí strana) pro scénáře začátku do konce včetně business intelligence, strojové učení, rozšířené Prediktivní analýza, atd.

* Téměř zkoumání dat v reálném čase

  * Uživatelské prostředí Azure Průzkumník Time Series Insights poskytuje vizualizace pro všechna data prostřednictvím kanálu ingestování datových proudů. Krátce po připojení zdroje událostí se zákazníci zobrazení, prozkoumání a dotazování na data události pro ověření, zda je generování dat podle očekávání a monitorovat prostředek IoT pro stav, produktivitu a celkovou efektivitu zařízení.

* Detekce anomálií a analýzy původních příčin

  * Průzkumník Azure Time Series Insights podporuje perspektivy zobrazení chování a uložte vícekrokového původních příčin a vzory. V kombinaci s Azure Stream Analytics můžete použít zákazníci služby Time Series Insights k detekci výstrahy a anomálie v téměř v reálném čase.

* Vytváření vlastních aplikací na platformě Time Series Insights

  * Azure Time Series Insights JavaScript SDK podporuje bohaté ovládací prvky a zjednodušený přístup do dotazů tak, aby zákazníci vytvářet vlastní aplikace IoT na platformě Time Series Insights podle potřeb jednotlivých podniků.
  * Zákazníci mohou používat také Time Series Insights dotaz rozhraní API přímo na jednotky data do vlastních aplikací IoT.

## <a name="next-steps"></a>Další kroky

Jste připravení začít s Azure TSI Private Preview:

> [!div class="nextstepaction"]
> [Přečtěte si příručku rychlý start](./time-series-insights-update-quickstart.md)

<!-- Images -->
[1]: media/v2-update-overview/overview_one.png
[2]: media/v2-update-overview/overview_two.png