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
ms.openlocfilehash: 64f892f344780d4af58c70935b9b7b68bad9550d
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72273740"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modelování dat v Azure Time Series Insights Preview

Tento dokument popisuje, jak pracovat s modely časových řad, které následují Azure Time Series Insights Preview. Podrobně popisuje několik běžných scénářů dat.

Pokud se chcete dozvědět víc o použití aktualizace, přečtěte si téma [Azure Time Series Insights Průzkumník Preview](./time-series-insights-update-explorer.md).

## <a name="types"></a>Typy

### <a name="create-a-single-type"></a>Vytvoření jednoho typu

1. Přejděte na panel selektor modelů časové řady a v nabídce vyberte **typy** . Sbalením panelu Zaměřte se na typy modelů časové řady.

    [@no__t – 1Create jeden typ](media/v2-update-how-to-tsm/portal-one.png)](media/v2-update-how-to-tsm/portal-one.png#lightbox)

1. Vyberte **+ Přidat**.
1. Zadejte všechny podrobnosti, které se týkají typů, a vyberte **vytvořit**. Tato akce vytvoří typy v prostředí.

    [@no__t – 1Add typu](media/v2-update-how-to-tsm/portal-two.png)](media/v2-update-how-to-tsm/portal-two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Hromadné nahrání jednoho nebo více typů

1. Vyberte **nahrát JSON**.
1. Vyberte soubor, který obsahuje datovou část typu.
1. Vyberte **nahrát**.

    [@no__t – 1Upload JSON](media/v2-update-how-to-tsm/portal-three.png)](media/v2-update-how-to-tsm/portal-three.png#lightbox)

### <a name="edit-a-single-type"></a>Upravit jeden typ

1. Vyberte typ a vyberte **Upravit**. 
1. Proveďte požadované změny a vyberte **Uložit**.

    [@no__t – 1Edit typu](media/v2-update-how-to-tsm/portal-four.png)](media/v2-update-how-to-tsm/portal-four.png#lightbox)

### <a name="delete-a-type"></a>Odstranění typu

1. Vyberte typ a vyberte **Odstranit**.
1. Pokud nejsou k těmto typům přidruženy žádné instance, odstraní se.

    [@no__t – 1Delete typu](media/v2-update-how-to-tsm/portal-five.png)](media/v2-update-how-to-tsm/portal-five.png#lightbox)

## <a name="hierarchies"></a>Hierarchie

### <a name="create-a-single-hierarchy"></a>Vytvořit jednu hierarchii

1. Přejděte na panel selektor modelů časové řady a v nabídce vyberte možnost **hierarchie** . Sbalením panelu Zaměřte se na hierarchie modelů časových řad.

    [@no__t – hierarchie 1Select](media/v2-update-how-to-tsm/portal-six.png)](media/v2-update-how-to-tsm/portal-six.png#lightbox)

1. Vyberte **+ Přidat**.

    [@no__t hierarchie 1Add](media/v2-update-how-to-tsm/portal-seven.png)](media/v2-update-how-to-tsm/portal-seven.png#lightbox)

1. V pravém podokně vyberte **+ Přidat úroveň** .

    [@no__t – 1Add úrovně](media/v2-update-how-to-tsm/portal-eight.png)](media/v2-update-how-to-tsm/portal-eight.png#lightbox)

1. Zadejte podrobnosti hierarchie a vyberte **vytvořit**.

    [@no__t – 1Create úrovně](media/v2-update-how-to-tsm/portal-nine.png)](media/v2-update-how-to-tsm/portal-nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Hromadně nahrát jednu nebo více hierarchií

1. Vyberte **nahrát JSON**.
1. Vyberte soubor, který obsahuje datovou část hierarchie.
1. Vyberte **nahrát**.

    [@no__t – hierarchie nahrávání 1Bulk](media/v2-update-how-to-tsm/portal-ten.png)](media/v2-update-how-to-tsm/portal-ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Upravit jednu hierarchii

1. Vyberte hierarchii a vyberte **Upravit**.
1. Proveďte požadované změny a vyberte **Uložit**.

    [@no__t – 1Edit jednu hierarchii](media/v2-update-how-to-tsm/portal-eleven.png)](media/v2-update-how-to-tsm/portal-eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Odstranění hierarchie

1. Vyberte hierarchii a vyberte **Odstranit**. 
1. Pokud k hierarchii nejsou přidružené žádné instance, odstraní se.

    [@no__t hierarchie 1Delete](media/v2-update-how-to-tsm/portal-twelve.png)](media/v2-update-how-to-tsm/portal-twelve.png#lightbox)

## <a name="instances"></a>Instance

### <a name="create-a-single-instance"></a>Vytvoření jedné instance

1. Přejděte na panel selektor modelů časové řady a v nabídce vyberte **instance** . Sbalením panelu Zaměřte se na instance modelů časových řad.

    [@no__t – 1Create jednu instanci](media/v2-update-how-to-tsm/portal-thirteen.png)](media/v2-update-how-to-tsm/portal-thirteen.png#lightbox)

1. Vyberte **Přidat**.

    [@no__t – 1Add instance](media/v2-update-how-to-tsm/portal-fourteen.png)](media/v2-update-how-to-tsm/portal-fourteen.png#lightbox)

1. Zadejte podrobnosti instance, vyberte přidružení typu a hierarchie a vyberte **vytvořit**.

### <a name="bulk-upload-one-or-more-instances"></a>Hromadně nahrát jednu nebo více instancí

1. Vyberte **nahrát JSON**.
1. Vyberte soubor, který obsahuje datovou část instance.

    [@no__t – 1Bulk nahrát jednu nebo víc instancí](media/v2-update-how-to-tsm/portal-fifteen.png)](media/v2-update-how-to-tsm/portal-fifteen.png#lightbox)

1. Vyberte **nahrát**.

### <a name="edit-a-single-instance"></a>Úprava jedné instance

1. Vyberte instanci a pak vyberte **Upravit**. 
1. Proveďte požadované změny a vyberte **Uložit**.

    [@no__t – 1Edit jednu instanci](media/v2-update-how-to-tsm/portal-sixteen.png)](media/v2-update-how-to-tsm/portal-sixteen.png#lightbox)

## <a name="next-steps"></a>Další kroky

- Další informace o modelech časových řad získáte v článku o [modelování dat](./time-series-insights-update-tsm.md).

- Pokud chcete získat další informace o verzi Preview, přečtěte si téma [vizualizace dat v průzkumníkovi Azure Time Series Insights Preview](./time-series-insights-update-explorer.md).

- Další informace o podporovaných tvarech JSON najdete v článku [podporované tvary JSON](./time-series-insights-send-events.md#supported-json-shapes).
