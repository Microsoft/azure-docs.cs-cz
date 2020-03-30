---
title: Modelování dat v prostředích preview – Přehledy azure time series | Dokumenty společnosti Microsoft
description: Přečtěte si o modelování dat ve verzi Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/18/2020
ms.custom: seodec18
ms.openlocfilehash: 39ebbf99ad31cce20eabc20fbdc056c889235713
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470747"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modelování dat ve verzi Azure Time Series Insights Preview

Tento článek popisuje, jak pracovat s modelem časové řady ve verzi Azure Time Series Insights Preview. Podrobně popisuje několik běžných datových scénářů.

> [!TIP]
> * Přečtěte si o [modelu náhledu časových řad](time-series-insights-update-tsm.md).
> * Přečtěte si další informace o navigaci v náhledu náhledu [v průzkumníku Azure Time Series Insights Preview](./time-series-insights-update-explorer.md).

## <a name="instances"></a>Instance

Průzkumník Azure Time Series Insights podporuje operace **instance CREATE**, **READ**, **UPDATE**a **DELETE** v prohlížeči. 

Chcete-li začít, vyberte zobrazení **modelu** z průzkumníka Analýzy průzkumníka časových **řad.**

### <a name="create-a-single-instance"></a>Vytvoření jedné instance

1. Přejděte na panel Pro výběr modelu časové řady a z nabídky vyberte **Instance.** Zobrazí se všechny instance přidružené k vybranému prostředí Time Series Insights.

    [![Vytvořte jednu instanci tak, že nejprve vyberete instance.](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png#lightbox)

1. Vyberte **+ Přidat**.

    [![Přidejte instanci výběrem tlačítka + Přidat.](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png#lightbox)

1. Zadejte podrobnosti instance, vyberte typ a přidružení hierarchie a vyberte **Vytvořit**.

### <a name="bulk-upload-one-or-more-instances"></a>Hromadné nahrání jedné nebo více instancí

> [!TIP]
> Instance můžete uložit na plochu v jsonu. Stažený soubor JSON lze poté odeslat pomocí následujících kroků.

1. Vyberte **možnost Nahrát JSON**.
1. Vyberte soubor, který obsahuje datovou část instancí.

    [![Hromadné nahrávání instancí prostřednictvím JSON.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png#lightbox)

1. Vyberte **Nahrát**.

### <a name="edit-a-single-instance"></a>Úprava jedné instance

1. Vyberte instanci a vyberte ikonu **úprav** nebo **tužky**. 
1. Proveďte požadované změny a vyberte **Uložit**.

    [![Upravte jednu instanci.](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png#lightbox)

### <a name="delete-an-instance"></a>Odstranění instance

1. Vyberte instanci a vyberte ikonu **odstranit** nebo **bin odpadu**.

   [![Odstraňte instanci výběrem odstranit.](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png#lightbox)

1. Potvrďte odstranění výběrem **možnosti Odstranit**.

> [!NOTE]
> Instance musí úspěšně projít kontrolou ověření pole, která má být odstraněna.

## <a name="hierarchies"></a>Hierarchie

Průzkumník Azure Time Series Insights podporuje operace hierarchie **CREATE**, **READ**, **UPDATE**a **DELETE** v prohlížeči. 

Chcete-li začít, vyberte zobrazení **modelu** z průzkumníka Analýzy průzkumníka časových **řad.**

### <a name="create-a-single-hierarchy"></a>Vytvoření jedné hierarchie

1. Přejděte na panel Pro výběr modelu časové řady a z nabídky vyberte **Hierarchie.** Zobrazí se všechny hierarchie přidružené k vybranému prostředí Time Series Insights.

    [![Vytvořte hierarchii prostřednictvím podokna.](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png#lightbox)

1. Vyberte **+ Přidat**.

    [![Hierarchie + tlačítko Přidat.](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png#lightbox)

1. Vyberte **+ Přidat úroveň** v pravém podokně.

    [![Přidejte úroveň hierarchie.](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png)](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png#lightbox)

1. Zadejte podrobnosti hierarchie a vyberte **Uložit**.

    [![Zadejte podrobnosti hierarchie.](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png)](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Hromadné nahrání jedné nebo více hierarchií

> [!TIP]
> Hierarchie můžete uložit na plochu v json. Stažený soubor JSON lze poté odeslat pomocí následujících kroků.

1. Vyberte **možnost Nahrát JSON**.
1. Vyberte soubor, který obsahuje datovou část hierarchie.
1. Vyberte **Nahrát**.

    [![Výběrpro hromadné nahrávání hierarchií.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Úprava jedné hierarchie

1. Vyberte hierarchii a vyberte ikonu **úprav** nebo **tužky**.
1. Proveďte požadované změny a vyberte **Uložit**.

    [![Výběrpro úpravy jedné hierarchie.](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png#lightbox)

### <a name="delete-a-hierarchy"></a>Odstranění hierarchie

1. Vyberte hierarchii a vyberte ikonu **odstranit** nebo **bin odpadu**. 

    [![Odstraňte hierarchii výběrem tlačítka Odstranit.](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png#lightbox)

1. Potvrďte odstranění výběrem **možnosti Odstranit**.

## <a name="types"></a>Typy

Průzkumník Azure Time Series Insights podporuje operace typu **CREATE**, **READ**, **UPDATE**a **DELETE** v prohlížeči. 

Chcete-li začít, vyberte zobrazení **modelu** z průzkumníka Analýzy průzkumníka časových **řad.**

### <a name="create-a-single-type"></a>Vytvoření jednoho typu

1. Přejděte na panel Pro výběr modelu časové řady a z nabídky vyberte **Typy.** Zobrazí se všechny typy přidružené k vybranému prostředí Time Series Insights.

    [![Podokno Typy modelů časových řad.](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png#lightbox)

1. Výběrem **možnosti + Přidat** zobrazíte možnost **Přidat nový typ** vyskakovacího pole modální modální modální modální.
1. Zadejte vlastnosti a proměnné pro váš typ. Po zadání vyberte **Uložit**. 

    [![Nastavení konfigurace pro přidání typu.](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Hromadné nahrání jednoho nebo více typů

> [!TIP]
> Typy můžete uložit na plochu v JSON. Stažený soubor JSON lze poté odeslat pomocí následujících kroků.

1. Vyberte **možnost Nahrát JSON**.
1. Vyberte soubor, který obsahuje datovou část typu.
1. Vyberte **Nahrát**.

    [![Možnosti hromadného odesílání typů.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png#lightbox)

### <a name="edit-a-single-type"></a>Úprava jednoho typu

1. Vyberte typ a vyberte ikonu **úprav** nebo **tužky**.
1. Proveďte požadované změny a vyberte **Uložit**.

    [![Upravte typ v podokně.](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png#lightbox)

### <a name="delete-a-type"></a>Odstranění typu

1. Vyberte typ a vyberte ikonu **odstranit** nebo **odpadkového koše**. .

   [![Odstraňte typ výběrem možnosti Odstranit.](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png#lightbox)

1. Potvrďte odstranění výběrem **možnosti Odstranit**.

## <a name="next-steps"></a>Další kroky

- Další informace o modelu časových řad naleznete [v textu Modelování dat](./time-series-insights-update-tsm.md).

- Další informace o verzi Preview najdete [v části Visualize data v průzkumníku Náhled přehledů Azure Time Series .](./time-series-insights-update-explorer.md)

- Další informace o podporovaných obrazcích JSON naleznete v článek [Podporované obrazce JSON](./time-series-insights-send-events.md#supported-json-shapes).
