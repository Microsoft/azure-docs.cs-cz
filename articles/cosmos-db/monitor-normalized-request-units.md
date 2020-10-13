---
title: Monitorujte normalizované RU/s pro kontejner Azure Cosmos nebo účet.
description: Naučte se monitorovat využití normalizované jednotky žádostí o operaci v Azure Cosmos DB. Vlastníci Azure Cosmos DB účtu můžou pochopit, které operace spotřebovávají více jednotek žádostí.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 06/25/2020
ms.openlocfilehash: 183b161039b86ce824fd0bfde82cf291d54024fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91801473"
---
# <a name="how-to-monitor-normalized-rus-for-an-azure-cosmos-container-or-an-account"></a>Jak monitorovat normalizovaná RU/s pro kontejner Azure Cosmos nebo účet

Azure Monitor pro Azure Cosmos DB poskytuje zobrazení metrik pro monitorování vašeho účtu a vytváření řídicích panelů. Metriky Azure Cosmos DB jsou ve výchozím nastavení shromažďovány, takže tato funkce nevyžaduje explicitní povolení ani konfiguraci.

**Normalizovaná** metrika použití ru se používá k zjištění, jak dobře nasycené rozsahy klíčů oddílů jsou vzhledem k provozu. Azure Cosmos DB distribuuje propustnost rovnoměrně napříč všemi rozsahy klíčů oddílu. Tato metrika poskytuje za sekundu zobrazení maximálního využití propustnosti pro rozsah klíčů oddílu. Pomocí této metriky můžete vypočítat využití RU/s v rozsahu klíče oddílu pro daný kontejner. Pokud se vám tato metrika zobrazuje vysoké procento využití jednotek požadavků napříč všemi rozsahy klíčů oddílu ve službě Azure monitor, měli byste zvýšit propustnost, aby splňovala potřeby vašich úloh. Příklad – normalizované využití je definováno jako maximum využití RU/s ve všech rozsahech klíče oddílu. Předpokládejme například, že vaše maximální propustnost je 20 000 RU/s a že máte dva rozsahy klíčů oddílu P_1 a P_2, každý schopný škálování na 10 000 RU/s. Pokud v dané druhé P_1 používal 6000 ru a P_2 8000 ru, je normalizované využití MAX (6000 RU/10 000 RU, 8000 RU/10 000 RU) = 0,8.

## <a name="what-to-expect-and-do-when-normalized-rus-is-higher"></a>Co očekávat a když je normalizované RU/s vyšší

Když normalizovaná spotřeba RU/s dosáhne 100% pro daný rozsah klíčů oddílu a klient v tomto časovém intervalu (1 sekund) vydává požadavky na určitý rozsah klíčů oddílu, obdrží v něm chybu s omezeným počtem. Klient by měl respektovat navrhovanou dobu čekání a opakovat požadavek. Sada SDK usnadňuje zpracování této situace opakováním předem nakonfigurovaných časů tím, že se bude čekat správně.  Není nutné, abyste viděli chybu omezení míry RU, protože normalizované RU dosáhlo 100%. Vzhledem k tomu, že normalizované RU je jediná hodnota, která představuje maximální využití pro všechny rozsahy klíčů oddílu, může být jeden rozsah klíčů oddílu zaneprázdněný, ale ostatní rozsahy klíčů oddílu můžou požadavky zpracovat bez problémů. Například jedna operace, například uložená procedura, která spotřebovává všechny důležité/s na rozsah klíče oddílu, povede k krátkému nárůstu normalizované spotřeby RU/s. V takových případech nedojde k žádným okamžitému omezení rychlosti, pokud je rychlost požadavků nízká nebo se požadavky provedou na jiné oddíly v různých rozsahech klíčů oddílu. 

Metriky Azure Monitor vám pomůžou najít operace na stavový kód pro SQL API pomocí metriky **celkových požadavků** . Později můžete tyto požadavky filtrovat pomocí kódu stavu 429 a rozdělit je podle **typu operace**.  

