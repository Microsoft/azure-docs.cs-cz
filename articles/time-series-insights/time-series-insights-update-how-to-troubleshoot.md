---
title: Azure Time Series Insights ve verzi Preview – Diagnostika a řešení potíží | Dokumentace Microsoftu
description: Pochopit, jak Diagnostika a řešení Azure čas Series Insights ve verzi Preview.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: e35439c80dea8ac47033464eeb57f7e9859fdcf5
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53275291"
---
# <a name="diagnose-and-troubleshoot"></a>Diagnostika a řešení potíží

Tento článek shrnuje několik běžných problémů, které můžete narazit při práci s vaším prostředím Azure čas Series Insights ve verzi Preview. Tento článek také popisuje možné příčiny a řešení pro každý problém.

## <a name="problem-i-cant-find-my-environment-in-the-time-series-insights-preview-explorer"></a>Problém: Nemůžu najít své prostředí v Průzkumníku čas Series Insights ve verzi Preview

Tomuto problému může dojít, pokud nemáte příslušná oprávnění pro přístup k prostředí Time Series Insights. Uživatelé potřebují přístup na úrovni čtenář roli zobrazíte jejich prostředí Time Series Insights. Ověřte aktuální úrovní přístupu a udělte další oprávnění, najdete v části zásady přístupu k datům v Time Series Insights prostředku v [webu Azure portal](https://portal.azure.com/).

  ![Prostředí][1]

## <a name="problem-no-data-is-seen-in-the-time-series-insights-preview-explorer"></a>Problém: Žádná data se zobrazí v Průzkumníku čas Series Insights ve verzi Preview

Několik běžných důvodů, proč se nemusí zobrazovat data v [Průzkumníka Azure čas Series Insights ve verzi Preview](https://insights.timeseries.azure.com/preview).

- Váš zdroj událostí nemusí být přijímá data.

    Ověřte, že váš zdroj událostí, který je v Centru událostí nebo službu IoT hub, přijímá data ze značky nebo instancí. Abyste se přesvědčili, přejděte na stránku přehled vašeho prostředku na webu Azure Portal.

    ![řídicí panel přehledů][2]

- Zdroj dat událostí není ve formátu JSON.

    Time Series Insights podporuje jenom data JSON. Ukázky JSON najdete v tématu [tvary JSON nepodporuje](./how-to-shape-query-json.md).

- Klíč zdroje událostí chybí požadované oprávnění.

    * Pro službu IoT hub, je potřeba zadat klíč, který má **služba připojit** oprávnění.

    ![Konfigurace][3]

    * Jak je znázorněno na předchozím obrázku, obě zásady **iothubowner** a **služby** fungovat, protože mají **služba připojit** oprávnění.
    * Pro Centrum událostí, je potřeba zadat klíč, který má **naslouchání** oprávnění.
  
    ![Oprávnění][4]

    * Jak je znázorněno na předchozím obrázku, obě **čtení** a **spravovat** zásady fungují, protože mají **naslouchání** oprávnění.

- Vaše skupina uživatelů, které jsou k dispozici není výhradně pro Time Series Insights.

    Během registrace služby IoT hub nebo event hub určete skupinu příjemců, který se používá k načtení dat. Nesdílejte této skupiny příjemců. Pokud se skupina uživatelů se sdílí, základní centra událostí automaticky odpojí jeden čtecích zařízení náhodně. Zadejte skupinu příjemců jedinečný pro čtení ze služby Time Series Insights.

- Vaše ID řady času vlastnost určili během zřizování je nesprávná, chybí nebo hodnotu null.

    Tomuto problému může dojít, pokud vlastnost ID řady času není správně nakonfigurovaná při zřizování prostředí. Další informace najdete v tématu [osvědčené postupy pro výběr ID řady času](./time-series-insights-update-how-to-id.md). V tuto chvíli nemůžete aktualizovat existující prostředí Time Series Insights použít jiné ID řady čas

## <a name="problem-some-data-shows-but-some-is-missing"></a>Problém: Některá data zobrazí, ale některé nebyl nalezen

Můžete odesílat data bez ID řady čas.

- Tomuto problému může dojít, když odesíláte události bez pole ID řady času v datové části. Další informace najdete v tématu [tvary JSON nepodporuje](./how-to-shape-query-json.md).

- Tomuto problému může dojít, protože je omezovaná vašeho prostředí.

    > [!NOTE]
    > V současné době Time Series Insights podporuje ingestování maximální počet 6 MB/s.

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problém: Nastavení názvu vlastnosti časového razítka zdroj události nefunguje

Ujistěte se, že název a hodnotu v souladu s těmito pravidly:

* Název vlastnosti časového razítka je velká a malá písmena.
* Hodnota vlastnosti časového razítka, která se dodává ze zdroje událostí, jako řetězec formátu JSON má formát `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`. Příkladem takových řetězec je `“2008-04-12T12:53Z”`.

Nejjednodušší způsob, jak zajistit, že váš název vlastnosti časového razítka se zaznamenávají a funguje správně, je použít Průzkumník čas Series Insights ve verzi Preview. V Průzkumníkovi čas Series Insights ve verzi Preview pomocí grafu můžete vybrat určitou dobu, po podle názvu vlastnosti časového razítka. Klikněte pravým tlačítkem na výběr a vyberte **zkoumat události** možnost. První záhlaví sloupců je váš název vlastnosti časového razítka. Měl by mít `($ts)` vedle slovo `Timestamp`, spíše než:

* `(abc)`, což znamená, že Time Series Insights čte hodnoty dat jako řetězce.
* Ikonu kalendáře, což znamená, že Time Series Insights čte hodnoty dat jako datový typ datetime.
* `#`, což znamená, že Time Series Insights čte hodnoty dat jako celé číslo.

Pokud vlastnost časového razítka není explicitně zadán, k události služby IoT hub nebo event hub čas zařazení do fronty se používá jako výchozí časové razítko.

## <a name="problem-i-cant-edit-or-view-my-time-series-model"></a>Problém: Nejde upravit nebo Zobrazit Moje modelu časové řady

- Vám může přistupovat k Time Series Insights S1 nebo S2 prostředí.

   Čas řady modely jsou podporovány pouze v prostředí s průběžnými PLATBAMI. Další informace o tom, jak přistupovat k prostředí S1/S2 z Exploreru čas Series Insights ve verzi Preview najdete v tématu [vizualizace dat v Průzkumníku](./time-series-insights-update-explorer.md).

   ![Access][5]

- Nemáte oprávnění k zobrazení a úpravě modelu.

   Uživatelé potřebují přístup k úpravám a zobrazování jejich modelu časové řady na úrovni Přispěvatel. Ověřit aktuální úrovní přístupu a udělit další přístup, najdete v části zásady přístupu k datům pro váš prostředek Time Series Insights na webu Azure Portal.

## <a name="problem-all-my-instances-in-the-time-series-insights-preview-explorer-dont-have-a-parent"></a>Problém: Všechny instance v Průzkumníku čas Series Insights ve verzi Preview nemají nadřazený

Tomuto problému může dojít, pokud vaše prostředí nemá definované hierarchii modelu časové řady. Další informace najdete v tématu [čas řady modely](./time-series-insights-update-how-to-tsm.md).

  ![Čas řady modelů][6]

## <a name="next-steps"></a>Další postup

- Čtení [čas řady modely](./time-series-insights-update-how-to-tsm.md).
- Čtení [tvary JSON nepodporuje](./how-to-shape-query-json.md).

<!-- Images -->
[1]: media/v2-update-diagnose-and-troubleshoot/environment.png
[2]: media/v2-update-diagnose-and-troubleshoot/dashboard-insights.png
[3]: media/v2-update-diagnose-and-troubleshoot/configuration.png
[4]: media/v2-update-diagnose-and-troubleshoot/permissions.png
[5]: media/v2-update-diagnose-and-troubleshoot/access.png
[6]: media/v2-update-diagnose-and-troubleshoot/tsm.png