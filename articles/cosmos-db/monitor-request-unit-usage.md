---
title: Monitorování využití propustnosti operace v Azure Cosmos DB
description: Naučte se monitorovat propustnost nebo využití jednotek žádostí operace v Azure Cosmos DB. Vlastníci Azure Cosmos DB účtu můžou pochopit, které operace pobírají víc jednotek žádostí.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 04/09/2020
ms.openlocfilehash: d6f0f30d7437b6f8ecc1d915eb3d3195f2504fec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93098242"
---
# <a name="how-to-monitor-throughput-or-request-unit-usage-of-an-operation-in-azure-cosmos-db"></a>Jak monitorovat propustnost nebo využití jednotek žádosti operace v Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Monitor pro Azure Cosmos DB poskytuje zobrazení metrik pro monitorování vašeho účtu a vytváření řídicích panelů. Metriky Azure Cosmos DB jsou ve výchozím nastavení shromažďovány, takže tato funkce nevyžaduje explicitní povolení ani konfiguraci. Metrika **celkových jednotek žádostí** se používá k získání využití jednotek žádosti pro různé typy operací. Později můžete analyzovat, které operace používaly většinu propustnosti. Ve výchozím nastavení se data propustnosti agreguje v intervalu 1 minuty. Agregační jednotku však můžete změnit změnou možnosti časové rozlišení.

Existují dva způsoby, jak analyzovat data o využití jednotek žádostí:

* V daném časovém intervalu, ve kterém operace pobírají více jednotek žádosti.
* Které operace obecně rozbírají vaše zatížení díky využívání dalších jednotek žádostí.
Tato analýza vám umožní soustředit se na operace, jako je například vložení, Upsert a pohled na jejich indexování. Můžete zjistit, jestli jste převzali nebo nahlásili určitá pole, a upravit [zásadu indexování](index-policy.md#include-exclude-paths) tak, aby zahrnovala nebo vyloučila cesty.

Pokud si všimnete, že některé dotazy pobírají více jednotek žádostí, můžete provést následující akce:

* Pokud požadujete správné množství dat, převezměte ho.
* Upravte dotaz tak, aby používal index s klauzulí Filter.
* Proveďte levnější volání funkcí systému souborů UDF.
* Definujte klíče oddílů pro minimalizaci ventilátoru mimo dotaz do různých oddílů.
* Můžete také použít metriky dotazů, které jsou vráceny v odpovědi na volání, podrobnosti diagnostického protokolu a v článku [ladění výkonu dotazů](sql-api-query-metrics.md) , kde se dozvíte další informace o spuštění dotazu.
* Můžete začít ze součtu a pak se podívat na průměrné využití pomocí správné dimenze.

## <a name="view-the-total-request-unit-usage-metric"></a>Zobrazení celkové metriky využití jednotky žádosti

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. V levém navigačním panelu vyberte **monitor** a vyberte **metriky**.

   :::image type="content" source="./media/monitor-request-unit-usage/monitor-metrics-blade.png" alt-text="Podokno metrik v Azure Monitor":::

1. V podokně **metriky** > **Vyberte prostředek** > zvolte požadované **předplatné** a **skupinu prostředků**. Jako **typ prostředku** vyberte **Azure Cosmos DB účty**, zvolte jeden ze stávajících účtů Azure Cosmos a pak vyberte **použít**.

   :::image type="content" source="./media/monitor-request-unit-usage/select-cosmos-db-account.png" alt-text="Vyberte účet Azure Cosmos DB, pro který chcete zobrazit metriky.":::

1. Potom v seznamu dostupných metrik vyberte položku Celková metrika **jednotek požadavků** . Podrobné informace o všech dostupných metrikách v tomto seznamu najdete v článku [metriky podle kategorií](monitor-cosmos-db-reference.md) . V tomto příkladu vybereme možnost **Celkový počet jednotek žádostí** a **průměr** jako hodnotu agregace. Kromě těchto podrobností můžete také vybrat **časový rozsah** a **časovou členitost** metrik. V poli Max (maximum) si můžete zobrazit metriky za posledních 30 dní.  Po použití filtru se v závislosti na vašem filtru zobrazí graf. Pro vybrané období můžete zobrazit průměrný počet spotřebovaných jednotek žádostí za minutu.  

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-metric.png" alt-text="Vyberte metriku z Azure Portal":::

## <a name="filters-for-request-unit-usage"></a>Filtry pro využití jednotky požadavku

Můžete také filtrovat metriky a získat grafy zobrazené konkrétním názvem **CollectionName**, **DatabaseName**, **typem operace OperationType**, **oblastí**, **stavem** a **StatusCode**. Možnosti **Přidat filtr** a **použít rozdělení** umožňují filtrovat využití jednotek požadavků a seskupit metriky.

Pokud chcete získat využití jednotky žádosti každé operace buď podle součtu (Sum), nebo průměrně, vyberte **použít rozdělení** a zvolte **typ operace** a hodnotu filtru, jak je znázorněno na následujícím obrázku:

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-operations.png" alt-text="Cosmos DB jednotky žádostí o operace ve službě Azure monitor":::

Pokud chcete zobrazit využití jednotky žádosti podle kolekce, vyberte **použít rozdělení** a jako filtr zvolte název kolekce. V rámci řídicího panelu se zobrazí chat podobný tomuto: s výběrem kolekce. Pak můžete vybrat konkrétní název kolekce a zobrazit další podrobnosti:

   :::image type="content" source="./media/monitor-request-unit-usage/request-unit-usage-collection.png" alt-text="Cosmos DB jednotky žádostí pro všechny operace podle kolekce ve službě Azure monitor":::

## <a name="next-steps"></a>Další kroky

* Monitorujte Azure Cosmos DB data pomocí [nastavení diagnostiky](cosmosdb-monitor-resource-logs.md) v Azure.
* [Auditování operací roviny ovládacího prvku Azure Cosmos DB](audit-control-plane-logs.md)
