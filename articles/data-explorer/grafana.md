---
title: Vizualizace dat z Azure Průzkumník dat pomocí Grafana
description: V tomto článku se naučíte, jak nastavit službu Azure Průzkumník dat jako zdroj dat pro Grafana a pak vizualizovat data z ukázkového clusteru.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: a1c52007ea86ca0812c4a73a92ce81db6ddadc7b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038031"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Vizualizace dat z Azure Průzkumník dat v Grafana

Grafana je analytická platforma, která umožňuje dotazování a vizualizaci dat a následné vytváření a sdílení řídicích panelů na základě vizualizací. Grafana poskytuje *modul plug-in*Azure Průzkumník dat, který umožňuje připojení a vizualizaci dat z Azure Průzkumník dat. V tomto článku se naučíte, jak nastavit službu Azure Průzkumník dat jako zdroj dat pro Grafana a pak vizualizovat data z ukázkového clusteru.

Pomocí následujícího videa se naučíte používat modul plug-in Azure Průzkumník dat v Grafana, nastavit Azure Průzkumník dat jako zdroj dat pro Grafana a pak vizualizovat data. 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

Případně můžete [nakonfigurovat zdroj dat](#configure-the-data-source) a [vizualizovat data](#visualize-data) podle podrobných pokynů v následujícím článku.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete následující:

* [Grafana verze 5.3.0 nebo novější](https://docs.grafana.org/installation/) pro váš operační systém

* [Modul plug-in Azure Průzkumník dat](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) pro Grafana

* Cluster, který obsahuje ukázková data StormEvents. Další informace najdete v tématu [rychlý Start: Vytvoření clusteru azure Průzkumník dat a databáze](create-cluster-database-portal.md) a ingestování [ukázkových dat do Azure Průzkumník dat](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

### <a name="specify-properties-and-test-the-connection"></a>Zadejte vlastnosti a otestujte připojení.

S instančním objektem přiřazeným k roli *Návštěvníci* teď v instanci Grafana zadáte vlastnosti a otestujete připojení k Azure Průzkumník dat.

1. V Grafana v nabídce vlevo vyberte ikonu ozubeného kolečka a **zdroje dat**.

    ![Zdroje dat](media/grafana/data-sources.png)

1. Vyberte **Přidat zdroj dat**.

1. Na stránce **zdroje dat/nová** zadejte název zdroje dat a potom vyberte typ **zdroje dat Azure Průzkumník dat**.

    ![Název a typ připojení](media/grafana/connection-name-type.png)

1. Do formuláře https://{název_clusteru} zadejte název clusteru. {Region}. kusto. Windows. NET. Zadejte další hodnoty z Azure Portal nebo CLI. Mapování najdete v tabulce pod následujícím obrázkem.

    ![Vlastnosti připojení](media/grafana/connection-properties.png)

    | Uživatelské rozhraní Grafana | portál Azure | Azure CLI |
    | --- | --- | --- |
    | ID předplatného | ID PŘEDPLATNÉHO | SubscriptionId |
    | ID tenanta | ID adresáře | tenant |
    | ID klienta | ID aplikace | appId |
    | Tajný klíč klienta | Heslo | heslo |
    | | | |

1. Vyberte **uložit & test**.

    Pokud je test úspěšný, pokračujte k další části. Pokud přecházíte mezi všemi problémy, zkontrolujte hodnoty zadané v Grafana a Projděte si předchozí kroky.

## <a name="visualize-data"></a>Vizualizace dat

Nyní jste dokončili konfiguraci služby Azure Průzkumník dat jako zdroje dat pro Grafana, je čas na vizualizaci dat. Ukážeme si základní příklad, který vám může udělat spoustu dalšího. Doporučujeme, abyste si vyhledali [dotazy na zápis pro Azure Průzkumník dat](write-queries.md) příklady dalších dotazů, které se mají spustit na vzorové datové sadě.

1. V Grafana nabídce vlevo vyberte ikonu Plus a potom **řídicí panel**.

    ![Vytvořit řídicí panel](media/grafana/create-dashboard.png)

1. Na kartě **Přidat** vyberte **graf**.

    ![Přidat graf](media/grafana/add-graph.png)

1. Na panelu graf vyberte **název panelu** a pak **Upravit**.

    ![Panel pro úpravy](media/grafana/edit-panel.png)

1. V dolní části panelu vyberte **zdroj dat** a pak vyberte zdroj dat, který jste nakonfigurovali.

    ![Výběr zdroje dat](media/grafana/select-data-source.png)

1. V podokně dotazu zkopírujte následující dotaz a pak vyberte **Spustit**. Dotaz sestaví počet událostí podle dne pro sadu vzorových dat.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![Spuštění dotazu](media/grafana/run-query.png)

1. Graf nezobrazuje žádné výsledky, protože je ve výchozím nastavení vymezený na data za posledních 6 hodin. V horní nabídce vyberte **Poslední 6 hodin**.

    ![Posledních 6 hodin](media/grafana/last-six-hours.png)

1. Zadejte vlastní rozsah, který pokrývá 2007, rok zahrnutý v naší ukázkové sadě dat StormEvents. Vyberte **Použít**.

    ![Vlastní rozsah kalendářních dat](media/grafana/custom-date-range.png)

    Nyní se v grafu zobrazí data z 2007, která jsou rozdělená podle dnů.

    ![Graf dokončen](media/grafana/finished-graph.png)

1. V horní nabídce vyberte ikonu Uložit: ![Ikona Uložit](media/grafana/save-icon.png).

## <a name="create-alerts"></a>Vytváření výstrah

1. V řídicím panelu Domů vyberte **upozorňování** > **kanály oznámení** pro vytvoření nového kanálu oznámení.

    ![Vytvoření kanálu oznámení](media/grafana/create-notification-channel.png)

1. Vytvořte nový **kanál oznámení**a pak ho **uložte**.

    ![Vytvořit nový kanál oznámení](media/grafana/new-notification-channel-adx.png)

1. Na **řídicím panelu**vyberte v rozevíracím seznamu možnost **Upravit** .

    ![vybrat upravit na řídicím panelu](media/grafana/edit-panel-4-alert.png)

1. Vyberte ikonu zvonku výstrahy a otevřete tak podokno **výstrah** . Vyberte **vytvořit výstrahu**. V podokně **Výstraha** vyplňte následující vlastnosti.

    ![vlastnosti výstrahy](media/grafana/alert-properties.png)

1. Kliknutím na ikonu **Uložit řídicí panel** uložte změny.

## <a name="next-steps"></a>Další kroky

* [Psaní dotazů pro Azure Data Explorer](write-queries.md)
