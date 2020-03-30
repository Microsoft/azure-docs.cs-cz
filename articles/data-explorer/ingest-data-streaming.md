---
title: Použití streamování ingestování ingestování dat do Azure Data Explorer
description: Přečtěte si, jak ingestovat (načíst) data do Azure Data Explorerpomocí streamování ingestování.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: d7d2bcf487c37fbb523b648d5aa4c572add5dfa9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297080"
---
# <a name="streaming-ingestion-preview"></a>Streamování (náhled)

Použití streamování ingestion, když budete potřebovat nízkou latenci s dobou ingestování méně než 10 sekund pro různá data svazku. Používá se k optimalizaci provozního zpracování mnoha tabulek v jedné nebo více databázích, kde je datový proud dat do každé tabulky relativně malý (málo záznamů za sekundu), ale celkový objem příjem dat je vysoký (tisíce záznamů za sekundu). 

Použití hromadné holčičí požití namísto streamování při množství dat roste na více než 1 MB za sekundu na tabulku. Přečtěte [si přehled při přijím dat,](/azure/data-explorer/ingest-data-overview) kde se dozvíte více o různých způsobech ingestování.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* Přihlaste se k [webovému uživatelskému uživatelskému uživatelskému uživatelskému](https://dataexplorer.azure.com/)
* [Vytvoření clusteru a databáze Průzkumníka dat Azure](create-cluster-database-portal.md)

## <a name="enable-streaming-ingestion-on-your-cluster"></a>Povolení streamování v clusteru

> [!WARNING]
> Před povolením požití v páře si prosím přečtěte [omezení.](#limitations)

1. Na webu Azure Portal přejděte do clusteru Azure Data Explorer. V **části Nastavení**vyberte Možnost **Konfigurace**. 
1. V podokně **Konfigurace** vyberte **Zapnuto,** chcete-li povolit **streamování .**
1. Vyberte **Uložit**.
 
    ![streamování na](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. V [uživatelském právu webu](https://dataexplorer.azure.com/)definujte [zásady streamování při příjmu](/azure/kusto/management/streamingingestionpolicy) dat v tabulkách nebo databázích, které budou přijímat streamovaná data. 

    > [!NOTE]
    > * Pokud je zásada definována na úrovni databáze, všechny tabulky v databázi jsou povoleny pro streamování ingestování.
    > * Použitá zásada může odkazovat pouze na nově ingemovaná data a nikoli na jiné tabulky v databázi.

## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>Použití streamování ingestování ingestovat data do clusteru

Existují dva podporované typy streamování ingestování:


* [**Centrum událostí**](/azure/data-explorer/ingest-data-event-hub), které se používá jako zdroj dat
* Vlastní ingestování vyžaduje, abyste **napsali aplikaci,** která používá jednu z klientských knihoven Průzkumníka dat Azure. Viz [ukázka streamování ingestování](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample) pro ukázkovou aplikaci.

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>Zvolte vhodný typ streamování při požití

|   |Centrum událostí  |Vlastní ingestování  |
|---------|---------|---------|
|Prodleva dat mezi zahájením požití a údaji dostupnými pro dotaz   |    Delší zpoždění     |   Kratší zpoždění      |
|Režie na vývoj    |   Rychlé a snadné nastavení, žádná vývojová režie    |   Vysoká režie vývoje pro aplikaci pro zpracování chyb a zajištění konzistence dat     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>Zakázání streamování v clusteru

> [!WARNING]
> Zakázání příjmu streamování může trvat několik hodin.

1. Přetáhnout [zásady streamování při ingestování](/azure/kusto/management/streamingingestionpolicy) ze všech relevantních tabulek a databází. Odstranění zásad streamování při přihlašování aktivuje přesun dat streamování z počátečního úložiště do trvalého úložiště v úložišti sloupců (rozsahy nebo úlomky). Přesun dat může trvat několik sekund až několik hodin, v závislosti na množství dat v počátečním úložišti a jak je procesor a paměť používán clusteru.
1. Na webu Azure Portal přejděte do clusteru Azure Data Explorer. V **části Nastavení**vyberte Možnost **Konfigurace**. 
1. V podokně **Konfigurace** vyberte **Možnost Vypnuto,** chcete-li **zakázat ingestování datových proudů**.
1. Vyberte **Uložit**.

    ![Streamování zingestování vypnuto](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>Omezení

* Streamování nepodporuje [kurzory databáze](/azure/kusto/management/databasecursor) nebo [mapování dat](/azure/kusto/management/mappings). Podporováno je pouze [předem vytvořené](/azure/kusto/management/create-ingestion-mapping-command) mapování dat. 
* Streamování výkonu a kapacity kapacity s větší velikosti virtuálních her a clusterů. Souběžné požití je omezeno na šest požití na jádro. Například pro 16 jádrových sku, jako jsou D14 a L16, maximální podporované zatížení je 96 souběžných ingestů. Pro dvě hlavní sku, jako je například D11, maximální podporované zatížení je 12 souběžných ingestování.
* Omezení velikosti dat na požadavek na požití je 4 MB.
* Aktualizace schématu, jako je například vytváření a úpravy tabulek a mapování ingestování, může trvat až pět minut pro službu ingestování streamování.
* Povolení streamování v clusteru, i když data nejsou ingestována prostřednictvím streamování, používá část místního disku SSD počítačů clusteru pro streamování dat a snižuje úložiště dostupné pro aktivní mezipaměť.
* [Značky rozsahu](/azure/kusto/management/extents-overview#extent-tagging) nelze nastavit na data streamování ingestování.

## <a name="next-steps"></a>Další kroky

* [Dotazovat se na data v Průzkumníku dat Azure](web-query-data.md)
