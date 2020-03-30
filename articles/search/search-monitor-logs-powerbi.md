---
title: Vizualizace protokolů a metrik azure kognitivního vyhledávání pomocí Power BI
description: Vizualizace protokolů a metrik azure kognitivního vyhledávání pomocí Power BI
manager: eladz
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 618033e24236d26601fafaf7b0678ee83ec973e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650134"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>Vizualizace protokolů a metrik azure kognitivního vyhledávání pomocí Power BI
[Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-what-is-azure-search) umožňuje ukládat protokoly operací a metriky služeb o vaší vyhledávací službě v účtu Azure Storage. Tato stránka obsahuje pokyny, jak si můžete tyto informace vizualizovat prostřednictvím aplikace Šablony Power BI. Aplikace poskytuje podrobné informace o vyhledávací službě, včetně informací o metrikách Vyhledávání, Indexování, Provoz a Služba.

Najdete si aplikaci Power BI Template App **Azure Cognitive Search: Analyzovat protokoly a metriky** na [trhu aplikací Power BI](https://appsource.microsoft.com/marketplace/apps).

## <a name="how-to-get-started-with-the-app"></a>Jak začít s aplikací
1. Povolte protokolování diagnostiky pro vyhledávací službu:
    1. Vytvoření nebo identifikace existujícího [účtu Úložiště Azure,](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) kde můžete protokoly archivovat
    1. Přejděte na službu Azure Cognitive Search na webu Azure Portal
    1. V části Monitorování v levém sloupci vyberte **Nastavení diagnostiky.**

        ![](media/search-monitor-logs-powerbi/diagnostic-settings.png)

    1. Vybrat **+ Přidat diagnostické nastavení**
    1. Zkontrolujte **archivovat účet úložiště**, zadejte informace o účtu úložiště a zkontrolujte **OperationLogs** a **AllMetrics**

        ![](media/search-monitor-logs-powerbi/add-diagnostic-setting.png)
    1. Vyberte **Uložit**.

1. Po povolení protokolování můžete pomocí vyhledávací služby spustit generování protokolů a metrik. Trvá až hodinu, než se kontejnery zobrazí v úložišti objektů Blob s těmito protokoly. Zobrazí se kontejner **evidence-protokoly-operationlogs** pro protokoly vyhledávacího provozu a kontejner **insights-metrics-pt1m** pro metriky.

1. Najděte aplikaci Azure Cognitive Search Power BI na [trhu aplikací Power BI](https://appsource.microsoft.com/marketplace/apps) a nainstalujte ji do nového pracovního prostoru nebo do existujícího pracovního prostoru. Aplikace se nazývá **Azure Cognitive Search: Analyzovat protokoly a metriky**.

1. Po instalaci aplikace vyberte aplikaci ze seznamu aplikací v Power BI.

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile.png)

1. Vyberte **Připojit,** chcete-li připojit data.

    ![](media/search-monitor-logs-powerbi/get-started-with-your-new-app.png)

1. Zadejte název účtu úložiště, který obsahuje protokoly a metriky. Ve výchozím nastavení se aplikace bude zabývat daty za posledních 10 dní, ale tuto hodnotu lze změnit pomocí parametru **Dny.**

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account.png)

1. Jako metodu ověřování vyberte **Klíč** a zadejte klíč účtu úložiště. Jako úroveň ochrany osobních údajů vyberte **Soukromé.** Klikněte na Přihlásit se a zahajte proces načítání.

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png)

1. Počkejte na aktualizaci dat. To může nějakou dobu trvat v závislosti na tom, kolik dat máte. Můžete zjistit, zda jsou data stále aktualizována na základě níže uvedeného indikátoru.

    ![](media/search-monitor-logs-powerbi/workspace-view-refreshing.png)

1. Po dokončení aktualizace dat vyberte **Azure Cognitive Search Report** pro zobrazení sestavy.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-report.png)

1. Po otevření sestavy nezapomeňte stránku aktualizovat tak, aby se otevřela s vašimi daty.

    ![](media/search-monitor-logs-powerbi/powerbi-search.png)

## <a name="how-to-change-the-app-parameters"></a>Jak změnit parametry aplikace
Pokud chcete vizualizovat data z jiného účtu úložiště nebo změnit počet dní dat na dotaz, změňte parametry **Days** a **StorageAccount** pomocí následujících kroků.

1. Přejděte do svých aplikací Power BI, najděte aplikaci Azure Cognitive Search a vyberte tlačítko **Upravit aplikaci,** abyste si pracovní prostor zobrazili.

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png)

1. Z voleb Datové sady vyberte **Nastavení.**

    ![](media/search-monitor-logs-powerbi/workspace-view-select-settings.png)

1. Na kartě Datové sady změňte hodnoty parametrů a vyberte **Použít**. Pokud dojde k problému s připojením, aktualizujte přihlašovací údaje zdroje dat na stejné stránce.

1. Přejděte zpět do pracovního prostoru a z možností Datové sady **vyberte Aktualizovat nyní.**

    ![](media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png)

1. Otevřete sestavu a zobrazte aktualizovaná data. Chcete-li zobrazit nejnovější data, může být také nutné sestavu aktualizovat.

## <a name="troubleshooting"></a>Řešení potíží
Pokud zjistíte, že vaše data nevidíte, postupujte takto:

1. Otevřete sestavu a aktualizujte stránku, abyste měli jistotu, že si prohlížíte nejnovější data. V sestavě je možnost aktualizovat data. Tuto možnost vyberte, chcete-li získat nejnovější data.

1. Ujistěte se, že název účtu úložiště a přístupový klíč, který jste zadali, jsou správné. Název účtu úložiště by měl odpovídat účtu nakonfigurovanému pomocí protokolů vyhledávací služby.

1. Zkontrolujte, zda váš účet úložiště obsahuje kontejnery **přehledy-protokoly-operationlogs** a **insights-metrics-pt1m** a každý kontejner má data. Protokoly a metriky budou v několika vrstvách složek.

1. Zkontrolujte, zda je datová sada stále osvěžující. Indikátor stavu aktualizace je zobrazen v kroku 8 výše. Pokud se stále aktualizuje, počkejte na dokončení aktualizace, chcete-li sestavu otevřít a aktualizovat.

## <a name="next-steps"></a>Další kroky
[Další informace o Azure Cognitive Search](https://docs.microsoft.com/azure/search/)

[Co je Power BI?](https://docs.microsoft.com/power-bi/fundamentals/power-bi-overview)

[Základní pojmy pro návrháře služby Power BI](https://docs.microsoft.com/power-bi/service-basic-concepts)