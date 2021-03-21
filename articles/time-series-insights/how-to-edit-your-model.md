---
title: Modelování dat v prostředích Gen2 – Azure Time Series Insights | Microsoft Docs
description: Přečtěte si o modelování dat v Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: 89efc1d4f34b250d211f9fd7492588bd2896eb6e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95016849"
---
# <a name="data-modeling-in-azure-time-series-insights-gen2"></a>Modelování dat v Azure Time Series Insights Gen2

Tento článek popisuje, jak pracovat s modelem časových řad v Azure Time Series Insights Gen2. Podrobně popisuje několik běžných scénářů dat.

> [!TIP]
>
> * Přečtěte si další informace o [modelu časové řady](concepts-model-overview.md).
> * Přečtěte si další informace o navigaci [Azure Time Series Insights Gen2 Exploreru](./concepts-ux-panels.md).

## <a name="instances"></a>Instance

Průzkumník Azure Time Series Insights podporuje operace **Vytvoření**, **čtení**, **aktualizace** a **odstranění** instancí v prohlížeči.

Začněte tím, že vyberete zobrazení **modelu** v zobrazení **analyzovat** v Průzkumníkovi Azure Time Series Insights.

### <a name="create-a-single-instance"></a>Vytvoření jedné instance

1. Přejděte na panel selektor modelu časové řady a v nabídce vyberte **instance** . Zobrazí se všechny instance přidružené k vybranému Azure Time Series Insights prostředí.

    [![Nejprve vyberte instance a vytvořte jednu instanci.](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png#lightbox)

1. Vyberte **+ Přidat**.

    [![Přidejte instanci tak, že vyberete tlačítko + Přidat.](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png#lightbox)

1. Zadejte podrobnosti instance, vyberte přidružení typu a hierarchie a vyberte **vytvořit**.

### <a name="bulk-upload-one-or-more-instances"></a>Hromadně nahrát jednu nebo více instancí

> [!TIP]
> Své instance můžete ukládat do svého počítače ve formátu JSON. Stažený soubor JSON se pak dá nahrát pomocí následujících kroků.

1. Vyberte **nahrát JSON**.
1. Vyberte soubor, který obsahuje datovou část instance.

    [![Hromadné nahrání instancí prostřednictvím formátu JSON.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png#lightbox)

1. Vyberte **Nahrát**.

### <a name="edit-a-single-instance"></a>Úprava jedné instance

1. Vyberte instanci a vyberte ikonu **Upravit** nebo **Tužka**.
1. Proveďte požadované změny a vyberte **Uložit**.

    [![Upravte jednu instanci.](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png#lightbox)

### <a name="delete-an-instance"></a>Odstranění instance

1. Vyberte instanci a vyberte ikonu **Odstranit** nebo **Odpadkový koš**.

   [![Odstraňte instanci výběrem možnosti odstranit.](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png#lightbox)

1. Potvrďte odstranění výběrem možnosti **Odstranit**.

> [!NOTE]
> Instance musí úspěšně předat kontrolu ověření pole, která se má odstranit.

## <a name="hierarchies"></a>Hierarchie

Průzkumník Azure Time Series Insights podporuje operace **Vytvoření**, **čtení**, **aktualizace** a **odstranění** hierarchie v prohlížeči.

Začněte tím, že vyberete zobrazení **modelu** v zobrazení **analyzovat** v Průzkumníkovi Azure Time Series Insights.

### <a name="create-a-single-hierarchy"></a>Vytvořit jednu hierarchii

1. Přejděte na panel selektor modelu časové řady a v nabídce vyberte možnost **hierarchie** . Zobrazí se všechny hierarchie přidružené k vybranému Azure Time Series Insights prostředí.

    [![Vytvořte hierarchii prostřednictvím podokna.](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png#lightbox)

1. Vyberte **+ Přidat**.

    [![Tlačítko hierarchie + přidat.](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png#lightbox)

1. V pravém podokně vyberte **+ Přidat úroveň** .

    [![Přidejte do hierarchie úroveň.](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png)](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png#lightbox)

1. Zadejte podrobnosti hierarchie a vyberte **Uložit**.

    [![Zadejte podrobnosti hierarchie.](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png)](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Hromadně nahrát jednu nebo více hierarchií

> [!TIP]
> Hierarchie můžete uložit do svého počítače ve formátu JSON. Stažený soubor JSON se pak dá nahrát pomocí následujících kroků.

1. Vyberte **nahrát JSON**.
1. Vyberte soubor, který obsahuje datovou část hierarchie.
1. Vyberte **Nahrát**.

    [![Výběry pro hromadné nahrávání hierarchií.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Upravit jednu hierarchii

1. Vyberte hierarchii a vyberte ikonu **Upravit** nebo **Tužka**.
1. Proveďte požadované změny a vyberte **Uložit**.

    [![Výběry pro úpravu jedné hierarchie.](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png#lightbox)

### <a name="delete-a-hierarchy"></a>Odstranění hierarchie

1. Vyberte hierarchii a vyberte ikonu **Odstranit** nebo **Odpadkový koš**.

    [![Odstraňte hierarchii výběrem tlačítka odstranit.](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png#lightbox)

1. Potvrďte odstranění výběrem možnosti **Odstranit**.

## <a name="types"></a>Typy

Průzkumník Azure Time Series Insights podporuje operace **Vytvoření**, **čtení**, **aktualizace** a **odstranění** v prohlížeči.

Začněte tím, že vyberete zobrazení **modelu** v zobrazení **analyzovat** v Průzkumníkovi Azure Time Series Insights.

### <a name="create-a-single-type"></a>Vytvoření jednoho typu

1. Přejděte na panel selektor modelu časové řady a v nabídce vyberte **typy** . Zobrazí se všechny typy přidružené k vybranému Azure Time Series Insights prostředí.

    [![Podokno typu modelu časové řady.](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png#lightbox)

1. Vyberte **+ Přidat** a zobrazte tak automaticky otevírané okno **Přidat nový typ** .
1. Zadejte vlastnosti a proměnné pro svůj typ. Po zadání vyberte **Uložit**.

    [![Nastavení konfigurace pro přidání typu](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Hromadné nahrání jednoho nebo více typů

> [!TIP]
> Vaše typy můžete uložit na plochu ve formátu JSON. Stažený soubor JSON se pak dá nahrát pomocí následujících kroků.

1. Vyberte **nahrát JSON**.
1. Vyberte soubor, který obsahuje datovou část typu.
1. Vyberte **Nahrát**.

    [![Možnosti odesílání hromadných typů.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png#lightbox)

### <a name="edit-a-single-type"></a>Upravit jeden typ

1. Vyberte typ a vyberte ikonu **Upravit** nebo **Tužka**.
1. Proveďte požadované změny a vyberte **Uložit**.

    [![Upravit typ v podokně.](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png#lightbox)

### <a name="delete-a-type"></a>Odstranění typu

1. Vyberte typ a vyberte ikonu **Odstranit** nebo **Odpadkový koš**.

   [![Odstraňte typ výběrem možnosti odstranit.](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png#lightbox)

1. Potvrďte odstranění výběrem možnosti **Odstranit**.

## <a name="next-steps"></a>Další kroky

* Další informace o modelu časové řady získáte v tématu věnovaném [modelování dat](./concepts-model-overview.md).

* Pokud chcete získat další informace o Gen2, přečtěte si článek [Vizualizujte data v Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md).

* Další informace o podporovaných tvarech JSON najdete v článku [podporované tvary JSON](./time-series-insights-send-events.md#supported-json-shapes).