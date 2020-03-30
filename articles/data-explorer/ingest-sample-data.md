---
title: Ingestování ukázkových dat do Průzkumníka dat Azure
description: Přečtěte si, jak ingestovat (načíst) ukázková data související s počasím do Průzkumníka dat Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 08/12/2019
ms.openlocfilehash: 3ece5a9d225e48654a0a3a96c3b7b78327565841
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "74975172"
---
# <a name="quickstart-ingest-sample-data-into-azure-data-explorer"></a>Úvodní příručka: Ingestování ukázkových dat do Průzkumníka dat Azure

Tento článek ukazuje, jak ingestovat (načíst) ukázková data do databáze Průzkumníka dat Azure. Existuje [několik způsobů, jak ingestovat data](ingest-data-overview.md); Tento článek se zaměřuje na základní přístup, který je vhodný pro účely testování.

> [!NOTE]
> Tato data už máte, pokud jste data [ingestování vyplnili pomocí knihovny Pythonu Průzkumníka dat Azure](python-ingest-data.md).

## <a name="prerequisites"></a>Požadavky

[Testovací cluster a databázi](create-cluster-database-portal.md)

## <a name="ingest-data"></a>Ingestace dat

Ukázková datová sada **StormEvents** obsahuje data týkající se počasí od [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

1. Přihlaste [https://dataexplorer.azure.com](https://dataexplorer.azure.com)se do .

1. V levém horním rohu aplikace vyberte **Přidat cluster**.

1. V dialogovém okně **Přidat cluster** zadejte `https://<ClusterName>.<Region>.kusto.windows.net/`adresu URL clusteru ve formuláři a vyberte **Přidat**.

1. Vložte do následujícího příkazu a vyberte **Spustit,** chcete-li vytvořit tabulku StormEvents.

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
    ```
1. Vložte do následujícího příkazu a vyberte **Spustit,** chcete-li ingestovat data do tabulky StormEvents.

    ```Kusto
    .ingest into table StormEvents h'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (ignoreFirstRecord=true)
    ```

1. Po dokončení požití vložte do následujícího dotazu, vyberte dotaz v okně a vyberte **Spustit**.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    Dotaz vrátí následující výsledky z ingestovaných ukázkových dat.

    ![Výsledky dotazu](media/ingest-sample-data/query-results.png)

## <a name="next-steps"></a>Další kroky

* [Přístup](ingest-data-overview.md) k dalším informacím o metodách ingestování v Azure Data Exploreru.
* [Úvodní příručka: Dotazujte se na data v Průzkumníku dat Azure](web-query-data.md) Webové uživatelské uj.
* [Napište dotazy](write-queries.md) pomocí kusto dotazovacího jazyka.
