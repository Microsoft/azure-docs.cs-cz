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
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 73384868deb8f0e33b233e363c42a12adbcbe402
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66237564"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modelování dat v Azure čas Series Insights ve verzi Preview

Tento dokument popisuje, jak pracovat s modely řady čas následující Insights Azure čas řady ve verzi Preview. Obsahuje podrobnosti o několik běžných scénářů data.

Další informace o tom, jak používat tuto aktualizaci, [Průzkumníka Azure čas Series Insights ve verzi Preview](./time-series-insights-update-explorer.md).

## <a name="types"></a>Typy

### <a name="create-a-single-type"></a>Vytvoření jednoho typu

1. Přejděte na panel selektor čas řady modely a vyberte **typy** z nabídky. Sbalte panel a zaměřte se na typy modelů řady čas.

    [![Vytvoření jednoho typu](media/v2-update-how-to-tsm/portal_one.png)](media/v2-update-how-to-tsm/portal_one.png#lightbox)

1. Vyberte **Přidat**.
1. Zadejte všechny podrobnosti, které se týkají typů a vyberte **vytvořit**. Tato akce vytvoří typy v prostředí.

    [![Přidání typu](media/v2-update-how-to-tsm/portal_two.png)](media/v2-update-how-to-tsm/portal_two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Hromadné nahrání jednoho nebo více typů

1. Vyberte **nahrát JSON**.
1. Vyberte soubor, který obsahuje typ datové části.
1. Vyberte **Nahrát**.

    [![Nahrát JSON](media/v2-update-how-to-tsm/portal_three.png)](media/v2-update-how-to-tsm/portal_three.png#lightbox)

### <a name="edit-a-single-type"></a>Úprava jednoho typu

1. Vyberte typ a vyberte **upravit**. 
1. Proveďte požadované změny a vyberte **Uložit**.

    [![Upravit typ](media/v2-update-how-to-tsm/portal_four.png)](media/v2-update-how-to-tsm/portal_four.png#lightbox)

### <a name="delete-a-type"></a>Odstranit typ

1. Vyberte typ a vyberte **odstranit**.
1. Pokud nejsou žádné instance související s typy, je odstranit.

    [![Odstranit typ](media/v2-update-how-to-tsm/portal_five.png)](media/v2-update-how-to-tsm/portal_five.png#lightbox)

## <a name="hierarchies"></a>Hierarchie

### <a name="create-a-single-hierarchy"></a>Vytvoření jedné hierarchie

1. Přejděte na panel selektor čas řady modely a vyberte **hierarchie** z nabídky. Sbalte panel a zaměřte se na modely řady čas hierarchie.

    [![Vyberte hierarchie](media/v2-update-how-to-tsm/portal_six.png)](media/v2-update-how-to-tsm/portal_six.png#lightbox)

1. Vyberte **Přidat**.

    [![Přidat hierarchie](media/v2-update-how-to-tsm/portal_seven.png)](media/v2-update-how-to-tsm/portal_seven.png#lightbox)

1. Vyberte **přidat úroveň** v pravém podokně.

    [![Přidat úroveň](media/v2-update-how-to-tsm/portal_eight.png)](media/v2-update-how-to-tsm/portal_eight.png#lightbox)

1. Zadejte podrobnosti o hierarchii a vyberte **vytvořit**.

    [![Vytvořit úroveň](media/v2-update-how-to-tsm/portal_nine.png)](media/v2-update-how-to-tsm/portal_nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Hromadné nahrání jednoho nebo více hierarchií

1. Vyberte **nahrát JSON**.
1. Vyberte soubor, který obsahuje datové části hierarchie.
1. Vyberte **Nahrát**.

    [![Hromadné nahrávání hierarchie](media/v2-update-how-to-tsm/portal_ten.png)](media/v2-update-how-to-tsm/portal_ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Upravit jedné hierarchie

1. Vybrat hierarchii a vyberte **upravit**.
1. Proveďte požadované změny a vyberte **Uložit**.

    [![Upravit jedné hierarchie](media/v2-update-how-to-tsm/portal_eleven.png)](media/v2-update-how-to-tsm/portal_eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Odstranit hierarchii

1. Vybrat hierarchii a vyberte **odstranit**. 
1. Pokud nejsou žádné instance související s hierarchií, se odstraní.

    [![Odstranit hierarchii](media/v2-update-how-to-tsm/portal_twelve.png)](media/v2-update-how-to-tsm/portal_twelve.png#lightbox)

## <a name="instances"></a>Instance

### <a name="create-a-single-instance"></a>Vytvořte jednu instanci

1. Přejděte na panel selektor čas řady modely a vyberte **instance** z nabídky. Sbalte panel a zaměřte se na modely čas řady instancí.

    [![Vytvořte jednu instanci](media/v2-update-how-to-tsm/portal_thirteen.png)](media/v2-update-how-to-tsm/portal_thirteen.png#lightbox)

1. Vyberte **Přidat**.

    [![Přidat instanci](media/v2-update-how-to-tsm/portal_fourteen.png)](media/v2-update-how-to-tsm/portal_fourteen.png#lightbox)

1. Zadejte podrobnosti instance, vyberte přidružení typu a hierarchie a zvolte **vytvořit**.

### <a name="bulk-upload-one-or-more-instances"></a>Hromadné nahrání jednu nebo víc instancí

1. Vyberte **nahrát JSON**.
1. Vyberte soubor, který obsahuje datovou část instancí.

    [![Hromadné nahrání jednu nebo víc instancí](media/v2-update-how-to-tsm/portal_fifteen.png)](media/v2-update-how-to-tsm/portal_fifteen.png#lightbox)

1. Vyberte **Nahrát**.

### <a name="edit-a-single-instance"></a>Upravit jednu instanci

1. Vyberte instanci a vyberte **upravit**. 
1. Proveďte požadované změny a vyberte **Uložit**.

    [![Upravit jednu instanci](media/v2-update-how-to-tsm/portal_sixteen.png)](media/v2-update-how-to-tsm/portal_sixteen.png#lightbox)

## <a name="next-steps"></a>Další postup

- Další informace o modelech řady čas, najdete v článku [modelování dat](./time-series-insights-update-tsm.md).

- Další informace o verzi preview, přečtěte si [vizualizace dat v Průzkumníku Azure čas Series Insights ve verzi Preview](./time-series-insights-update-explorer.md).

- Další informace o podporované tvary JSON najdete v článku [podporované tvary JSON](./time-series-insights-send-events.md#json).
