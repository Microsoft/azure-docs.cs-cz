---
title: Jak monitorovat latenci na straně serveru pro operace v Azure Cosmos DB
description: Naučte se monitorovat latenci serveru pro operace v Azure Cosmos DBm účtu nebo kontejneru. Vlastníci Azure Cosmos DB účtu můžou pochopit problémy latence na straně serveru s vašimi účty Azure Cosmos.
ms.service: cosmos-db
ms.topic: how-to
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: 5be2365fb5850c3f45b320d66c114fb791b22c3e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85262697"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>Jak monitorovat latenci na straně serveru pro operace v Azure Cosmos DBovém kontejneru nebo účtu

Azure Monitor pro Azure Cosmos DB poskytuje zobrazení metrik pro monitorování vašeho účtu a vytváření řídicích panelů. Metriky Azure Cosmos DB jsou ve výchozím nastavení shromažďovány, takže tato funkce nevyžaduje explicitní povolení ani konfiguraci. Metrika latence na straně serveru se používá k zobrazení latence na straně serveru v rámci operace. Azure Cosmos DB poskytuje SLA méně než 10 MS pro operace čtení a zápisu s přímým připojením. V případě operací čtení a zápisu bodů se SLA vypočte jako podrobná v [dokumentu SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/).

Pokud vidíte neobvykle velkou latenci při operacích s bodem, jako je například:

* Operace Get nebo set s klíčem oddílu a ID v přímém režimu
* Operace čtení nebo zápisu nebo
* Dotaz

Můžete vyhledat diagnostický protokol a zobrazit velikost vrácených dat. Pokud se vám při operacích s dotazem zobrazuje nepřetržitá vysoká latence, můžete vyhledat diagnostický protokol pro velikost vrácených dat, [propustnosti nebo ru/s](cosmosdb-monitor-resource-logs.md#diagnostic-queries) nebo počet takových operací v daném období. Tímto způsobem můžete ladit problémy s latencí na straně serveru.

## <a name="view-the-server-side-latency-metric"></a>Zobrazit metriku latence na straně serveru

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

1. V levém navigačním panelu vyberte **monitor** a vyberte **metriky**.

   :::image type="content" source="./media/monitor-server-side-latency/monitor-metrics-blade.png" alt-text="Podokno metrik v Azure Monitor":::

1. V podokně **metriky** > **Vyberte prostředek** > zvolte požadované **předplatné**a **skupinu prostředků**. Jako **typ prostředku**vyberte **Azure Cosmos DB účty**, zvolte jeden ze stávajících účtů Azure Cosmos a pak vyberte **použít**.
   
   :::image type="content" source="./media/monitor-server-side-latency/select-cosmos-db-account.png" alt-text="Vyberte účet Azure Cosmos DB, pro který chcete zobrazit metriky.":::

1. Dále vyberte metriku **latence na straně serveru** ze seznamu dostupných metrik. Podrobné informace o všech dostupných metrikách v tomto seznamu najdete v článku [metriky podle kategorií](monitor-cosmos-db-reference.md) . V tomto příkladu vybereme možnost **latence na straně serveru** a **Průměrná** hodnota pro agregaci. Kromě těchto podrobností můžete také vybrat **časový rozsah** a **časovou členitost** metrik. V poli Max (maximum) si můžete zobrazit metriky za posledních 30 dní.  Po použití filtru se v závislosti na vašem filtru zobrazí graf. Pro vybrané období můžete zobrazit latenci na straně serveru za minutu.  

   :::image type="content" source="./media/monitor-server-side-latency/server-side-latency-metric.png" alt-text="Vyberte metriku latence na straně serveru z Azure Portal":::

## <a name="filters-for-server-side-latency"></a>Filtry pro latenci na straně serveru

Můžete také vyfiltrovat metriky a získat grafy zobrazené v konkrétním typu **CollectionName**, **ConnectionMode**, **DatabaseName**, **typem operace OperationType**, **region**a **PublicAPIType**. 

Chcete-li filtrovat metriky, vyberte možnost **Přidat filtr** a zvolte požadovanou vlastnost, například **PublicAPIType** a vyberte hodnotu **SQL**. Přidejte další filtr pro **typem operace OperationType**. Graf pak zobrazí latenci na straně serveru pro různé operace během vybraného období. Operace provedené prostřednictvím uložené procedury nejsou protokolovány, takže nejsou k dispozici v rámci metriky typem operace OperationType.

Metrika **latence na straně serveru** pro každou operaci se zobrazí, jak je znázorněno na následujícím obrázku:

:::image type="content" source="./media/monitor-server-side-latency/server-side-latency-filters.png" alt-text="Filtry pro metriky latence na straně serveru":::

Metriky můžete také seskupit pomocí možnosti **použít rozdělení** .  

## <a name="next-steps"></a>Další kroky

* Monitorujte Azure Cosmos DB data pomocí [nastavení diagnostiky](cosmosdb-monitor-resource-logs.md) v Azure.
* [Auditování operací roviny ovládacího prvku Azure Cosmos DB](audit-control-plane-logs.md)
