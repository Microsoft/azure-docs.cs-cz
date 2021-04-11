---
title: Vizualizace protokolů a metrik pomocí Power BI
description: Pomocí Power BI Vizualizujte protokoly a metriky Azure Kognitivní hledání.
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: fbbeb861e50abfd393b416ddc46ff147fffb7b8e
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581625"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>Vizualizace protokolů a metrik služby Azure Kognitivní hledání pomocí Power BI

[Azure kognitivní hledání](./search-what-is-azure-search.md) může odesílat protokoly operací a metriky služeb na účet Azure Storage, který pak můžete vizualizovat v Power BI. Tento článek vysvětluje postup a použití aplikace Power BI Template k vizualizaci dat. Šablona vám může pomáhat získat podrobné přehledy o vaší vyhledávací službě, včetně informací o dotazech, indexováních, operacích a metrikách služeb.

Můžete najít Power BI App template **kognitivní hledání Azure: Analýza protokolů a metrik** na [tržišti aplikací pro Power BI](https://appsource.microsoft.com/marketplace/apps).

## <a name="set-up-the-app"></a>Nastavení aplikace

1. Povolit protokolování metrik a prostředků pro vyhledávací službu:

    1. Umožňuje vytvořit nebo identifikovat existující [účet Azure Storage](../storage/common/storage-account-create.md) , do kterého můžete archivovat protokoly.
    1. Přejděte do služby Azure Kognitivní hledání v Azure Portal
    1. V části monitorování v levém sloupci vyberte **nastavení diagnostiky** .

        :::image type="content" source="media/search-monitor-logs-powerbi/diagnostic-settings.png" alt-text="Snímek obrazovky ukazující, jak vybrat nastavení diagnostiky v části Monitorování služby Azure Kognitivní hledání." border="false":::

    1. Vybrat **+ Přidat nastavení diagnostiky**
    1. Zaregistrujte se **do účtu úložiště**, zadejte informace o svém účtu úložiště a pak ověřte **OperationLogs** a **AllMetrics** .

        :::image type="content" source="media/search-monitor-logs-powerbi/add-diagnostic-setting.png" alt-text="Snímek obrazovky ukazující, jak provést výběry pro metriky a protokolování prostředků na stránce nastavení diagnostiky.":::
    1. Vyberte **Uložit**.

1. Po povolení protokolování můžete pomocí vyhledávací služby začít generovat protokoly a metriky. Může to trvat až hodinu, než se kontejnery zobrazí v úložišti objektů BLOB v těchto protokolech. Zobrazí se kontejner **Insights-logs-operationlogs** pro protokoly pro vyhledávání a kontejner Insights- **Metrics-pt1m** pro metriky.

1. Najděte aplikaci Azure Kognitivní hledání Power BI na [webu tržiště aplikací pro Power BI](https://appsource.microsoft.com/marketplace/apps) a nainstalujte ji do nového nebo existujícího pracovního prostoru. Aplikace se nazývá **Azure kognitivní hledání: Analýza protokolů a metrik**.

1. Po instalaci aplikace vyberte aplikaci ze seznamu aplikací v Power BI.

    :::image type="content" source="media/search-monitor-logs-powerbi/azure-search-app-tile.png" alt-text="Snímek obrazovky s aplikací Azure Kognitivní hledání, kterou chcete vybrat ze seznamu aplikací":::

1. Vyberte **připojit** a připojte data.

    :::image type="content" source="media/search-monitor-logs-powerbi/get-started-with-your-new-app.png" alt-text="Snímek obrazovky, který ukazuje, jak se připojit k datům v aplikaci Azure Kognitivní hledání.":::

1. Zadejte název účtu úložiště, který obsahuje vaše protokoly a metriky. Ve výchozím nastavení se aplikace bude považovat za posledních 10 dnů, ale tato hodnota se dá změnit s parametrem **dny** .

    :::image type="content" source="media/search-monitor-logs-powerbi/connect-to-storage-account.png" alt-text="Snímek obrazovky ukazující, jak zadat název účtu úložiště a počet dní k dotazování na stránce připojit k Azure Kognitivní hledání.":::

1. Jako metodu ověřování vyberte **klíč** a zadejte svůj klíč účtu úložiště. Jako úroveň ochrany osobních údajů vyberte **soukromá** . Klikněte na Přihlásit se a zahajte proces načítání.

    :::image type="content" source="media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png" alt-text="Snímek obrazovky ukazující, jak zadat metodu ověřování, klíč účtu a úroveň ochrany osobních údajů na stránce připojit k Azure Kognitivní hledání.":::

1. Počkejte, až se data aktualizují. To může nějakou dobu trvat v závislosti na tom, kolik dat máte. Můžete zjistit, zda se data stále aktualizují na základě níže uvedeného indikátoru.

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-refreshing.png" alt-text="Snímek obrazovky ukazující, jak číst informace na stránce pro aktualizaci dat":::

1. Po dokončení aktualizace dat vyberte **sestavu Azure kognitivní hledání** a zobrazte sestavu.

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-report.png" alt-text="Snímek obrazovky ukazující, jak vybrat sestavu Azure Kognitivní hledání na stránce aktualizace dat":::

1. Po otevření sestavy nezapomeňte stránku aktualizovat, aby se otevírala s daty.

    :::image type="content" source="media/search-monitor-logs-powerbi/powerbi-search.png" alt-text="Snímek obrazovky Power BI sestavy služby Azure Kognitivní hledání.":::

## <a name="modify-app-parameters"></a>Upravit parametry aplikace

Pokud chcete vizualizovat data z jiného účtu úložiště nebo změnit počet dnů, na které se má dotaz zadat dotaz, změňte parametry **dnů** a **StorageAccount** podle následujících kroků.

1. Přejděte do aplikací Power BI, Najděte aplikaci Azure Kognitivní hledání a kliknutím na tlačítko **Upravit aplikaci** zobrazíte pracovní prostor.

    :::image type="content" source="media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png" alt-text="Snímek obrazovky ukazující, jak vybrat tlačítko Upravit aplikaci pro aplikaci Azure Kognitivní hledání":::

1. V možnostech datové sady vyberte **Nastavení** .

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-settings.png" alt-text="Snímek obrazovky ukazující, jak vybrat nastavení z možností datové sady Azure Kognitivní hledání":::

1. Na kartě datové sady změňte hodnoty parametrů a vyberte **použít**. Pokud dojde k potížím s připojením, aktualizujte přihlašovací údaje ke zdroji dat na stejné stránce.

1. Přejděte zpátky do pracovního prostoru a vyberte **aktualizovat hned** z možností datové sady.

    :::image type="content" source="media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png" alt-text="Snímek obrazovky ukazující, jak vybrat aktualizaci z možností datové sady Azure Kognitivní hledání":::

1. Otevřete sestavu pro zobrazení aktualizovaných dat. Je také možné, že budete muset aktualizovat sestavu, aby se zobrazila nejnovější data.

## <a name="troubleshooting-report-issues"></a>Řešení potíží s hlášením

Pokud zjistíte, že se data nezobrazí, postupujte podle těchto kroků pro řešení potíží:

1. Otevřete sestavu a aktualizujte stránku, abyste měli jistotu, že si prohlížíte nejnovější data. V sestavě je možnost aktualizovat data. Tuto možnost vyberte, pokud chcete získat nejnovější data.

1. Ujistěte se, že zadaný název účtu úložiště a přístupový klíč jsou správné. Název účtu úložiště by měl odpovídat účtu nakonfigurovanému s protokoly služby Search.

1. Ujistěte se, že váš účet úložiště obsahuje kontejnery **Insights-logs-operationlogs** a **Insights-Metrics-pt1m** a každý kontejner obsahuje data. Protokoly a metriky budou v rámci několika vrstev složek.

1. Zkontrolujte, jestli se datová sada ještě neaktualizovala. Indikátor stavu aktualizace se zobrazí v kroku 8 výše. Pokud se stále aktualizuje, počkejte, až se aktualizace dokončí, aby se tato sestava otevřela a aktualizovala.

## <a name="next-steps"></a>Další kroky

+ [Monitorovat operace a aktivity hledání](search-monitor-usage.md)
+ [Co je Power BI?](/power-bi/fundamentals/power-bi-overview)
+ [Základní pojmy pro návrháře ve službě Power BI](/power-bi/service-basic-concepts)