---
title: Použití ingestování streamování k ingestování dat do Azure Průzkumník dat
description: Přečtěte si, jak ingestovat (načíst) data do Azure Průzkumník dat pomocí ingestování streamování.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: b99827ff9fd01c7179a25fafd05bfc8e4ef8ae63
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921092"
---
# <a name="streaming-ingestion-preview"></a>Ingestování streamování (Preview)

Ingestování streamování je cílené pro scénáře, které vyžadují nízkou latenci a dobu příjmu kratší než 10 sekund pro různá data svazků. Používá se k optimalizaci provozního zpracování mnoha tabulek v jedné nebo více databázích, kde datový proud dat do každé tabulky je relativně malý (několik záznamů za sekundu), ale celkový objem příjmu dat je vysoký (tisíce záznamů za sekundu).

Použijte klasickou (hromadnou) ingestu místo příjmu streamování, když se objem dat zvětšuje na více než 1 MB za sekundu na tabulku. Přečtěte si [Přehled příjmu dat](/azure/data-explorer/ingest-data-overview) , kde se dozvíte víc o různých metodách přijímání.

## <a name="prerequisites"></a>Předpoklady

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.
* Přihlaste se k [webovému uživatelskému rozhraní](https://dataexplorer.azure.com/).
* Vytvoření [clusteru a databáze Azure Průzkumník dat](create-cluster-database-portal.md)

## <a name="enable-streaming-ingestion-on-your-cluster"></a>Povolit přijímání streamování v clusteru

> [!WARNING]
> Zkontrolujte prosím [omezení](#limitations) před tím, než povolíte přijímání parou.

1. V Azure Portal přejdete do svého clusteru Azure Průzkumník dat. V **Nastavení**vyberte **Konfigurace**. 
1. V podokně **Konfigurace** vyberte **zapnuto** , aby se povolilo **přijímání streamování**.
1. Vyberte **Save** (Uložit).
 
    ![přijímání příjmu streamování](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. V [uživatelském rozhraní Web](https://dataexplorer.azure.com/)definujte [zásady přijímání streamování](/azure/kusto/concepts/streamingingestionpolicy) pro tabulky nebo databáze, které budou přijímat streamovaná data. 

    > [!NOTE]
    > * Pokud je zásada definovaná na úrovni databáze, všechny tabulky v databázi mají povolený příjem dat do streamování.
    > * Použitá zásada může odkazovat pouze na nově ingestovaná data a nikoli na jiné tabulky v databázi.

## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>Použití ingestování streamování k ingestování dat do clusteru

Existují dva podporované typy přijímání streamování:


* [**Centrum událostí**](/azure/data-explorer/ingest-data-event-hub) používané jako zdroj dat
* **Vlastní** ingestování vyžaduje, abyste napsali aplikaci, která používá jednu z klientských knihoven Azure Průzkumník dat. Ukázková aplikace najdete v ukázce ingestování [streamování](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample) .

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>Zvolit vhodný typ příjmu streamování

|   |Centrum událostí  |Vlastní ingestování  |
|---------|---------|---------|
|Zpoždění dat mezi zahájením příjmu a daty dostupnými pro dotaz   |    delší zpoždění     |   kratší zpoždění      |
|Režie vývoje    |   rychlé a snadné nastavení bez režie vývoje    |   vysoce vyvinutá režie pro aplikace pro zpracování chyb a zajištění konzistence dat     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>Zakázat ingestování streamování v clusteru

> [!WARNING]
> Zakazování příjmu streamování může trvat několik hodin.

1. Přetáhněte [zásadu přijímání streamování](/azure/kusto/concepts/streamingingestionpolicy) ze všech relevantních tabulek a databází. Odebrání zásady přijímání do streamování spustí přenos dat příjmu streamování z počátečního úložiště do trvalého úložiště v úložišti sloupců (rozsahy nebo horizontálních oddílů). Pohyb dat může trvat během několika sekund po několik hodin, v závislosti na množství dat v počátečním úložišti a způsobu, jakým cluster používá a paměť.
1. V Azure Portal přejdete do svého clusteru Azure Průzkumník dat. V **Nastavení**vyberte **Konfigurace**. 
1. V podokně **Konfigurace** vyberte **vypnuto** a zakažte příjem **vysílání datového proudu**.
1. Vyberte **Save** (Uložit).

    ![Ingestování streamování vypnuto](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>Omezení

* Zvýšení výkonu a kapacity příjmu streamování díky větší velikosti virtuálních počítačů a clusterů. Souběžné přijímání je omezené na šest ingest na jádro. Například u 16 základních SKU, jako je například D14 a L16, je maximální podporované zatížení 96 souběžných ingestování. U dvou základních SKU, jako je D11, je maximální podporované zatížení 12 souběžných ingest.
* Omezení velikosti dat na žádost o přijetí dat je 4 MB.
* Aktualizace schématu, jako je vytváření a úprava tabulek a mapování přijímání, můžou trvat až 5 minut, než se služba pro příjem dat streamuje.
* Povolení ingestování streamování na clusteru, i když se data nedrží prostřednictvím streamování, používá část místního disku SSD clusterových počítačů pro data příjmu streamování a snižuje úložiště dostupné pro hotkou mezipaměť.
* U dat příjmu streamování se nedají nastavit [značky rozsahu](/azure/kusto/management/extents-overview#extent-tagging) .

Přijímání streamování nepodporuje následující funkce:
* [Kurzory databáze](/azure/kusto/management/databasecursor).
* [Mapování dat](/azure/kusto/management/mappings). Je podporováno pouze [předem vytvořené](/azure/kusto/management/create-ingestion-mapping-command) mapování dat. 

## <a name="next-steps"></a>Další kroky

* [Dotazování na data v Azure Průzkumník dat](web-query-data.md)
