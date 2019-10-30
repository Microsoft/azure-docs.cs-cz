---
title: Modelování dat v Azure Time Series Insights Preview | Microsoft Docs
description: Pochopení modelování dat v Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/29/2019
ms.custom: seodec18
ms.openlocfilehash: 81c2c2af78f5f066e1b27e14fa774df04d7c5868
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063988"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modelování dat v Azure Time Series Insights Preview

Tento článek popisuje, jak pracovat s modelem časových řad ve službě Azure Time Series Insights Preview. Podrobně popisuje několik běžných scénářů dat.

Pokud se chcete dozvědět víc o použití aktualizace, přečtěte si téma [Azure Time Series Insights Průzkumník Preview](./time-series-insights-update-explorer.md).

## <a name="types"></a>Druhy

### <a name="create-a-single-type"></a>Vytvoření jednoho typu

1. Přejděte na panel selektor modelu časové řady a v nabídce vyberte **typy** . Sbalením panelu Zaměřte se na typy modelu časové řady.

    [podokno ![ch typů](media/v2-update-how-to-tsm/portal-one.png)](media/v2-update-how-to-tsm/portal-one.png#lightbox)

1. Vyberte **+ Přidat**.
1. Zadejte všechny podrobnosti, které se týkají typů, a vyberte **vytvořit**. Tato akce vytvoří typy v prostředí.

    [Výběry ![pro přidání typu](media/v2-update-how-to-tsm/portal-two.png)](media/v2-update-how-to-tsm/portal-two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Hromadné nahrání jednoho nebo více typů

1. Vyberte **nahrát JSON**.
1. Vyberte soubor, který obsahuje datovou část typu.
1. Vyberte **Nahrát**.

    [Výběry ![hromadné nahrávání jednoho nebo více typů](media/v2-update-how-to-tsm/portal-three.png)](media/v2-update-how-to-tsm/portal-three.png#lightbox)

### <a name="edit-a-single-type"></a>Upravit jeden typ

1. Vyberte typ a vyberte **Upravit**. 
1. Proveďte požadované změny a vyberte **Uložit**.

    [Výběry ![pro úpravu typu](media/v2-update-how-to-tsm/portal-four.png)](media/v2-update-how-to-tsm/portal-four.png#lightbox)

### <a name="delete-a-type"></a>Odstranění typu

1. Vyberte typ a vyberte **Odstranit**.
1. Pokud nejsou k těmto typům přidruženy žádné instance, odstraní se.

    [![tlačítko "odstranit"](media/v2-update-how-to-tsm/portal-five.png)](media/v2-update-how-to-tsm/portal-five.png#lightbox)

## <a name="hierarchies"></a>Hierarchie

### <a name="create-a-single-hierarchy"></a>Vytvořit jednu hierarchii

1. Přejděte na panel selektor modelu časové řady a v nabídce vyberte možnost **hierarchie** . Sbalením panelu Zaměřte se na hierarchie modelu časové řady.

    [![podokně hierarchie](media/v2-update-how-to-tsm/portal-six.png)](media/v2-update-how-to-tsm/portal-six.png#lightbox)

1. Vyberte **+ Přidat**.

    [![tlačítko Přidat](media/v2-update-how-to-tsm/portal-seven.png)](media/v2-update-how-to-tsm/portal-seven.png#lightbox)

1. V pravém podokně vyberte **+ Přidat úroveň** .

    [![tlačítko Přidat úroveň](media/v2-update-how-to-tsm/portal-eight.png)](media/v2-update-how-to-tsm/portal-eight.png#lightbox)

1. Zadejte podrobnosti hierarchie a vyberte **vytvořit**.

    [Podrobnosti ![hieararchy a tlačítko "vytvořit"](media/v2-update-how-to-tsm/portal-nine.png)](media/v2-update-how-to-tsm/portal-nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Hromadně nahrát jednu nebo více hierarchií

1. Vyberte **nahrát JSON**.
1. Vyberte soubor, který obsahuje datovou část hierarchie.
1. Vyberte **Nahrát**.

    [Výběry ![pro hromadné nahrávání hierarchií](media/v2-update-how-to-tsm/portal-ten.png)](media/v2-update-how-to-tsm/portal-ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Upravit jednu hierarchii

1. Vyberte hierarchii a vyberte **Upravit**.
1. Proveďte požadované změny a vyberte **Uložit**.

    [Výběry ![pro úpravy jedné hierarchie](media/v2-update-how-to-tsm/portal-eleven.png)](media/v2-update-how-to-tsm/portal-eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Odstranění hierarchie

1. Vyberte hierarchii a vyberte **Odstranit**. 
1. Pokud k hierarchii nejsou přidružené žádné instance, odstraní se.

    [![tlačítko "odstranit"](media/v2-update-how-to-tsm/portal-twelve.png)](media/v2-update-how-to-tsm/portal-twelve.png#lightbox)

## <a name="instances"></a>Instance

### <a name="create-a-single-instance"></a>Vytvoření jedné instance

1. Přejděte na panel selektor modelu časové řady a v nabídce vyberte **instance** . Sbalením panelu Zaměřte se na instance modelu časové řady.

    [![podokně Instances](media/v2-update-how-to-tsm/portal-thirteen.png)](media/v2-update-how-to-tsm/portal-thirteen.png#lightbox)

1. Vyberte **Přidat**.

    [Výběry ![pro přidání instance](media/v2-update-how-to-tsm/portal-fourteen.png)](media/v2-update-how-to-tsm/portal-fourteen.png#lightbox)

1. Zadejte podrobnosti instance, vyberte přidružení typu a hierarchie a vyberte **vytvořit**.

### <a name="bulk-upload-one-or-more-instances"></a>Hromadně nahrát jednu nebo více instancí

1. Vyberte **nahrát JSON**.
1. Vyberte soubor, který obsahuje datovou část instance.

    [![výběry pro hromadné nahrávání jedné nebo více instancí](media/v2-update-how-to-tsm/portal-fifteen.png)](media/v2-update-how-to-tsm/portal-fifteen.png#lightbox)

1. Vyberte **Nahrát**.

### <a name="edit-a-single-instance"></a>Úprava jedné instance

1. Vyberte instanci a pak vyberte **Upravit**. 
1. Proveďte požadované změny a vyberte **Uložit**.

    [Výběry ![pro úpravy jedné instance](media/v2-update-how-to-tsm/portal-sixteen.png)](media/v2-update-how-to-tsm/portal-sixteen.png#lightbox)

## <a name="next-steps"></a>Další kroky

- Další informace o modelu časové řady získáte v tématu věnovaném [modelování dat](./time-series-insights-update-tsm.md).

- Pokud chcete získat další informace o verzi Preview, přečtěte si téma [vizualizace dat v průzkumníkovi Azure Time Series Insights Preview](./time-series-insights-update-explorer.md).

- Další informace o podporovaných tvarech JSON najdete v článku [podporované tvary JSON](./time-series-insights-send-events.md#supported-json-shapes).
