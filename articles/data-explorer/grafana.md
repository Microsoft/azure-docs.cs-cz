---
title: Vizualizace dat z Průzkumníka dat Azure pomocí Grafany
description: V tomto článku se naučíte nastavit Azure Data Explorer jako zdroj dat pro Grafana a pak vizualizovat data z ukázkového clusteru.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: a1c52007ea86ca0812c4a73a92ce81db6ddadc7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74038031"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Vizualizace dat z Průzkumníka dat Azure v Grafaně

Grafana je analytická platforma, která umožňuje dotazovat a vizualizovat data a pak vytvářet a sdílet řídicí panely na základě vizualizací. Grafana poskytuje *plugin*Průzkumníka dat Azure , který umožňuje připojení a vizualizaci dat z Průzkumníka dat Azure. V tomto článku se naučíte nastavit Azure Data Explorer jako zdroj dat pro Grafana a pak vizualizovat data z ukázkového clusteru.

Pomocí následujícího videa se dozvíte, jak používat modul plug-in Grafana Azure Data Explorer, nastavit Azure Data Explorer jako zdroj dat pro Grafana a pak vizualizovat data. 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

Případně můžete [nakonfigurovat zdroj dat](#configure-the-data-source) a [vizualizovat data,](#visualize-data) jak je podrobně popsáno v níže uvedeném článku.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku je třeba provést následující:

* [Grafana verze 5.3.0 nebo novější](https://docs.grafana.org/installation/) pro váš operační systém

* [Modul plug-in Průzkumník dat Azure](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) pro Grafana

* Cluster, který obsahuje ukázková data StormEvents. Další informace najdete [v tématu Úvodní příručka: Vytvoření clusteru A databáze Azure A databáze](create-cluster-database-portal.md) a [ukázková data ingestování do Průzkumníka dat Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

### <a name="specify-properties-and-test-the-connection"></a>Určení vlastností a testování připojení

S instanční přiřazený k roli *diváků,* nyní zadáte vlastnosti ve vaší instanci Grafana a otestovat připojení k Průzkumníku dat Azure.

1. V Grafaně v levém menu vyberte ikonu ozubeného kola a pak **Zdroje dat**.

    ![Zdroje dat](media/grafana/data-sources.png)

1. Vyberte **Přidat zdroj dat**.

1. Na stránce **Zdroje dat / Nový** zadejte název zdroje dat a vyberte typ zdroje dat **Průzkumníka Dat Azure**.

    ![Název a typ připojení](media/grafana/connection-name-type.png)

1. Zadejte název clusteru do formuláře https://{ClusterName}. {Region}.kusto.windows.net. Zadejte další hodnoty z portálu Azure nebo cli. Mapování naleznete v tabulce pod následujícím obrázkem.

    ![Vlastnosti připojení](media/grafana/connection-properties.png)

    | Grafické ui | portál Azure | Azure CLI |
    | --- | --- | --- |
    | ID předplatného | ID PŘEDPLATNÉHO | SubscriptionId |
    | Id klienta | ID adresáře | Nájemce |
    | ID klienta | ID aplikace | appId |
    | Tajný klíč klienta | Heslo | heslo |
    | | | |

1. Vyberte **Uložit & test**.

    Pokud je test úspěšný, přejděte k další části. Pokud narazíte na nějaké problémy, zkontrolujte hodnoty, které jste zadali v Grafana, a zkontrolujte předchozí kroky.

## <a name="visualize-data"></a>Vizualizace dat

Teď, když jste dokončili konfiguraci Azure Data Explorer jako zdroj dat pro Grafana, je čas vizualizovat data. Zde vám ukážeme základní příklad, ale můžete toho udělat mnohem víc. Doporučujeme podívat se na [zápis dotazů pro Azure Data Explorer](write-queries.md) příklady dalších dotazů ke spuštění proti ukázkové datové sady.

1. V Grafaně v levém menu vyberte ikonu plus a pak **řídicí panel**.

    ![Vytvořit řídicí panel](media/grafana/create-dashboard.png)

1. Na kartě **Přidat** vyberte **Graf**.

    ![Přidat graf](media/grafana/add-graph.png)

1. Na panelu grafu vyberte **Název panelu a** pak **Upravit**.

    ![Upravit panel](media/grafana/edit-panel.png)

1. V dolní části panelu vyberte **Zdroj dat a** vyberte zdroj dat, který jste nakonfigurovali.

    ![Výběr zdroje dat](media/grafana/select-data-source.png)

1. V podokně dotazů zkopírujte v následujícím dotazu a vyberte **spustit**. Dotaz segmenty počet událostí podle dne pro ukázkové datové sady.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![Spuštění dotazu](media/grafana/run-query.png)

1. Graf nezobrazuje žádné výsledky, protože je ve výchozím nastavení vymezen na data za posledních šest hodin. V horní nabídce vyberte **Posledních 6 hodin**.

    ![Posledních šest hodin](media/grafana/last-six-hours.png)

1. Zadejte vlastní rozsah, který pokrývá 2007, rok zahrnutý v naší ukázkové datové sadě StormEvents. Vyberte **Použít**.

    ![Vlastní rozsah dat](media/grafana/custom-date-range.png)

    Nyní graf ukazuje data z roku 2007, bucketed ve dne.

    ![Dokončený graf](media/grafana/finished-graph.png)

1. V horní nabídce vyberte ikonu uložit: ![Ikona Uložit](media/grafana/save-icon.png).

## <a name="create-alerts"></a>Vytvořit výstrahy

1. V domovském řídicím panelu vyberte **Možnost Upozorňovat** > **kanály oznámení** a vytvořte nový kanál oznámení.

    ![vytvořit kanál oznámení](media/grafana/create-notification-channel.png)

1. Vytvořte nový **kanál oznámení**a poté **uložte**.

    ![Vytvořit nový kanál oznámení](media/grafana/new-notification-channel-adx.png)

1. Na **řídicím panelu**vyberte v rozevíracím souboru možnost **Upravit.**

    ![vybrat úpravy v řídicím panelu](media/grafana/edit-panel-4-alert.png)

1. Výběrem ikony výstražného zvonku otevřete podokno **Výstrahy.** Vyberte **možnost Vytvořit výstrahu**. Vyplňte následující vlastnosti v podokně **Výstrahy.**

    ![vlastnosti výstrahy](media/grafana/alert-properties.png)

1. Chcete-li uložit změny, vyberte ikonu **Uložit řídicí panel.**

## <a name="next-steps"></a>Další kroky

* [Psaní dotazů pro Azure Data Explorer](write-queries.md)
