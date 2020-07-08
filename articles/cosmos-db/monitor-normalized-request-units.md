---
title: Monitorujte normalizované RU/s pro kontejner Azure Cosmos nebo účet.
description: Naučte se monitorovat využití normalizované jednotky žádostí o operaci v Azure Cosmos DB. Vlastníci Azure Cosmos DB účtu můžou pochopit, které operace spotřebovávají více jednotek žádostí.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 06/25/2020
ms.openlocfilehash: 8709389208ba1320685b1834b20893f08ef33ed7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85482900"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>Jak monitorovat normalizovaná RU/s pro kontejner Azure Cosmos nebo účet

Azure Monitor pro Azure Cosmos DB poskytuje zobrazení metrik pro monitorování vašeho účtu a vytváření řídicích panelů. Metriky Azure Cosmos DB jsou ve výchozím nastavení shromažďovány, takže tato funkce nevyžaduje explicitní povolení ani konfiguraci.

**Normalizovaná** metrika použití ru se používá k zobrazení, jak dobře nasycené repliky se týkají spotřeby jednotek požadavků v rozsahu klíče oddílu. Azure Cosmos DB distribuuje propustnost rovnoměrně napříč všemi fyzickými oddíly. Tato metrika poskytuje za sekundu zobrazení maximálního využití propustnosti v rámci sady replik. Pomocí této metriky můžete vypočítat využití RU/s v jednotlivých oddílech pro daný kontejner. Pokud při použití této metriky vidíte vysoké procento využití jednotek požadavků, měli byste zvýšit propustnost tak, aby splňovala potřeby vašich úloh.

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>Co očekávat a když je normalizované RU/s vyšší

Když normalizovaná spotřeba RU/s dosáhne 100%, klient obdrží chyby s omezením četnosti. Klient by měl respektovat dobu čekání a opakovat akci. Pokud existuje krátký špička, která dosahuje 100% využití, znamená to, že propustnost repliky dosáhla svého maximálního limitu výkonu. Například jedna operace, například uložená procedura, která spotřebovává všechny RU/s na replice, povede k krátkému nárůstu normalizované spotřeby RU/s. V takových případech nedojde k žádné okamžité rychlosti, která by omezila chyby, pokud je míra požadavků nízká. Důvodem je to, že Azure Cosmos DB umožňuje žádostem o poskytnutí více než zřízené RU/s pro konkrétní požadavek a další žádosti v tomto časovém období jsou omezeny na míru.

Metriky Azure Monitor vám pomůžou najít operace na stavový kód pomocí metriky **celkových požadavků** . Později můžete tyto požadavky filtrovat pomocí kódu stavu 429 a rozdělit je podle **typu operace**.

Pokud chcete zjistit, které požadavky jsou omezené, doporučuje se tyto informace získat prostřednictvím diagnostických protokolů.

Pokud je nepřetržitá špička z 100% normalizované spotřeby RU/s nebo blízko až 100%, doporučuje se zvýšit propustnost. Pomocí metrik Azure monitoru a protokolů Azure monitor můžete zjistit, které operace jsou těžké a jejich špičkové využití.

**Normalizovaná metrika spotřeby ru** se používá také k tomu, abyste viděli, který rozsah klíčů oddílu je pro účely využití zahříváníný. Takže vám poskytneme zešikmení propustnosti směrem k rozsahu klíče oddílu. Později můžete sledovat protokol **PartitionKeyRUConsumption** Azure monitor protokoly, abyste získali informace o tom, které klíče logického oddílu jsou z pohledu na používání aktivní.

## <a name="view-the-normalized-request-unit-consumption-metric"></a>Zobrazení normalizované metriky spotřeby jednotek požadavků

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

2. V levém navigačním panelu vyberte **monitor** a vyberte **metriky**.

   :::image type="content" source="./media/monitor-normalized-request-units/monitor-metrics-blade.png" alt-text="Podokno metrik v Azure Monitor":::

3. V podokně **metriky** > **Vyberte prostředek** > zvolte požadované **předplatné**a **skupinu prostředků**. Jako **typ prostředku**vyberte **Azure Cosmos DB účty**, zvolte jeden ze stávajících účtů Azure Cosmos a pak vyberte **použít**.

   :::image type="content" source="./media/monitor-normalized-request-units/select-cosmos-db-account.png" alt-text="Volba účtu Azure Cosmos k zobrazení metrik":::

4. Dále můžete vybrat metriku ze seznamu dostupných metrik. Můžete vybrat metriky specifické pro jednotky žádosti, úložiště, latenci, dostupnost, Cassandra a další. Podrobné informace o všech dostupných metrikách v tomto seznamu najdete v článku [metriky podle kategorií](monitor-cosmos-db-reference.md) . V tomto příkladu vybereme normalizovanou metriku s **využitím ru** a **Maximum** jako hodnotu agregace.

   Kromě těchto podrobností můžete také vybrat **časový rozsah** a **časovou členitost** metrik. V poli Max (maximum) si můžete zobrazit metriky za posledních 30 dní.  Po použití filtru se v závislosti na vašem filtru zobrazí graf.

   :::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png" alt-text="Vyberte metriku z Azure Portal":::

### <a name="filters-for-normalized-request-unit-consumption"></a>Filtry normalizované spotřeby jednotek požadavků

Můžete také filtrovat metriky a graf zobrazený podle konkrétního typu **CollectionName**, **DatabaseName**, **PartitionKeyRangeID**a **oblasti**. Pokud chcete metriky filtrovat, vyberte **Přidat filtr** a zvolte požadovanou vlastnost, jako je například **CollectionName** a odpovídající hodnota, které vás zajímají. Graf pak zobrazí normalizované jednotky pro spotřebu RU spotřebované pro kontejner pro vybrané období.  

Metriky můžete seskupit pomocí možnosti **použít rozdělení** .  

Normalizovaná metrika spotřeby jednotek žádostí pro každý kontejner se zobrazí, jak je znázorněno na následujícím obrázku:

:::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png" alt-text="Použít filtry na normalizovanou metriku spotřeby jednotek požadavků":::

## <a name="next-steps"></a>Další kroky

* Monitorujte Azure Cosmos DB data pomocí [nastavení diagnostiky](cosmosdb-monitor-resource-logs.md) v Azure.
* [Auditování operací roviny ovládacího prvku Azure Cosmos DB](audit-control-plane-logs.md)
