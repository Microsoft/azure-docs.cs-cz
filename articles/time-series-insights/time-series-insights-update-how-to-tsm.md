---
title: Modelování dat v Azure Time Series Insights Preview | Microsoft Docs
description: Pochopení modelování dat v Azure Time Series Insights Preview.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/04/2019
ms.custom: seodec18
ms.openlocfilehash: 245a69f5e5834e68bbbd17a96859a93bc16eacbe
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744372"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modelování dat v Azure Time Series Insights Preview

Tento dokument popisuje, jak pracovat s modely časových řad, které následují Azure Time Series Insights Preview. Podrobně popisuje několik běžných scénářů dat.

Pokud se chcete dozvědět víc o použití aktualizace, přečtěte si téma [Azure Time Series Insights Průzkumník Preview](./time-series-insights-update-explorer.md).

## <a name="types"></a>Typy

### <a name="create-a-single-type"></a>Vytvoření jednoho typu

1. Přejděte na panel selektor modelů časové řady a v nabídce vyberte **typy** . Sbalením panelu Zaměřte se na typy modelů časové řady.

    [![Vytvoření jednoho typu](media/v2-update-how-to-tsm/portal-one.png)](media/v2-update-how-to-tsm/portal-one.png#lightbox)

1. Vyberte **+ Přidat**.
1. Zadejte všechny podrobnosti, které se týkají typů, a vyberte **vytvořit**. Tato akce vytvoří typy v prostředí.

    [![Přidat typ](media/v2-update-how-to-tsm/portal-two.png)](media/v2-update-how-to-tsm/portal-two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Hromadné nahrání jednoho nebo více typů

1. Vyberte **nahrát JSON**.
1. Vyberte soubor, který obsahuje datovou část typu.
1. Vyberte **Nahrát**.

    [![Nahrát JSON](media/v2-update-how-to-tsm/portal-three.png)](media/v2-update-how-to-tsm/portal-three.png#lightbox)

### <a name="edit-a-single-type"></a>Upravit jeden typ

1. Vyberte typ a vyberte **Upravit**. 
1. Proveďte požadované změny a vyberte **Uložit**.

    [![Upravit typ](media/v2-update-how-to-tsm/portal-four.png)](media/v2-update-how-to-tsm/portal-four.png#lightbox)

### <a name="delete-a-type"></a>Odstranění typu

1. Vyberte typ a vyberte **Odstranit**.
1. Pokud nejsou k těmto typům přidruženy žádné instance, odstraní se.

    [![Odstranění typu](media/v2-update-how-to-tsm/portal-five.png)](media/v2-update-how-to-tsm/portal-five.png#lightbox)

## <a name="hierarchies"></a>Hierarchie

### <a name="create-a-single-hierarchy"></a>Vytvořit jednu hierarchii

1. Přejděte na panel selektor modelů časové řady a v nabídce vyberte možnost **hierarchie** . Sbalením panelu Zaměřte se na hierarchie modelů časových řad.

    [![Vybrat hierarchie](media/v2-update-how-to-tsm/portal-six.png)](media/v2-update-how-to-tsm/portal-six.png#lightbox)

1. Vyberte **+ Přidat**.

    [![Přidat hierarchii](media/v2-update-how-to-tsm/portal-seven.png)](media/v2-update-how-to-tsm/portal-seven.png#lightbox)

1. V pravém podokně vyberte **+ Přidat úroveň** .

    [![Přidat úroveň](media/v2-update-how-to-tsm/portal-eight.png)](media/v2-update-how-to-tsm/portal-eight.png#lightbox)

1. Zadejte podrobnosti hierarchie a vyberte **vytvořit**.

    [![Vytvořit úroveň](media/v2-update-how-to-tsm/portal-nine.png)](media/v2-update-how-to-tsm/portal-nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Hromadně nahrát jednu nebo více hierarchií

1. Vyberte **nahrát JSON**.
1. Vyberte soubor, který obsahuje datovou část hierarchie.
1. Vyberte **Nahrát**.

    [![Hierarchie hromadného nahrání](media/v2-update-how-to-tsm/portal-ten.png)](media/v2-update-how-to-tsm/portal-ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Upravit jednu hierarchii

1. Vyberte hierarchii a vyberte **Upravit**.
1. Proveďte požadované změny a vyberte **Uložit**.

    [![Upravit jednu hierarchii](media/v2-update-how-to-tsm/portal-eleven.png)](media/v2-update-how-to-tsm/portal-eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Odstranění hierarchie

1. Vyberte hierarchii a vyberte **Odstranit**. 
1. Pokud k hierarchii nejsou přidružené žádné instance, odstraní se.

    [![Odstranění hierarchie](media/v2-update-how-to-tsm/portal-twelve.png)](media/v2-update-how-to-tsm/portal-twelve.png#lightbox)

## <a name="instances"></a>Instance

### <a name="create-a-single-instance"></a>Vytvoření jedné instance

1. Přejděte na panel selektor modelů časové řady a v nabídce vyberte **instance** . Sbalením panelu Zaměřte se na instance modelů časových řad.

    [![Vytvoření jedné instance](media/v2-update-how-to-tsm/portal-thirteen.png)](media/v2-update-how-to-tsm/portal-thirteen.png#lightbox)

1. Vyberte **Přidat**.

    [![Přidat instanci](media/v2-update-how-to-tsm/portal-fourteen.png)](media/v2-update-how-to-tsm/portal-fourteen.png#lightbox)

1. Zadejte podrobnosti instance, vyberte přidružení typu a hierarchie a vyberte **vytvořit**.

### <a name="bulk-upload-one-or-more-instances"></a>Hromadně nahrát jednu nebo více instancí

1. Vyberte **nahrát JSON**.
1. Vyberte soubor, který obsahuje datovou část instance.

    [![Hromadně nahrát jednu nebo více instancí](media/v2-update-how-to-tsm/portal-fifteen.png)](media/v2-update-how-to-tsm/portal-fifteen.png#lightbox)

1. Vyberte **Nahrát**.

### <a name="edit-a-single-instance"></a>Úprava jedné instance

1. Vyberte instanci a pak vyberte **Upravit**. 
1. Proveďte požadované změny a vyberte **Uložit**.

    [![Úprava jedné instance](media/v2-update-how-to-tsm/portal-sixteen.png)](media/v2-update-how-to-tsm/portal-sixteen.png#lightbox)

## <a name="next-steps"></a>Další postup

- Další informace o modelech časových řad získáte v článku o [modelování dat](./time-series-insights-update-tsm.md).

- Pokud chcete získat další informace o verzi Preview, přečtěte si téma [vizualizace dat v průzkumníkovi Azure Time Series Insights Preview](./time-series-insights-update-explorer.md).

- Další informace o podporovaných tvarech JSON najdete v článku [podporované tvary JSON](./time-series-insights-send-events.md#json).
