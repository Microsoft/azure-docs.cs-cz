---
title: Diagnostika a řešení potíží s prostředím Gen2 – Azure Time Series Insights | Microsoft Docs
description: Naučte se diagnostikovat a řešit potíže s Azure Time Series Insightsm prostředím Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: d9dd07e3a35d83ff6bd9c7c493768d1197667c39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98108785"
---
# <a name="diagnose-and-troubleshoot-an-azure-time-series-insights-gen2-environment"></a>Diagnostika a řešení potíží s prostředím Azure Time Series Insights Gen2

Tento článek shrnuje několik běžných problémů, se kterými se můžete setkat při práci s prostředím Azure Time Series Insights Gen2. Tento článek také popisuje možné příčiny a řešení jednotlivých problémů.

## <a name="problem-i-cant-find-my-environment-in-the-gen2-explorer"></a>Problém: nemůžu najít své prostředí v Průzkumníkovi Gen2

K tomuto problému může dojít, pokud nemáte oprávnění pro přístup k Time Series Insights prostředí. Uživatelé potřebují pro zobrazení svého Time Series Insightsho prostředí roli přístupu na úrovni čtenáře. Chcete-li ověřit aktuální úrovně přístupu a udělit další přístup, přejděte do části **zásady přístupu k datům** v prostředku Time Series Insights v [Azure Portal](https://portal.azure.com/).

  [![Ověřte zásady přístupu k datům.](media/preview-troubleshoot/verify-data-access-policies.png)](media/preview-troubleshoot/verify-data-access-policies.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-gen2-explorer"></a>Problém: v Průzkumníkovi Gen2 se nezobrazí žádná data.

K dispozici je několik běžných důvodů, proč se vaše data nemusí zobrazit v [Azure Time Series Insights Průzkumníku Gen2](https://insights.timeseries.azure.com/preview).

- Váš zdroj událostí pravděpodobně nepřijímá data.

    Ověřte, že váš zdroj událostí, což je centrum událostí nebo centrum IoT, přijímá data z vašich značek nebo instancí. Pokud to chcete ověřit, na stránce s přehledem na svém prostředku navštivte Azure Portal.

    [![Projděte si přehled metriky řídicího panelu.](media/preview-troubleshoot/verify-dashboard-metrics.png)](media/preview-troubleshoot/verify-dashboard-metrics.png#lightbox)

- Vaše zdrojová data události nejsou ve formátu JSON.

    Time Series Insights podporuje pouze data JSON. V případě ukázek JSON čtěte [podporované tvary JSON](./concepts-json-flattening-escaping-rules.md).

- V klíči zdroje událostí chybí požadovaná oprávnění.

  - V případě služby IoT Hub musíte zadat klíč, který má oprávnění **připojit ke službě** .

    [![Ověřte oprávnění pro IoT Hub.](media/preview-troubleshoot/verify-correct-permissions.png)](media/preview-troubleshoot/verify-correct-permissions.png#lightbox)

    - Zásady **iothubowner** **i fungují,** protože mají oprávnění **k připojení ke službě** .

  - V centru událostí je nutné zadat klíč, který má oprávnění k **naslouchání** .
  
    [![Zkontrolujte oprávnění centra událostí.](media/preview-troubleshoot/verify-eh-permissions.png)](media/preview-troubleshoot/verify-eh-permissions.png#lightbox)

    - Zásady **číst** i **Spravovat** budou fungovat, protože mají oprávnění k **naslouchání** .

- Zadaná skupina uživatelů není výhradně Time Series Insights.

    Během registrace centra IoT nebo centra událostí určíte skupinu uživatelů, která se používá ke čtení dat. Tato skupina uživatelů musí být jedinečná pro každé prostředí. Pokud je skupina uživatelů sdílená, příslušné centrum událostí automaticky odpojí jedno z čtecích zařízení s náhodným připojením. Poskytněte jedinečnou skupinu uživatelů, ze které se má Time Series Insights číst.

- Vaše vlastnost ID časové řady zadaná v době zřizování není správná, chybí nebo je null.

    K tomuto problému může dojít, pokud je vlastnost ID časové řady v době zřizování prostředí nesprávně nakonfigurovaná. Další informace najdete v článku [osvědčené postupy pro výběr ID časové řady](./how-to-select-tsid.md). V tuto chvíli nemůžete aktualizovat existující prostředí Time Series Insights tak, aby používalo jiné ID časové řady.

## <a name="problem-some-data-shows-but-some-is-missing"></a>Problém: některá data jsou zobrazená, ale chybí.

Můžete odesílat data bez ID časové řady.

- K tomuto problému může dojít při odesílání událostí bez pole ID časové řady v datové části. Další informace najdete v článku [podporované tvary JSON](./concepts-json-flattening-escaping-rules.md).
- K tomuto problému může dojít, protože vaše prostředí je omezené.

    > [!NOTE]
    > V tuto chvíli Time Series Insights podporuje maximální rychlost přijímání dat 1 MB/s.

## <a name="problem-data-was-showing-but-now-ingestion-has-stopped"></a>Problém: zobrazila se data, ale teď se zastavilo přijímání.

- Je možné, že se váš zdrojový klíč události znovu vygeneroval a vaše prostředí Gen2 potřebuje nový zdrojový klíč události.

K tomuto problému dochází, pokud klíč poskytnutý při vytváření zdroje událostí již není platný. V Time Series Insights se zobrazila telemetrie, ale nepřišly žádné příchozí zprávy. Pokud si nejste jistí, jestli se klíč znovu vygeneroval, můžete v Event Hubs "protokolu aktivit" vytvořit nebo aktualizovat autorizační pravidla oboru názvů "nebo" vytvořit nebo aktualizovat prostředek IotHub "pro Centrum IoT.

Pokud chcete v prostředí Time Series Insights Gen2 aktualizovat nový klíč, otevřete v Azure Portal svůj prostředek centra a zkopírujte nový klíč. Přejděte ke svému prostředku TSI a klikněte na zdroje událostí.

   [![Snímek obrazovky s položkou nabídky zdroje událostí, která se nazývá, se zobrazí jako prostředek.](media/preview-troubleshoot/update-hub-key-step-1.png)](media/preview-troubleshoot/update-hub-key-step-1.png#lightbox)

Vyberte zdroje událostí, ze kterých se zastaví přijímání, a vložte je do nového klíče a klikněte na Uložit.

   [![Snímek obrazovky ukazuje prostředek T S, u kterého je zadaný klíč zásad rozbočovače v-T.](media/preview-troubleshoot/update-hub-key-step-2.png)](media/preview-troubleshoot/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>Problém: název vlastnosti časového razítka zdroje události nefunguje

Ujistěte se, že název a hodnota odpovídají následujícím pravidlům:

- V názvu vlastnosti časového razítka se rozlišují malá a velká písmena.
- Hodnota vlastnosti časového razítka, která pochází ze zdroje události jako řetězec JSON, má formát `yyyy-MM-ddTHH:mm:ss.FFFFFFFK` . Příklad takového řetězce je `"2008-04-12T12:53Z"` .

Nejjednodušší způsob, jak zajistit, aby byl název vlastnosti časového razítka zachycen a správně fungoval, je použít Time Series Insights Průzkumníku Gen2. V Time Series Insights Průzkumníku Gen2 pomocí grafu vyberte časový úsek, po kterém jste zadali název vlastnosti časového razítka. Klikněte pravým tlačítkem na výběr a vyberte možnost **prozkoumat události** . Záhlaví prvního sloupce je název vlastnosti časového razítka. Místo toho by mělo být `($ts)` vedle slova `Timestamp` :

- `(abc)`, což znamená, že Time Series Insights čte hodnoty dat jako řetězce.
- Ikona **kalendáře** , která indikuje, že Time Series Insights čte hodnotu dat jako DateTime.
- `#`, což označuje, že Time Series Insights čte hodnoty dat jako celé číslo.

Pokud není explicitně zadaná vlastnost timestamp, použije se jako výchozí časové razítko čas zařazování centra IoT nebo centra událostí.

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>Problém: nemůžeme zobrazit data z úložiště s mým teplem v Průzkumníkovi.

- Je možné, že jste v poslední době zřídili úložiště a data stále přecházejí do služby.
- Možná jste odstranili své teplé úložiště. v takovém případě byste ztratili data.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>Problém: Nemůžu zobrazit nebo upravit model časové řady

- Možná přistupujete k Time Series Insights prostředí S1 nebo S2.

   Modely časových řad se podporují jenom v prostředí s průběžnými platbami. Další informace o tom, jak přistupovat k prostředí S1 nebo S2 z Time Series Insights Průzkumníku Gen2, najdete [v článku vizualizace dat v Průzkumníkovi](./concepts-ux-panels.md).

   [![V prostředí nejsou žádné události.](media/preview-troubleshoot/troubleshoot-no-events.png)](media/preview-troubleshoot/troubleshoot-no-events.png#lightbox)

- Možná nemáte oprávnění k zobrazení a úpravě modelu.

   Uživatelé potřebují přístup na úrovni přispěvatele pro úpravy a zobrazení modelu časové řady. Pokud chcete ověřit aktuální úrovně přístupu a udělit další přístup, přejděte do části **zásady přístupu k datům** v prostředku Time Series Insights v Azure Portal.

## <a name="problem-all-my-instances-in-the-gen2-explorer-lack-a-parent"></a>Problém: všechny moje instance v Průzkumníkovi Gen2 nemají nadřazený objekt.

K tomuto problému může dojít, pokud vaše prostředí nemá definovánu hierarchii modelu časové řady. Další informace najdete v článku o [práci s modely časových řad](./time-series-insights-overview.md).

  [![Nenadřazené instance zobrazí upozornění.](media/preview-troubleshoot/unparented-instances.png)](media/preview-troubleshoot/unparented-instances.png#lightbox)

## <a name="next-steps"></a>Další kroky

- Přečtěte si informace o tom, jak [pracovat s modely časových řad](./time-series-insights-overview.md).

- Přečtěte si o [podporovaných tvarech JSON](./concepts-json-flattening-escaping-rules.md).

- Zkontrolujte [plánování a omezení](./how-to-plan-your-environment.md) v Azure Time Series Insights Gen2.