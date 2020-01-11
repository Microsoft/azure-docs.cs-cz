---
title: Diagnostika a řešení potíží s prostředím verze Preview – Azure Time Series Insights | Microsoft Docs
description: Naučte se diagnostikovat a řešit potíže s prostředím Azure Time Series Insights ve verzi Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.openlocfilehash: 1fc3aa6caa6266d2cd42e4783e8e39d5cc92c220
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2020
ms.locfileid: "75861570"
---
# <a name="diagnose-and-troubleshoot-a-preview-environment"></a>Diagnostika a řešení potíží s prostředím verze Preview

Tento článek shrnuje několik běžných problémů, se kterými se můžete setkat při práci s prostředím Azure Time Series Insights ve verzi Preview. Tento článek také popisuje možné příčiny a řešení jednotlivých problémů.

## <a name="problem-i-cant-find-my-environment-in-the-preview-explorer"></a>Problém: nemůžu najít své prostředí v Průzkumníkovi Preview

K tomuto problému může dojít, pokud nemáte oprávnění pro přístup k Time Series Insights prostředí. Uživatelé potřebují pro zobrazení svého Time Series Insightsho prostředí roli přístupu na úrovni čtenáře. Chcete-li ověřit aktuální úrovně přístupu a udělit další přístup, přejděte do části **zásady přístupu k datům** v prostředku Time Series Insights v [Azure Portal](https://portal.azure.com/).

  [![ověřit zásady přístupu k datům.](media/preview-troubleshoot/verify-data-access-policies.png)](media/preview-troubleshoot/verify-data-access-policies.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-preview-explorer"></a>Problém: v Průzkumníkovi Preview se nezobrazí žádná data.

K dispozici je několik běžných důvodů, proč se vaše data nemusí zobrazit v [Průzkumníkovi služby Azure Time Series Insights Preview](https://insights.timeseries.azure.com/preview).

- Váš zdroj událostí pravděpodobně nepřijímá data.

    Ověřte, že váš zdroj událostí, což je centrum událostí nebo centrum IoT, přijímá data z vašich značek nebo instancí. Pokud to chcete ověřit, na stránce s přehledem na svém prostředku navštivte Azure Portal.

    [![přehled metriky řídicího panelu.](media/preview-troubleshoot/verify-dashboard-metrics.png)](media/preview-troubleshoot/verify-dashboard-metrics.png#lightbox)

- Vaše zdrojová data události nejsou ve formátu JSON.

    Time Series Insights podporuje pouze data JSON. V případě ukázek JSON čtěte [podporované tvary JSON](./how-to-shape-query-json.md).

- V klíči zdroje událostí chybí požadovaná oprávnění.

  * V případě služby IoT Hub musíte zadat klíč, který má oprávnění **připojit ke službě** .

    [![Ověřte oprávnění pro IoT Hub.](media/preview-troubleshoot/verify-correct-permissions.png)](media/preview-troubleshoot/verify-correct-permissions.png#lightbox)

    * Zásady **iothubowner** **i fungují,** protože mají oprávnění **k připojení ke službě** .

  * V centru událostí je nutné zadat klíč, který má oprávnění k **naslouchání** .
  
    [![zkontrolovat oprávnění centra událostí.](media/preview-troubleshoot/verify-eh-permissions.png)](media/preview-troubleshoot/verify-eh-permissions.png#lightbox)

    * Zásady **číst** i **Spravovat** budou fungovat, protože mají oprávnění k **naslouchání** .

- Zadaná skupina uživatelů není výhradně Time Series Insights.

    Během registrace centra IoT nebo centra událostí určíte skupinu uživatelů, která se používá ke čtení dat. Tato skupina uživatelů musí být jedinečná pro každé prostředí. Pokud je skupina uživatelů sdílená, příslušné centrum událostí automaticky odpojí jedno z čtecích zařízení s náhodným připojením. Poskytněte jedinečnou skupinu uživatelů, ze které se má Time Series Insights číst.

- Vaše vlastnost ID časové řady zadaná v době zřizování není správná, chybí nebo je null.

    K tomuto problému může dojít, pokud je vlastnost ID časové řady v době zřizování prostředí nesprávně nakonfigurovaná. Další informace najdete v článku [osvědčené postupy pro výběr ID časové řady](./time-series-insights-update-how-to-id.md). V tuto chvíli nemůžete aktualizovat existující prostředí Time Series Insights tak, aby používalo jiné ID časové řady.

## <a name="problem-some-data-shows-but-some-is-missing"></a>Problém: některá data jsou zobrazená, ale chybí.

Můžete odesílat data bez ID časové řady.

- K tomuto problému může dojít při odesílání událostí bez pole ID časové řady v datové části. Další informace najdete v článku [podporované tvary JSON](./how-to-shape-query-json.md).
- K tomuto problému může dojít, protože vaše prostředí je omezené.

    > [!NOTE]
    > V tuto chvíli Time Series Insights podporuje maximální rychlost přijímání dat 6 MB/s.

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>Problém: název vlastnosti časového razítka zdroje události nefunguje

Ujistěte se, že název a hodnotu v souladu s těmito pravidly:

* V názvu vlastnosti časového razítka se rozlišují malá a velká písmena.
* Hodnota vlastnosti časového razítka, která pochází ze zdroje události jako řetězec JSON, má formát `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`. Příkladem takových řetězec je `“2008-04-12T12:53Z”`.

Nejjednodušší způsob, jak zajistit, aby byl název vlastnosti časového razítka zachycen a správně fungoval, je použít Průzkumníka služby Time Series Insights Preview. V Průzkumníku služby Time Series Insights Preview pomocí grafu vyberte časový úsek po zadání názvu vlastnosti časového razítka. Klikněte pravým tlačítkem na výběr a vyberte možnost **prozkoumat události** . Záhlaví prvního sloupce je název vlastnosti časového razítka. Mělo by mít `($ts)` vedle `Timestamp`Wordu místo:

* `(abc)`, která označuje, že Time Series Insights čte hodnoty dat jako řetězce.
* Ikona **kalendáře** , která indikuje, že Time Series Insights čte hodnotu dat jako DateTime.
* `#`, která označuje, že Time Series Insights čte hodnoty dat jako celé číslo.

Pokud není explicitně zadaná vlastnost timestamp, použije se jako výchozí časové razítko čas zařazování centra IoT nebo centra událostí.

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>Problém: nemůžeme zobrazit data z úložiště s mým teplem v Průzkumníkovi.

- Je možné, že jste v poslední době zřídili úložiště a data stále přecházejí do služby.
- Možná jste odstranili své teplé úložiště. v takovém případě byste ztratili data.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>Problém: Nemůžu zobrazit nebo upravit model časové řady

- Možná přistupujete k Time Series Insights prostředí S1 nebo S2.

   Modely časových řad se podporují jenom v prostředí s průběžnými platbami. Další informace o tom, jak získat přístup k prostředí S1 nebo S2 z Průzkumníka Time Series Insights Preview, najdete [v článku vizualizace dat v Průzkumníkovi](./time-series-insights-update-explorer.md).

   [v prostředí ![žádné události.](media/preview-troubleshoot/troubleshoot-no-events.png)](media/preview-troubleshoot/troubleshoot-no-events.png#lightbox)

- Možná nemáte oprávnění k zobrazení a úpravě modelu.

   Uživatelé potřebují přístup na úrovni přispěvatele pro úpravy a zobrazení modelu časové řady. Pokud chcete ověřit aktuální úrovně přístupu a udělit další přístup, přejděte do části **zásady přístupu k datům** v prostředku Time Series Insights v Azure Portal.

## <a name="problem-all-my-instances-in-the-preview-explorer-lack-a-parent"></a>Problém: všechny moje instance v Průzkumníkovi Preview nemají nadřazený prvek

K tomuto problému může dojít, pokud vaše prostředí nemá definovánu hierarchii modelu časové řady. Další informace najdete v článku [práce s modely časových řad](./time-series-insights-update-how-to-tsm.md).

  [Při ![nenadřazených instancí se zobrazí upozornění.](media/preview-troubleshoot/unparented-instances.png)](media/preview-troubleshoot/unparented-instances.png#lightbox)

## <a name="next-steps"></a>Další kroky

- Přečtěte si téma [práce s modely časových řad](./time-series-insights-update-how-to-tsm.md).

- Přečtěte si o [podporovaných tvarech JSON](./how-to-shape-query-json.md).

- Přečtěte si téma [plánování a omezení](./time-series-insights-update-plan.md) ve verzi Preview Azure Time Series Insights.
