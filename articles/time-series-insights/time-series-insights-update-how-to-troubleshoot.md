---
title: Diagnostika a řešení potíží s Azure Time Series Insights (Preview) | Dokumentace Microsoftu
description: Pochopení způsobu, jak Diagnostika a řešení potíží s Azure Time Series Insights (Preview)
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: a9078d2f8a738700a30d265d9cfa3cd77ad72f08
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53015445"
---
# <a name="how-to-diagnose-and-troubleshoot"></a>Tom, jak Diagnostika a řešení potíží

Tento článek shrnuje několik běžných problémů můžete setkat, práci s vaším prostředím Azure Time Series Insights (TSI). Tento článek také popisuje možné příčiny a řešení pro každý.

## <a name="problem-i-cant-find-my-environment-in-the-time-series-insights-preview-explorer"></a>Problém: Nelze najít své prostředí v Průzkumníku Time Series Insights (Preview)

To může nastat, pokud nemáte oprávnění k přístupu k prostředí TSI. Uživatelé budou potřebovat role úroveň přístupu "Čtenář, zobrazíte jejich prostředí TSI. Můžete ověřit aktuální úrovní přístupu a udělit další přístup návštěvou část zásady přístupu k datům na prostředek TSI v [webu Azure Portal](https://portal.azure.com/).

  ![environment][1]

## <a name="problem-no-data-is-seen-in-the-time-series-insights-preview-explorer"></a>Problém: Žádná data se zobrazí v Průzkumníku Time Series Insights (Preview)

Několik běžných důvodů, proč se nemusí zobrazovat data v [Průzkumníku TSI Azure (Preview)](https://insights.timeseries.azure.com/preview):

1. Váš zdroj událostí nesmí přijímat data.

    Ověřte, že váš zdroj událostí (Event Hubu nebo služby IoT Hub) přijímá data ze značek / instance. Provedete to tak, že přejdete na stránku přehled vašeho prostředku na webu Azure Portal.

    ![řídicí panel přehledů][2]

1. Zdroj dat událostí není ve formátu JSON

    Azure TSI podporuje pouze data JSON. Ukázky JSON najdete v tématu [tvary JSON nepodporuje](./how-to-shape-query-json.md).

1. Klíč zdroje událostí chybí požadovaná oprávnění

    * Pro službu IoT Hub budete muset zadat klíč, který má povolení pro připojení služby.

    ![konfigurace][3]

    * Jak je znázorněno na předchozím obrázku, buď zásady *iothubowner* a služba bude fungovat, protože obě mají povolení pro připojení služby.
    * Pro Centrum událostí budete muset zadat klíč, který má oprávnění naslouchat.
  
    ![oprávnění][4]

    * Jak je znázorněno na předchozím obrázku, buď zásady **čtení** a **spravovat** bude fungovat, protože obě mají **naslouchání** oprávnění.

1. Vaše skupina uživatelů, které jsou k dispozici není výhradní TSI

    Během registrace služby IoT Hub nebo Event Hub určete skupinu příjemců, který se má použít pro načtení dat. Této skupiny příjemců nesmí sdílet. Pokud se skupina uživatelů se sdílí, základní centra událostí automaticky odpojí jeden čtecích zařízení náhodně. Zadejte skupinu příjemců jedinečný pro čtení z TSI.

1. Vaše ID řady času vlastnost určili během zřizování je nesprávná, chybí nebo hodnotu null

    K tomu může dojít, pokud **ID řady času** vlastnost není správně nakonfigurovaná při zřizování prostředí. Podrobnosti najdete [osvědčené postupy pro výběr ID řady času](./time-series-insights-update-how-to-id.md). V tuto chvíli nemůžete aktualizovat stávající prostředí Time Series Insights aktualizace chcete použít jinou **ID řady času**.

## <a name="problem-some-data-is-shown-but-some-is-missing"></a>Problém: Některá data se zobrazí, ale chybí některé

1. Může odesílat data bez ID řady času

    K této chybě může dojít, když odesíláte události bez pole ID řady času v datové části. Zobrazit [tvary JSON nepodporuje](./how-to-shape-query-json.md) Další informace.

1. To může nastat, protože je omezovaná vašeho prostředí.

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

Pokud **časové razítko** vlastnost nebyl explicitně zadán, bude můžeme využívat k události služby IoT Hub nebo Event Hub **čas zařazení do fronty** jako výchozí časové razítko.

## <a name="problem-i-cant-edit-or-view-my-time-series-model"></a>Problém: nejde upravit nebo Zobrazit Moje modelu časové řady

1. Může být přistupujete k Time Series Insights S1 nebo S2 prostředí

   Čas řady modely jsou podporována pouze v **PAYG** prostředí. Najdete v tomto článku pro další informace o přístupu k prostředí S1/S2 z Průzkumníka aktualizace času Series Insights.

   ![access][5]

1. Možná nemáte oprávnění k zobrazení a úpravě modelu

   Uživatelé potřebují "Přispěvatel" úroveň přístupu můžete upravit a zobrazit jejich modelu časové řady. Můžete ověřit aktuální úrovní přístupu a udělit další přístup pomocí najdete v části zásady přístupu k datům pro váš prostředek Time Series Insights na webu Azure Portal.

## <a name="problem-all-my-instances-in-time-series-insights-preview-explorer-dont-have-a-parent"></a>Problém: Všechny instance v Průzkumníku Time Series Insights (Preview) nemají nadřazený

K tomu může dojít, pokud vaše prostředí nemá **modelu časové řady** definice hierarchie. Najdete v tomto článku pro další informace o [jak pracovat s modely řady čas](./time-series-insights-update-how-to-tsm.md).

  ![tsm][6]

## <a name="next-steps"></a>Další postup

Čtení [jak pracovat s modely řady čas](./time-series-insights-update-how-to-tsm.md).

Čtení [tvary JSON nepodporuje](./how-to-shape-query-json.md).

<!-- Images -->
[1]: media/v2-update-diagnose-and-troubleshoot/environment.png
[2]: media/v2-update-diagnose-and-troubleshoot/dashboard-insights.png
[3]: media/v2-update-diagnose-and-troubleshoot/configuration.png
[4]: media/v2-update-diagnose-and-troubleshoot/permissions.png
[5]: media/v2-update-diagnose-and-troubleshoot/access.png
[6]: media/v2-update-diagnose-and-troubleshoot/tsm.png