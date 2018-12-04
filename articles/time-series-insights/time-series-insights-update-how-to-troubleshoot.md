---
title: Diagnostika a řešení potíží s Azure Time Series Insights (preview) | Dokumentace Microsoftu
description: Pochopení způsobu, jak Diagnostika a řešení potíží s Azure Time Series Insights (preview)
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: d13d373169287a0ec5931d5437b0a3bc70ecd79a
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52855752"
---
# <a name="how-to-diagnose-and-troubleshoot"></a>Tom, jak Diagnostika a řešení potíží

Tento článek shrnuje několik běžných problémů můžete setkat, práci s vaším prostředím Azure Time Series Insights (TSI). Tento článek také popisuje možné příčiny a řešení pro každý.

## <a name="problem-no-data-is-seen-in-the-time-series-insights-preview-explorer"></a>Problém: Žádná data se zobrazí v Time Series Insights (preview) Explorer

Existuje několik důvodů, proč se nemusí zobrazovat data v Průzkumníku TSI Azure:

1. Váš zdroj událostí nesmí přijímat data.

    Ověřte, že váš zdroj událostí (Event Hubu nebo služby IoT Hub) přijímá data ze značek / instance. Provedete to tak, že přejdete na stránku přehled vašeho prostředku na webu Azure Portal.

    ![řídicí panel přehledů][1]

1. Zdroj dat událostí není ve formátu JSON

    Azure TSI podporuje pouze data JSON. Ukázky JSON najdete v tématu [tvary JSON nepodporuje](./how-to-shape-query-json.md).

1. Klíč zdroje událostí chybí požadovaná oprávnění

    ![konfigurace][2]

    * Pro službu IoT Hub budete muset zadat klíč, který má povolení pro připojení služby.
    * Jak je znázorněno na předchozím obrázku, buď zásady *iothubowner* a služba bude fungovat, protože obě mají povolení pro připojení služby.
    * Pro Centrum událostí budete muset zadat klíč, který má oprávnění naslouchat.
    * Jak je znázorněno na předchozím obrázku, buď zásady číst a spravovat bude fungovat, protože obě mají oprávnění naslouchat.

    ![oprávnění][3]

1. Vaše skupina uživatelů, které jsou k dispozici není výhradní TSI

    Během registrace am služby IoT Hub nebo centra událostí zadejte skupinu příjemců, který se má použít pro načtení dat. Tuto skupinu příjemců nesmí sdílet. Pokud se skupina uživatelů se sdílí, základní centra událostí automaticky odpojí jeden čtecích zařízení náhodně. Zadejte skupinu příjemců jedinečný pro čtení z TSI.

## <a name="problem-some-data-is-shown-but-some-is-missing"></a>Problém: Některá data se zobrazí, ale chybí některé

To může nastat, protože je omezovaná vašeho prostředí.

> [!NOTE]
> V současné době podporuje Azure TSI ingestování maximální počet 6 MB/s.

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problém: Nastavení názvu vlastnosti časového razítka zdroj událostí nebude fungovat.

Ujistěte se, že název a hodnotu v souladu s těmito pravidly:

* **Časové razítko** název vlastnosti je velká a malá písmena.
* **Časové razítko** hodnotu vlastnosti, které pochází ze zdroje událostí, jako řetězec formátu JSON by měl mít formát `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`. Příkladem takových řetězec je `“2008-04-12T12:53Z”`.

Nejjednodušší způsob, jak zajistit, že váš název vlastnosti časového razítka se zaznamenávají a funguje správně, je použít v Průzkumníku TSI. V Průzkumníku TSI pomocí grafu můžete vybrat určitou dobu, po podle názvu vlastnosti časového razítka. Klikněte pravým tlačítkem na výběr a zvolte **zkoumat události** možnost. První záhlaví sloupce musí být váš **časové razítko** by měl mít název vlastnosti a `($ts)` vedle slovo `Timestamp`, spíše než:

* `(abc)`, což by označoval TSI čte hodnoty dat jako řetězce
* Ikonu kalendáře, což může naznačovat, že TSI čte hodnoty dat jako datový typ datetime
* `#`, což by označoval TSI čte hodnoty dat jako celé číslo

## <a name="problem-my-time-series-id-property-is-incorrect-missing-or-null"></a>Problém: Vlastnost Moje ID řady času je nesprávná, chybí nebo hodnotu null

K tomu může dojít, pokud **ID řady času** vlastnost není správně nakonfigurovaná při zřizování prostředí. Najdete v článku [osvědčené postupy pro výběr ID řady času](./time-series-insights-update-how-to-id.md) článek při výběru **ID řady času**. V tuto chvíli nemůžete aktualizovat stávající prostředí TSI (preview) Chcete-li použít jinou **ID řady času**.

## <a name="problem-all-my-instances-in-time-series-insights-preview-explorer-dont-have-a-parent"></a>Problém: Všechny instance v Time Series Insights (preview) Průzkumníka nemají nadřazený

K tomu může dojít, pokud vaše prostředí nemá **modelu časové řady** definice hierarchie. Najdete v tomto článku pro další informace o [jak pracovat s modely řady čas](./time-series-insights-update-how-to-tsm.md).

## <a name="next-steps"></a>Další postup

* Čtení [jak pracovat s modely řady čas](./time-series-insights-update-how-to-tsm.md).

* Čtení [tvary JSON nepodporuje](./how-to-shape-query-json.md).

<!-- Images -->
[1]: media/v2-update-diagnose-and-troubleshoot/dashboard-insights.png
[2]: media/v2-update-diagnose-and-troubleshoot/configuration.png
[3]: media/v2-update-diagnose-and-troubleshoot/permissions.png