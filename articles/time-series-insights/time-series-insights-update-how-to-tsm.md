---
title: Modelování dat v Azure čas Series Insights ve verzi Preview | Dokumentace Microsoftu
description: Vysvětlení modelování dat v Azure čas Series Insights ve verzi Preview.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: 1686b52c9dacf5c78335f76bd19b41c4c7d15cee
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555400"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Modelování dat v Azure čas Series Insights ve verzi Preview

Tento dokument popisuje, jak pracovat s modely řady čas následující Insights Azure čas řady ve verzi Preview. Obsahuje podrobnosti o několik běžných scénářů data.

Další informace o tom, jak používat tuto aktualizaci, [Průzkumníka Azure čas Series Insights ve verzi Preview](./time-series-insights-update-explorer.md).

## <a name="types"></a>Typy

### <a name="create-a-single-type"></a>Vytvoření jednoho typu

1. Přejděte na panel selektor čas řady modely a vyberte **typy** z nabídky. Sbalte panel a zaměřte se na typy modelů řady čas.

    ![Portal_one][1]

1. Vyberte **Přidat**.
1. Zadejte všechny podrobnosti, které se týkají typů a vyberte **vytvořit**. Tato akce vytvoří typy v prostředí.

    ![Portal_two][2]

### <a name="bulk-upload-one-or-more-types"></a>Hromadné nahrání jednoho nebo více typů

1. Vyberte **nahrát JSON**.
1. Vyberte soubor, který obsahuje typ datové části.
1. Vyberte **Nahrát**.

    ![Portal_three][3]

### <a name="edit-a-single-type"></a>Úprava jednoho typu

Vyberte typ a vyberte **upravit**. Proveďte požadované změny a vyberte **Uložit**.

![Portal_four][4]

### <a name="delete-a-type"></a>Odstranit typ

Vyberte typ a vyberte **odstranit**. Pokud nejsou žádné instance související s typy, je odstranit.

![Portal_five][5]

## <a name="hierarchies"></a>Hierarchie

### <a name="create-a-single-hierarchy"></a>Vytvoření jedné hierarchie

1. Přejděte na panel selektor čas řady modely a vyberte **hierarchie** z nabídky. Sbalte panel a zaměřte se na modely řady čas hierarchie.

    ![Portal_six][6]

1. Vyberte **Přidat**.

    ![Portal_seven][7]

1. Vyberte **přidat úroveň** v pravém podokně.

    ![Portal_eight][8]

1. Zadejte podrobnosti o hierarchii a vyberte **vytvořit**.

    ![Portal_nine][9]

### <a name="bulk-upload-one-or-more-hierarchies"></a>Hromadné nahrání jednoho nebo více hierarchií

1. Vyberte **nahrát JSON**.
1. Vyberte soubor, který obsahuje datové části hierarchie.
1. Vyberte **Nahrát**.

    ![Portal_ten][10]

### <a name="edit-a-single-hierarchy"></a>Upravit jedné hierarchie

Vybrat hierarchii a vyberte **upravit**. Proveďte požadované změny a vyberte **Uložit**.

![Portal_eleven][11]

### <a name="delete-a-hierarchy"></a>Odstranit hierarchii

Vybrat hierarchii a vyberte **odstranit**. Pokud nejsou žádné instance související s hierarchií, se odstraní.

![Portal_twelve][12]

## <a name="instances"></a>Instance

### <a name="create-a-single-instance"></a>Vytvořte jednu instanci

1. Přejděte na panel selektor čas řady modely a vyberte **instance** z nabídky. Sbalte panel a zaměřte se na modely čas řady instancí.

    ![Portal_thirteen][13]

1. Vyberte **Přidat**.

    ![Portal_fourteen][14]

1. Zadejte podrobnosti instance, vyberte přidružení typu a hierarchie a zvolte **vytvořit**.

### <a name="bulk-upload-one-or-more-instances"></a>Hromadné nahrání jednu nebo víc instancí

1. Vyberte **nahrát JSON**.
1. Vyberte soubor, který obsahuje datovou část instancí.

    ![Portal_fifteen][15]

1. Vyberte **Nahrát**.

### <a name="edit-a-single-instance"></a>Upravit jednu instanci

Vyberte instanci a vyberte **upravit**. Proveďte požadované změny a vyberte **Uložit**.

![Portal_sixteen][16]

### <a name="delete-an-instance"></a>Odstranění instance

Vyberte instanci a vyberte **odstranit**. Pokud nejsou žádné události související s instancí, je odstranit.

## <a name="next-steps"></a>Další postup

- Další informace o modelech řady čas, najdete v článku [modelování dat](./time-series-insights-update-tsm.md).
- Další informace o verzi preview, přečtěte si [vizualizace dat v Průzkumníku Azure čas Series Insights ve verzi Preview](./time-series-insights-update-explorer.md).
- Další informace o podporované tvary JSON najdete v článku [podporované tvary JSON](./time-series-insights-send-events.md#json).

<!-- Images -->
[1]: media/v2-update-how-to-tsm/portal_one.png
[2]: media/v2-update-how-to-tsm/portal_two.png
[3]: media/v2-update-how-to-tsm/portal_three.png
[4]: media/v2-update-how-to-tsm/portal_four.png
[5]: media/v2-update-how-to-tsm/portal_five.png
[6]: media/v2-update-how-to-tsm/portal_six.png
[7]: media/v2-update-how-to-tsm/portal_seven.png
[8]: media/v2-update-how-to-tsm/portal_eight.png
[9]: media/v2-update-how-to-tsm/portal_nine.png
[10]: media/v2-update-how-to-tsm/portal_ten.png
[11]: media/v2-update-how-to-tsm/portal_eleven.png
[12]: media/v2-update-how-to-tsm/portal_twelve.png
[13]: media/v2-update-how-to-tsm/portal_thirteen.png
[14]: media/v2-update-how-to-tsm/portal_fourteen.png
[15]: media/v2-update-how-to-tsm/portal_fifteen.png
[16]: media/v2-update-how-to-tsm/portal_sixteen.png