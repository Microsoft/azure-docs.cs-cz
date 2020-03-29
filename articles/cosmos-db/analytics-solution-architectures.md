---
title: Řešení využívající globálně distribuované analýzy v Azure Cosmos DB.
description: Seznamte se s řešeními, která můžete sestavit pomocí globálně distribuované analýzy v Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: d0b5042c1401a95cc75f4c01ace843659b6babfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73681760"
---
# <a name="solutions-using-globally-distributed-analytics-in-azure-cosmos-db"></a>Řešení využívající globálně distribuované analýzy v Azure Cosmos DB

Tento článek popisuje řešení, která lze sestavit pomocí globálně distribuované analýzy v Azure Cosmos DB.

## <a name="retail-and-consumer-goods"></a>Maloobchodní a spotřební zboží

Pomocí podpory Spark v Azure Cosmos DB můžete poskytovat doporučení a nabídky v reálném čase. Pomocí individuálního nastavení v reálném čase a doporučení produktů můžete zákazníkům pomoci objevit položky, které budou potřebovat.

* Integrovaná podpora machine learningu poskytovaná runtimeApache Spark můžete použít ke generování doporučení v reálném čase v katalogech produktů.

* Můžete těžit data streamového proudu kliknutí, nákupní data a zákaznická data a poskytnout cílená doporučení, která pohánějí celoživotní hodnotu.

* Pomocí funkce globální distribuce Azure Cosmos DB lze v milisekundách analyzovat velké objemy produktových dat rozprostřených mezi oblasti.

* Můžete rychle získat přehledy o geograficky distribuovaných uživatelích a datech. Míru konverze propagace můžete zlepšit tím, že budete správnou reklamu zobrazovat správnému uživateli ve správný čas.

* Vestavěnou funkci streamování Spark můžete využít k obohacení živých dat tím, že je zkombinujete se statickými zákaznickými daty. Tímto způsobem můžete poskytovat personalizovanější a cílenější reklamy v reálném čase a v kontextu s tím, co zákazníci dělají.

Následující obrázek ukazuje, jak se podpora Azure Cosmos DB Spark používá k optimalizaci cen a propagačních akcí:

![Podpora Spark Azure Cosmos DB pro optimalizaci cen a propagačních akcí](./media/analytics-solution-architectures/optimize-pricing-and-promotions.png)


Následující obrázek ukazuje, jak se podpora Azure Cosmos DB Spark používá v modulu doporučení v reálném čase:

![Podpora Azure Cosmos DB Spark v modulu doporučení v reálném čase](./media/analytics-solution-architectures/real-time-recommendation-engine.png)

## <a name="manufacturing-and-iot"></a>Výroba a IoT

Integrovaná analytická platforma Azure Cosmos DB umožňuje povolit analýzu dat IoT v reálném čase z milionů zařízení v globálním měřítku. Můžete provádět moderní inovace, jako je předpovídání počasí, prediktivní analýza a optimalizace energie.

* Pomocí Azure Cosmos DB můžete těžit data, jako jsou metriky aktiv v reálném čase a faktory počasí, a pak použít analýzu inteligentních sítí k optimalizaci výkonu připojených zařízení v terénu. Analýza inteligentních sítí je klíčem ke kontrole provozních nákladů, ke zlepšení spolehlivosti sítě a poskytování personalizovaných energetických služeb spotřebitelům.

Následující obrázek ukazuje, jak se podpora Spark azure cosmos db používá ke čtení metrik ze zařízení IoT a k použití analýzy inteligentních sítí:

![Podpora Spark Azure Cosmos DB pro čtení metrik ze zařízení IoT](./media/analytics-solution-architectures/read-metrics-from-iot-devices.png)

## <a name="predictive-maintenance"></a>Prediktivní údržba

* Udržování aktiv, jako jsou kompresory, které se používají v malých vrtných soupravách na hlubinných plošinách, je složité úsilí. Tyto prostředky jsou umístěny po celém světě a generovat petabajty dat. Pomocí Azure Cosmos DB můžete vytvořit kanál prediktivních dat od konce, který používá streamování Spark ke zpracování velkého množství telemetrie senzorů, ukládání částí prostředků a dat mapování senzorů.

* Můžete vytvářet a nasazovat modely strojového učení předpovědět selhání majetku dříve, než k nim dojde, a vydat příkazy údržby práce před selháním dojde.

Následující obrázek ukazuje, jak se podpora Spark azure cosmos db používá k vytvoření systému prediktivní údržby:

![Podpora Spark azure cosmos DB pro sestavení systému prediktivní údržby](./media/analytics-solution-architectures/predictive-maintenance-system.png)

Následující obrázek ukazuje, jak se podpora Spark azure cosmos DB používá k vytvoření diagnostického systému vozidla v reálném čase:

![Podpora Spark azure cosmos DB pro vytvoření diagnostického systému vozidel v reálném čase](./media/analytics-solution-architectures/real-time-vehicle-diagnostic-system.png)

## <a name="gaming"></a>Hry

* Díky integrované podpoře Spark vám Azure Cosmos DB umožňuje snadno vytvářet, škálovat a nasazovat pokročilé modely analýz a strojového učení během několika minut a vytvářet tak nejlepší možné herní prostředí.

* Můžete analyzovat data o hráčích, nákupech a chováních a vytvářet relevantní personalizované nabídky, abyste dosáhli vysoké míry konverze.

* Pomocí strojového učení Spark můžete analyzovat a získat přehled o telemetrických datech her. Můžete diagnostikovat a předcházet pomalému načítání a problémům ve hře.

Následující obrázek ukazuje, jak se podpora Spark Azure Cosmos DB používá v herní analýze:

![Podpora Azure Cosmos DB Spark v oblasti herní analýzy](./media/analytics-solution-architectures/gaming-analytics.png)

## <a name="next-steps"></a>Další kroky

* Další informace o výhodách Azure Cosmos DB najdete v článku [přehledu.](introduction.md)
* [Začínáme s rozhraním AZURE Cosmos DB API pro MongoDB](mongodb-introduction.md)
* [Začínáme s rozhraním AZURE Cosmos DB Cassandra API](cassandra-introduction.md)
* [Začínáme s rozhraním Azure Cosmos DB Gremlin API](graph-introduction.md)
* [Začínáme s rozhraním API tabulky Azure Cosmos DB](table-introduction.md)
