---
title: Sledování využití propustnosti operace v Azure Cosmos DB
description: Zjistěte, jak sledovat využití propustnost nebo jednotky požadavku operace v Azure Cosmos DB. Vlastníci účtu Azure Cosmos DB můžete pochopit, které operace jsou s více jednotek požadavku.
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/09/2020
ms.openlocfilehash: bc39ef199a5d40d3eaa75023277d3e00b93e131a
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115427"
---
# <a name="how-to-monitor-throughput-or-request-unit-usage-of-an-operation-in-azure-cosmos-db"></a>Jak sledovat propustnost nebo vyžádání využití jednotky operace v Azure Cosmos DB

Azure Monitor pro Azure Cosmos DB poskytuje zobrazení metrik pro monitorování vašeho účtu a vytváření řídicích panelů. Metriky Azure Cosmos DB jsou shromažďovány ve výchozím nastavení, tato funkce nevyžaduje, abyste povolit nebo nakonfigurovat nic explicitně. Metrika **Celkový počet jednotek požadavků** se používá k získání využití jednotek požadavků pro různé typy operací. Později můžete analyzovat, které operace používaly většinu propustnost. Ve výchozím nastavení jsou data propustnosta agregována v intervalu jedné minuty. Jednotku agregace však můžete změnit změnou možnosti rozlišovací schopnosti času.

Existují dva způsoby, jak analyzovat data o využití jednotky požadavku:

* V daném časovém intervalu, které operace jsou s více jednotek požadavku.
* Které operace obecně dominují vaší pracovní zátěži tím, že spotřebovávají více jednotek požadavků.
Tato analýza umožňuje zaměřit se na operace, jako je vložení, upsert a podívejte se na jejich indexování. Můžete zjistit, zda jste nad nebo pod indexování konkrétní pole a upravit [zásady indexování](index-policy.md#include-exclude-paths) zahrnout nebo vyloučit cesty.

Pokud zjistíte, že některé dotazy provádějí další jednotky požadavků, můžete provést akce, jako jsou:

* Zvažte, zda požadujete správné množství dat.
* Upravte dotaz tak, aby používal index s klauzulí filtru.
* Proveďte levnější volání funkcí UDF.
* Definujte klíče oddílů, abyste minimalizovali ventilátor z dotazu do různých oddílů.
* Můžete také použít metriky dotazu vrácené v odpovědi na volání, podrobnosti diagnostického protokolu a odkazovat na článek [optimalizace výkonu dotazu,](sql-api-query-metrics.md) abyste se dozvěděli další informace o spuštění dotazu.
* Můžete začít od součtu a pak se podívat na využití avg pomocí správné dimenze.

## <a name="view-the-total-request-unit-usage-metric"></a>Zobrazení metriky využití jednotky celkového požadavku

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Na levém navigačním panelu vyberte **Sledovat** a vyberte **Metriky**.

   ![Podokno Metriky ve službě Azure Monitor](./media/monitor-request-unit-usage/monitor-metrics-blade.png)

1. V podokně **Metriky** > **Vyberte zdroj** > zvolte požadované **předplatné**a **skupinu prostředků**. Pro **typ prostředku**vyberte **účty Azure Cosmos DB**, vyberte jeden z vašich existujících účtů Azure Cosmos a vyberte **Použít**.

   ![Výběr účtu Azure Cosmos DB pro zobrazení metrik](./media/monitor-request-unit-usage/select-cosmos-db-account.png)

1. Dále vyberte metriku **Celkový počet jednotek požadavků** ze seznamu dostupných metrik. Podrobné informace o všech dostupných metrikách v tomto seznamu najdete v článku [Metriky podle kategorií.](monitor-cosmos-db-reference.md) V tomto příkladu vyberte jako hodnotu agregace **jednotky součtu a** **vach.** Kromě těchto podrobností můžete také vybrat **časový rozsah** a **rozlišovací** schopnost metrik. Při maximální mlze můžete zobrazit metriky za posledních 30 dní.  Po použití filtru se na základě filtru zobrazí graf. Můžete zobrazit průměrný počet jednotek požadavku spotřebovaných za minutu pro vybrané období.  

   ![Výběr metriky z webu Azure Portal](./media/monitor-request-unit-usage/request-unit-usage-metric.png)

## <a name="filters-for-request-unit-usage"></a>Filtry pro použití jednotky požadavku

Můžete také filtrovat metriky a získat grafy zobrazené podle konkrétního **CollectionName**, **DatabaseName**, **OperationType**, **Region**, **Status**a **StatusCode**. Možnosti **Přidat filtr** a **Použít rozdělení** umožňují filtrovat využití jednotky požadavku a seskupit metriky.

Chcete-li získat využití jednotky požadavku pro každou operaci buď součtem(součtem), nebo průměrem, vyberte **Použít rozdělení** a zvolte **Typ operace** a hodnotu filtru, jak je znázorněno na následujícím obrázku:

   ![Jednotky požadavků Cosmos DB pro operace v Azure monitoru](./media/monitor-request-unit-usage/request-unit-usage-operations.png)

Pokud chcete zobrazit využití jednotky požadavku podle kolekce, vyberte **Použít rozdělení** a zvolte název kolekce jako filtr. Uvidíte chat, jako je následující s výběrem kolekcí v řídicím panelu. Potom můžete vybrat konkrétní název kolekce a zobrazit další podrobnosti:

   ![Jednotky požadavků Cosmos DB pro všechny operace podle kolekce v Azure monitoru](./media/monitor-request-unit-usage/request-unit-usage-collection.png)

## <a name="next-steps"></a>Další kroky

* Monitorujte data Azure Cosmos DB pomocí [diagnostických nastavení](cosmosdb-monitor-resource-logs.md) v Azure.
* [Audit operací řídicí roviny služby Azure Cosmos DB](audit-control-plane-logs.md)