---
title: Modelování dat v Azure Time Series Insights | Dokumentace Microsoftu
description: Principy modelování dat v Azure Time Series Insights
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/30/2018
ms.openlocfilehash: 926d1a35cb10d50ddeacbed5476e2dcf14d0999d
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52855902"
---
# <a name="data-modeling-in-azure-time-series-insights"></a>Modelování dat v Azure Time Series Insights

Tento dokument popisuje, jak pracovat s **čas řady modely** následující služby Azure Time Series Insights (preview). Obsahuje podrobnosti o několik běžných scénářů data.

Čtení [Azure TSI (preview) Průzkumníka](./time-series-insights-update-explorer.md) článku se dozvíte další informace o procházení privátní verze Preview.

## <a name="types"></a>Typy

### <a name="how-to-create-a-single-type"></a>Jak vytvořit jeden typ.

1. Začněte tím, že nadpis panelu TSM modelu selektor a z nabídky vyberte typy. Potom sbalte panel a zaměřte se na TSM typy:

    ![portal_one][1]

1. Klikněte na **Přidat**.
1. Zadejte všechny informace týkající se typy a klikněte na tlačítko **vytvořit**. Mohlo by měl vytvořit typy v prostředí:

    ![portal_two][2]

### <a name="how-to-bulk-upload-one-or-more-types"></a>Postup hromadné nahrání jednoho nebo více typů

1. Klikněte na **nahrát JSON**.
1. Vyberte tento soubor, který obsahuje typ datové části.
1. Klikněte na **nahrát**

    ![portal_three][3]

### <a name="how-to-edit-a-single-type"></a>Úprava jednoho typu

* Vyberte typ a klikněte na **upravit** tlačítko. Proveďte požadované změny a klikněte na tlačítko **Uložit**:

    ![portal_four][4]

### <a name="how-to-delete-a-type"></a>Jak odstranit typ

* Vyberte typ a klikněte na **odstranit** tlačítko. Pokud nejsou žádné instance přidružené k typům, budou odstraněny:

    ![portal_five][5]

## <a name="hierarchies"></a>Hierarchie

### <a name="how-to-create-a-single-hierarchy"></a>Vytvoření jedné hierarchie

1. Začněte tím, že nadpis panelu TSM modelu selektor a vyberte v nabídce hierarchie. Potom sbalte panel a zaměřte se na TSM typy:

    ![portal_six][6]

1. Klikněte na **přidat**

    ![portal_seven][7]

1. Klikněte na **přidat úroveň** v pravém podokně:

    ![portal_eight][8]

1. Zadejte podrobnosti o hierarchii a klikněte na tlačítko **vytvořit**:

    ![portal_nine][9]

### <a name="how-to-bulk-upload-one-or-more-hierarchies"></a>Postup hromadné nahrání jednoho nebo více hierarchií

1. Klikněte na **nahrát JSON**.
1. Zvolte soubor, který obsahuje datové části hierarchie.
1. Klikněte na **nahrát**:

    ![portal_ten][10]

### <a name="how-to-edit-a-single-hierarchy"></a>Úprava jedné hierarchie

* Vybrat hierarchii a klikněte na **upravit** tlačítko. Proveďte požadované změny a klikněte na tlačítko **Uložit**:

    ![portal_eleven][11]

### <a name="how-to-delete-a-hierarchy"></a>Jak odstranit hierarchii

* Vybrat hierarchii a klikněte na **odstranit** tlačítko. Pokud nejsou žádné instance přidružené k hierarchii, budou odstraněny.

    ![portal_twelve][12]

## <a name="instances"></a>Instance

### <a name="how-to-create-a-single-instance"></a>Jak vytvořit jednu instanci

1. Začněte tím, že nadpis panelu TSM modelu selektor a z nabídky vyberte instance. Potom sbalte panel a zaměřte se na TSM typy:

    ![portal_thirteen][13]

1. Klikněte na **přidat**:

    ![portal_fourteen][14]

1. Zadejte podrobnosti instance, vyberte přidružení typu a hierarchii a klikněte na tlačítko **vytvořit**.

### <a name="how-to-bulk-upload-one-or-more-instances"></a>Postup hromadné nahrání jednu nebo víc instancí

1. Klikněte na **nahrát JSON**.
1. Zvolte soubor, který obsahuje datovou část instancí:

    ![portal_fifteen][15]

1. Klikněte na **nahrát**.

### <a name="how-to-edit-a-single-instance"></a>Úprava jedna instance

* Vyberte instanci a klikněte na **upravit** tlačítko. Proveďte požadované změny a klikněte na tlačítko **Uložit**:

    ![portal_sixteen][16]

### <a name="how-to-delete-an-instance"></a>Odstranění instance

* Vyberte instanci a klikněte na **odstranit** tlačítko. Pokud nejsou žádné události přidružené k instancím, budou odstraněny.

## <a name="next-steps"></a>Další postup

Čtení [čas řady modely](./time-series-insights-update-tsm.md) Další informace o **čas řady modely**.

Zobrazit Azure TSI (preview) Průzkumníka [článku](./time-series-insights-update-explorer.md) získat další informace o verzi preview.

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