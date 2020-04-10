---
title: Jak sledovat latenci na straně serveru pro operace v Azure Cosmos DB
description: Zjistěte, jak monitorovat latenci serveru pro operace v účtu Azure Cosmos DB nebo v kontejneru. Vlastníci účtu Azure Cosmos DB můžou pochopit problémy s latencí na straně serveru s vašimi účty Azure Cosmos.
ms.service: cosmos-db
ms.topic: conceptual
author: kanshiG
ms.author: govindk
ms.date: 04/07/2020
ms.openlocfilehash: 62c10a2ada9ff7d3bf7090028dd9684192517d02
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991386"
---
# <a name="how-to-monitor-the-server-side-latency-for-operations-in-an-azure-cosmos-db-container-or-account"></a>Jak sledovat latenci na straně serveru pro operace v kontejneru nebo účtu Azure Cosmos DB

Azure Monitor pro Azure Cosmos DB poskytuje zobrazení metrik pro monitorování vašeho účtu a vytváření řídicích panelů. Metriky Azure Cosmos DB jsou shromažďovány ve výchozím nastavení, tato funkce nevyžaduje, abyste povolit nebo nakonfigurovat nic explicitně. Metrika latence na straně serveru se používá k zobrazení latence operace na straně serveru. Azure Cosmos DB poskytuje sla méně než 10 ms pro operace čtení a zápisu bodu s přímým připojením. Pro operace čtení a zápisu bodů jsou sla vypočteny podle podrobného dokumentu [sla](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/).

Pokud se u bodových operací zobrazí neobvykle velká latence, například:

* Operace získání nebo nastavení s klíčem oddílu a ID v přímém režimu
* Operace čtení nebo zápisu nebo
* Dotaz

Můžete vyhledat diagnostický protokol a zobrazit velikost vrácených dat. Pokud se zobrazí trvalé vysoké latence pro operace dotazu, můžete vyhledat diagnostický protokol pro velikost vrácených dat, [propustnost nebo RU/s](cosmosdb-monitor-resource-logs.md#diagnostic-queries) použité nebo počet těchto operací v daném období. Tímto způsobem můžete ladit problémy s latencí na straně serveru.

## <a name="view-server-side-latency-metric"></a>Zobrazit metriku latence na straně serveru

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Na levém navigačním panelu vyberte **Sledovat** a vyberte **Metriky**.

   ![Podokno Metriky ve službě Azure Monitor](./media/monitor-server-side-latency/monitor-metrics-blade.png)

1. V podokně **Metriky** > **Vyberte zdroj** > zvolte požadované **předplatné**a **skupinu prostředků**. Pro **typ prostředku**vyberte **účty Azure Cosmos DB**, vyberte jeden z vašich existujících účtů Azure Cosmos a vyberte **Použít**.
   
   ![Výběr účtu Cosmos DB pro zobrazení metrik](./media/monitor-server-side-latency/select-cosmos-db-account.png)

1. Dále vyberte metriku **latence na straně serveru** ze seznamu dostupných metrik. Podrobné informace o všech dostupných metrikách v tomto seznamu najdete v článku [Metriky podle kategorií.](monitor-cosmos-db-reference.md) V tomto příkladu vyberte **latenci na straně serveru** a **avg** jako hodnotu agregace. Kromě těchto podrobností můžete také vybrat **časový rozsah** a **rozlišovací** schopnost metrik. Při maximální mlze můžete zobrazit metriky za posledních 30 dní.  Po použití filtru se na základě filtru zobrazí graf. Můžete zobrazit průměrný počet jednotek požadavku spotřebovaných za minutu pro vybrané období.  

   ![Výběr metriky latence na straně serveru z webu Azure Portal](./media/monitor-server-side-latency/server-side-latency-metric.png)

## <a name="filters-for-server-side-latency"></a>Filtry pro latenci na straně serveru

Můžete také filtrovat metriky a graf zobrazený podle konkrétního **názvu CollectionName**, **ConnectionMode**, **DatabaseName**, **OperationType**, **Region**a **PublicAPIType**. 

Chcete-li metriky filtrovat, vyberte **možnost Přidat filtr** a zvolte požadovanou vlastnost, například **PublicAPIType,** a vyberte hodnotu **SQL**. Přidejte další filtr pro **OperationType**. Graf pak zobrazí latenci na straně serveru pro různé operace během vybraného období. Operace prováděné prostřednictvím uložené procedury nejsou protokolovány, takže nejsou k dispozici v rámci metriky OperationType.

Metriky **latence na straně serveru** pro každou operaci jsou zobrazeny tak, jak je znázorněno na následujícím obrázku:

![Filtry pro metriky latence na straně serveru](./media/monitor-server-side-latency/server-side-latency-filters.png)

Metriky můžete také seskupit pomocí možnosti **Použít rozdělení.**  

## <a name="next-steps"></a>Další kroky

* Monitorování dat Azure Cosmos DB pomocí [diagnostických nastavení](cosmosdb-monitor-resource-logs.md) v Azure
* [Audit operací řídicí roviny služby Azure Cosmos DB](audit-control-plane-logs.md)