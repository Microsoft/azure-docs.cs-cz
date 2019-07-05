---
title: Modelování dat v Azure čas Series Insights ve verzi Preview | Dokumentace Microsoftu
description: Vysvětlení modelování dat v Azure čas Series Insights ve verzi Preview.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/26/2019
ms.custom: seodec18
ms.openlocfilehash: 05faf77d22f77da87e7c22d47473e6debf0f77c8
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67460925"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modelování dat v Azure čas Series Insights ve verzi Preview

Tento dokument popisuje, jak pracovat s modely řady čas následující Insights Azure čas řady ve verzi Preview. Obsahuje podrobnosti o několik běžných scénářů data.

Další informace o tom, jak používat tuto aktualizaci, [Průzkumníka Azure čas Series Insights ve verzi Preview](./time-series-insights-update-explorer.md).

## <a name="types"></a>Typy

### <a name="create-a-single-type"></a>Vytvoření jednoho typu

1. Přejděte na panel selektor čas řady modely a vyberte **typy** z nabídky. Sbalte panel a zaměřte se na typy modelů řady čas.

    [![Vytvoření jednoho typu](media/v2-update-how-to-tsm/portal-one.png)](media/v2-update-how-to-tsm/portal-one.png#lightbox)

1. Vyberte **Přidat**.
1. Zadejte všechny podrobnosti, které se týkají typů a vyberte **vytvořit**. Tato akce vytvoří typy v prostředí.

    [![Přidání typu](media/v2-update-how-to-tsm/portal-two.png)](media/v2-update-how-to-tsm/portal-two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Hromadné nahrání jednoho nebo více typů

1. Vyberte **nahrát JSON**.
1. Vyberte soubor, který obsahuje typ datové části.
1. Vyberte **Nahrát**.

    [![Nahrát JSON](media/v2-update-how-to-tsm/portal-three.png)](media/v2-update-how-to-tsm/portal-three.png#lightbox)

### <a name="edit-a-single-type"></a>Úprava jednoho typu

1. Vyberte typ a vyberte **upravit**. 
1. Proveďte požadované změny a vyberte **Uložit**.

    [![Upravit typ](media/v2-update-how-to-tsm/portal-four.png)](media/v2-update-how-to-tsm/portal-four.png#lightbox)

### <a name="delete-a-type"></a>Odstranit typ

1. Vyberte typ a vyberte **odstranit**.
1. Pokud nejsou žádné instance související s typy, je odstranit.

    [![Odstranit typ](media/v2-update-how-to-tsm/portal-five.png)](media/v2-update-how-to-tsm/portal-five.png#lightbox)

## <a name="hierarchies"></a>Hierarchie

### <a name="create-a-single-hierarchy"></a>Vytvoření jedné hierarchie

1. Přejděte na panel selektor čas řady modely a vyberte **hierarchie** z nabídky. Sbalte panel a zaměřte se na modely řady čas hierarchie.

    [![Vyberte hierarchie](media/v2-update-how-to-tsm/portal-six.png)](media/v2-update-how-to-tsm/portal-six.png#lightbox)

1. Vyberte **Přidat**.

    [![Přidat hierarchie](media/v2-update-how-to-tsm/portal-seven.png)](media/v2-update-how-to-tsm/portal-seven.png#lightbox)

1. Vyberte **přidat úroveň** v pravém podokně.

    [![Přidat úroveň](media/v2-update-how-to-tsm/portal-eight.png)](media/v2-update-how-to-tsm/portal-eight.png#lightbox)

1. Zadejte podrobnosti o hierarchii a vyberte **vytvořit**.

    [![Vytvořit úroveň](media/v2-update-how-to-tsm/portal-nine.png)](media/v2-update-how-to-tsm/portal-nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Hromadné nahrání jednoho nebo více hierarchií

1. Vyberte **nahrát JSON**.
1. Vyberte soubor, který obsahuje datové části hierarchie.
1. Vyberte **Nahrát**.

    [![Hromadné nahrávání hierarchie](media/v2-update-how-to-tsm/portal-ten.png)](media/v2-update-how-to-tsm/portal-ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Upravit jedné hierarchie

1. Vybrat hierarchii a vyberte **upravit**.
1. Proveďte požadované změny a vyberte **Uložit**.

    [![Upravit jedné hierarchie](media/v2-update-how-to-tsm/portal-eleven.png)](media/v2-update-how-to-tsm/portal-eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Odstranit hierarchii

1. Vybrat hierarchii a vyberte **odstranit**. 
1. Pokud nejsou žádné instance související s hierarchií, se odstraní.

    [![Odstranit hierarchii](media/v2-update-how-to-tsm/portal-twelve.png)](media/v2-update-how-to-tsm/portal-twelve.png#lightbox)

## <a name="instances"></a>Instance

### <a name="create-a-single-instance"></a>Vytvořte jednu instanci

1. Přejděte na panel selektor čas řady modely a vyberte **instance** z nabídky. Sbalte panel a zaměřte se na modely čas řady instancí.

    [![Vytvořte jednu instanci](media/v2-update-how-to-tsm/portal-thirteen.png)](media/v2-update-how-to-tsm/portal-thirteen.png#lightbox)

1. Vyberte **Přidat**.

    [![Přidat instanci](media/v2-update-how-to-tsm/portal-fourteen.png)](media/v2-update-how-to-tsm/portal-fourteen.png#lightbox)

1. Zadejte podrobnosti instance, vyberte přidružení typu a hierarchie a zvolte **vytvořit**.

### <a name="bulk-upload-one-or-more-instances"></a>Hromadné nahrání jednu nebo víc instancí

1. Vyberte **nahrát JSON**.
1. Vyberte soubor, který obsahuje datovou část instancí.

    [![Hromadné nahrání jednu nebo víc instancí](media/v2-update-how-to-tsm/portal-fifteen.png)](media/v2-update-how-to-tsm/portal-fifteen.png#lightbox)

1. Vyberte **Nahrát**.

### <a name="edit-a-single-instance"></a>Upravit jednu instanci

1. Vyberte instanci a vyberte **upravit**. 
1. Proveďte požadované změny a vyberte **Uložit**.

    [![Upravit jednu instanci](media/v2-update-how-to-tsm/portal-sixteen.png)](media/v2-update-how-to-tsm/portal-sixteen.png#lightbox)

## <a name="next-steps"></a>Další postup

- Další informace o modelech řady čas, najdete v článku [modelování dat](./time-series-insights-update-tsm.md).

- Další informace o verzi preview, přečtěte si [vizualizace dat v Průzkumníku Azure čas Series Insights ve verzi Preview](./time-series-insights-update-explorer.md).

- Další informace o podporované tvary JSON najdete v článku [podporované tvary JSON](./time-series-insights-send-events.md#json).