Pokud chcete zjistit, které požadavky jsou omezené, doporučuje se tyto informace získat prostřednictvím diagnostických protokolů.

Pokud dochází k nepřetržité špičkě 100% normalizované spotřeby RU/s nebo blízko až 100% přes více rozsahů klíčů oddílu, doporučuje se zvýšit propustnost. Díky metrikám Azure monitor a diagnostickým protokolům Azure monitor můžete zjistit, které operace jsou těžké a jejich špičkové využití.

V souhrnu se za **normalizovanou** metriku s využitím ru používá k zobrazení toho, který rozsah klíčů oddílu je v souladu s využitím. Takže vám poskytne přehled o propustnosti směrem k rozsahu klíčů oddílu. Později můžete sledovat protokol **PartitionKeyRUConsumption** Azure monitor protokoly, abyste získali informace o tom, které klíče logického oddílu jsou z pohledu na používání aktivní. To bude ukazovat na změnu v možnosti klíč oddílu nebo na změnu v logice aplikace. Chcete-li vyřešit omezení přenosové rychlosti, rozdělte zatížení dat do několika oddílů nebo stačí zvýšit propustnost, jak je skutečně potřeba. 



## <a name="view-the-normalized-request-unit-consumption-metric"></a>Zobrazení normalizované metriky spotřeby jednotek požadavků

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

2. V levém navigačním panelu vyberte **monitor** a vyberte **metriky**.

   :::image type="content" source="./media/monitor-normalized-request-units/monitor-metrics-blade.png" alt-text="Podokno metrik v Azure Monitor":::

3. V podokně **metriky** > **Vyberte prostředek** > zvolte požadované **předplatné**a **skupinu prostředků**. Jako **typ prostředku**vyberte **Azure Cosmos DB účty**, zvolte jeden ze stávajících účtů Azure Cosmos a pak vyberte **použít**.

   :::image type="content" source="./media/monitor-normalized-request-units/select-cosmos-db-account.png" alt-text="Podokno metrik v Azure Monitor":::

4. Dále můžete vybrat metriku ze seznamu dostupných metrik. Můžete vybrat metriky specifické pro jednotky žádosti, úložiště, latenci, dostupnost, Cassandra a další. Podrobné informace o všech dostupných metrikách v tomto seznamu najdete v článku [metriky podle kategorií](monitor-cosmos-db-reference.md) . V tomto příkladu vybereme normalizovanou metriku s **využitím ru** a **Maximum** jako hodnotu agregace.

   Kromě těchto podrobností můžete také vybrat **časový rozsah** a **časovou členitost** metrik. V poli Max (maximum) si můžete zobrazit metriky za posledních 30 dní.  Po použití filtru se v závislosti na vašem filtru zobrazí graf.

   :::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-metric.png" alt-text="Podokno metrik v Azure Monitor":::

### <a name="filters-for-normalized-request-unit-consumption"></a>Filtry normalizované spotřeby jednotek požadavků

Můžete také filtrovat metriky a graf zobrazený podle konkrétního typu **CollectionName**, **DatabaseName**, **PartitionKeyRangeID**a **oblasti**. Pokud chcete metriky filtrovat, vyberte **Přidat filtr** a zvolte požadovanou vlastnost, jako je například **CollectionName** a odpovídající hodnota, které vás zajímají. Graf pak zobrazí normalizované jednotky pro spotřebu RU spotřebované pro kontejner pro vybrané období.  

Metriky můžete seskupit pomocí možnosti **použít rozdělení** .  

Normalizovaná metrika spotřeby jednotek žádostí pro každý kontejner se zobrazí, jak je znázorněno na následujícím obrázku:

:::image type="content" source="./media/monitor-normalized-request-units/normalized-request-unit-usage-filters.png" alt-text="Podokno metrik v Azure Monitor":::

## <a name="next-steps"></a>Další kroky

* Monitorujte Azure Cosmos DB data pomocí [nastavení diagnostiky](cosmosdb-monitor-resource-logs.md) v Azure.
* [Auditování operací roviny ovládacího prvku Azure Cosmos DB](audit-control-plane-logs.md)
