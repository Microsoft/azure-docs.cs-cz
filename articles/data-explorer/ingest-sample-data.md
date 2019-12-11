---
title: Ingestování ukázkových dat do Azure Průzkumník dat
description: Přečtěte si, jak ingestovat (načítat) ukázková data související s počasí do Azure Průzkumník dat.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 08/12/2019
ms.openlocfilehash: 3ece5a9d225e48654a0a3a96c3b7b78327565841
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975172"
---
# <a name="quickstart-ingest-sample-data-into-azure-data-explorer"></a>Rychlý Start: ingestování ukázkových dat do Azure Průzkumník dat

V tomto článku se dozvíte, jak ingestovat (načíst) ukázková data do databáze Azure Průzkumník dat. Existuje [několik způsobů](ingest-data-overview.md), jak ingestovat data; Tento článek se zaměřuje na základní přístup, který je vhodný pro účely testování.

> [!NOTE]
> Tato data už máte, pokud jste dokončili ingestování [dat pomocí knihovny Python pro Azure Průzkumník dat](python-ingest-data.md).

## <a name="prerequisites"></a>Předpoklady

[Testovací cluster a databáze](create-cluster-database-portal.md)

## <a name="ingest-data"></a>Příjem dat

Ukázková datová sada **StormEvents** obsahuje data týkající se počasí od [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

1. Přihlaste se k webu [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. V levém horním rohu aplikace vyberte **Přidat cluster**.

1. V dialogovém okně **Přidat cluster** zadejte adresu URL clusteru ve formuláři `https://<ClusterName>.<Region>.kusto.windows.net/`a pak vyberte **Přidat**.

1. Vložte následující příkaz a vyberte **Spustit** pro vytvoření tabulky StormEvents.

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
    ```
1. Vložte následující příkaz a vyberte **Spustit** pro ingestování dat do tabulky StormEvents.

    ```Kusto
    .ingest into table StormEvents h'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (ignoreFirstRecord=true)
    ```

1. Po dokončení ingestování vložte následující dotaz, vyberte dotaz v okně a vyberte **Spustit**.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    Dotaz vrátí následující výsledky z přijatých ukázkových dat.

    ![Výsledky dotazu](media/ingest-sample-data/query-results.png)

## <a name="next-steps"></a>Další kroky

* Ingestování [dat z Azure Průzkumník dat](ingest-data-overview.md) , kde se dozvíte další informace o metodách přijímání.
* [Rychlý Start: dotazování dat v Azure Průzkumník dat](web-query-data.md) Webové uživatelské rozhraní.
* [Zápis dotazů](write-queries.md) pomocí dotazovacího jazyka Kusto
