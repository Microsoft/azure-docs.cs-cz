---
title: Řešení využívající globálně distribuované analýzy v Azure Cosmos DB.
description: Seznamte se s řešeními, která se dají sestavit pomocí globálně distribuovaných analýz v Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: d0b5042c1401a95cc75f4c01ace843659b6babfe
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "73681760"
---
# <a name="solutions-using-globally-distributed-analytics-in-azure-cosmos-db"></a>Řešení využívající globálně distribuované analýzy v Azure Cosmos DB

Tento článek popisuje řešení, která se dají vytvořit pomocí globálně distribuovaných analýz v Azure Cosmos DB.

## <a name="retail-and-consumer-goods"></a>Maloobchodní a spotřební zboží

Podporu Sparku v Azure Cosmos DB můžete použít k poskytování doporučení a nabídek v reálném čase. Zákazníkům můžete podávat informace o položkách, které budou potřebovat při individuálním přizpůsobení a doporučeních produktu v reálném čase.

* Pomocí integrované podpory Machine Learning poskytované modulem runtime Apache Spark můžete vygenerovat doporučení v reálném čase napříč katalogy produktů.

* Můžete kliknout na streamovat data, koupit data a zákaznická data a zajistit tak cílené doporučení, která zajišťují, aby byla hodnota životnosti.

* Pomocí funkce globální distribuce Azure Cosmos DB lze analyzovat velké objemy dat produktu, které jsou rozdělené do různých oblastí, v milisekundách.

* Můžete rychle získat přehled o geograficky distribuovaných uživatelích a datech. Můžete zvýšit míru převodu povýšení tím, že zachováte správnou reklamu správnému uživateli ve správný čas.

* Pomocí integrované funkce streamování Sparku můžete rozšířit živá data tak, že je zkombinujete se statickými Zákaznickými daty. Tímto způsobem můžete doručovat lépe přizpůsobené a cílené reklamy v reálném čase a v kontextu s tím, co zákazníci dělají.

Následující obrázek ukazuje, jak se Azure Cosmos DB podpora Sparku používá k optimalizaci cen a propagačních akcí:

![Podpora Sparku v Azure Cosmos DB pro optimalizaci cen a propagačních akcí](./media/analytics-solution-architectures/optimize-pricing-and-promotions.png)


Následující obrázek ukazuje, jak se Azure Cosmos DB podpora Sparku používá v modulu doporučení v reálném čase:

![Podpora Azure Cosmos DB Spark v modulu doporučení v reálném čase](./media/analytics-solution-architectures/real-time-recommendation-engine.png)

## <a name="manufacturing-and-iot"></a>Výroba a IoT

Integrovaná analytická platforma Azure Cosmos DB umožňuje povolit analýzu dat IoT v reálném čase z milionů zařízení v globálním měřítku. Můžete vytvářet moderní inovace, jako je předpověď vzorců počasí, prediktivní analýzy a optimalizace energií.

* Pomocí Azure Cosmos DB můžete svoje data, jako jsou metriky prostředků v reálném čase a povětrnostní faktory, použít k optimalizaci výkonu připojených zařízení v poli pomocí analýzy inteligentních mřížek. Analýza inteligentních mřížek je klíč, který řídí provozní náklady, vylepšuje spolehlivost v Gridech a dodává přizpůsobené energetické služby zákazníkům.

Následující obrázek ukazuje, jak se Azure Cosmos DB podpora Sparku používá ke čtení metrik ze zařízení IoT a k použití analýzy inteligentních mřížek:

![Podpora Sparku Azure Cosmos DB pro čtení metrik ze zařízení IoT](./media/analytics-solution-architectures/read-metrics-from-iot-devices.png)

## <a name="predictive-maintenance"></a>Prediktivní údržba

* Udržování prostředků, jako jsou například kompresory používané v malých vrtných plošinách, do hlubinných platforem je komplexní Endeavor. Tyto prostředky se nacházejí po celém světě a generují petabajty dat. Pomocí Azure Cosmos DB můžete vytvořit kompletní prediktivní datový kanál, který využívá Spark streamování ke zpracování velkých objemů telemetrie senzorů, částí prostředků a dat mapování senzorů.

* Můžete vytvářet a nasazovat modely strojového učení pro předpověď selhání prostředků předtím, než dojde k selhání, a vystavit pracovní objednávky údržby.

Následující obrázek ukazuje, jak se Azure Cosmos DB podpora Sparku používá k vytvoření prediktivního systému údržby:

![Podpora Sparku Azure Cosmos DB k vytvoření prediktivního systému údržby](./media/analytics-solution-architectures/predictive-maintenance-system.png)

Následující obrázek ukazuje, jak se Azure Cosmos DB podpora Sparku používá k sestavení diagnostického systému pro vozidlo v reálném čase:

![Podpora Sparku Azure Cosmos DB pro sestavení diagnostického systému pro vozidlo v reálném čase](./media/analytics-solution-architectures/real-time-vehicle-diagnostic-system.png)

## <a name="gaming"></a>Hry

* Díky integrované podpoře Spark vám Azure Cosmos DB umožňuje snadno sestavovat, škálovat a nasazovat pokročilé analýzy a modely strojového učení během několika minut, aby se vytvořily nejlepší možné herní prostředí.

* Můžete analyzovat data z přehrávače, nákupu a chování a vytvořit relevantní individuální nabídky pro dosažení vysoké míry převodu.

* Pomocí Spark Machine Learning můžete analyzovat a získávat přehledy o datech herních telemetrie. Můžete diagnostikovat a zabránit pomalým časům načítání a problémům s hrou.

Následující obrázek ukazuje, jak se v herních analýzách používá Azure Cosmos DB podpora Sparku:

![Podpora Sparku v Azure Cosmos DB v herních analýzách](./media/analytics-solution-architectures/gaming-analytics.png)

## <a name="next-steps"></a>Další kroky

* Další informace o výhodách Azure Cosmos DB najdete v článku [Přehled](introduction.md) .
* [Začínáme s rozhraním API Azure Cosmos DB pro MongoDB](mongodb-introduction.md)
* [Začínáme s Azure Cosmos DB rozhraní API Cassandra](cassandra-introduction.md)
* [Začínáme s rozhraním API Azure Cosmos DB Gremlin](graph-introduction.md)
* [Začínáme s Azure Cosmos DB rozhraní API pro tabulky](table-introduction.md)
