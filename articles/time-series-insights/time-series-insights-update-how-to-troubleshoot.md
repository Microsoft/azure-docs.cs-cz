---
title: Diagnostika a řešení potíží s prostředím Preview – Přehledy Azure Time Series | Dokumenty společnosti Microsoft
description: Přečtěte si, jak diagnostikovat a řešit problémy s prostředím Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 667dee6365f38ae058e91c61c24838d8912df26a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80152640"
---
# <a name="diagnose-and-troubleshoot-a-preview-environment"></a>Diagnostika a řešení potíží s prostředím Preview

Tento článek shrnuje několik běžných problémů, se kterými se můžete setkat při práci s prostředím Azure Time Series Insights Preview. Článek také popisuje potenciální příčiny a řešení pro každý problém.

## <a name="problem-i-cant-find-my-environment-in-the-preview-explorer"></a>Problém: V průzkumníku náhledu nelze najít své prostředí

K tomuto problému může dojít, pokud nemáte oprávnění k přístupu k prostředí Time Series Insights. Uživatelé potřebují přístupovou roli na úrovni čtečky, aby viděli své prostředí Time Series Insights. Pokud chcete ověřit aktuální úrovně přístupu a udělit další přístup, přejděte do části **Zásady přístupu k datům** v prostředku Time Series Insights na [webu Azure Portal](https://portal.azure.com/).

  [![Ověřte zásady přístupu k datům.](media/preview-troubleshoot/verify-data-access-policies.png)](media/preview-troubleshoot/verify-data-access-policies.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-preview-explorer"></a>Problém: V průzkumníku náhledu se nezobrazí žádná data.

Existuje několik běžných důvodů, proč se vaše data nemusí zobrazit v [průzkumníku Náhled Přehledy Azure Time Series](https://insights.timeseries.azure.com/preview).

- Zdroj události pravděpodobně nepřijímá data.

    Ověřte, zda zdroj událostí, který je centrem událostí nebo službou IoT hub, přijímá data z vašich značek nebo instancí. Pokud o tom chcete ověřit, přejděte na stránku s přehledem vašeho prostředku na webu Azure Portal.

    [![Zkontrolujte přehled metrik řídicího panelu.](media/preview-troubleshoot/verify-dashboard-metrics.png)](media/preview-troubleshoot/verify-dashboard-metrics.png#lightbox)

- Data zdroje událostí nejsou ve formátu JSON.

    Time Series Insights podporuje pouze data JSON. Ukázky JSON načtu [podporované obrazce JSON](./how-to-shape-query-json.md).

- Ve zdrojovém klíči události chybí požadované oprávnění.

  * Pro službu IoT hub je třeba zadat klíč, který má oprávnění **připojení služby.**

    [![Ověřte oprávnění centra IoT.](media/preview-troubleshoot/verify-correct-permissions.png)](media/preview-troubleshoot/verify-correct-permissions.png#lightbox)

    * Zásady **iothubowner** a **servisní** práce, protože mají oprávnění **připojení služby.**

  * Pro centrum událostí je třeba zadat klíč, který má oprávnění **naslouchat.**
  
    [![Zkontrolujte oprávnění centra událostí.](media/preview-troubleshoot/verify-eh-permissions.png)](media/preview-troubleshoot/verify-eh-permissions.png#lightbox)

    * **Zásady čtení** i **správy** fungují, protože mají oprávnění **naslouchat.**

- Poskytnutá spotřebitelská skupina není výhradní pro Time Series Insights.

    Při registraci centra IoT hub nebo centra událostí zadáte skupinu spotřebitelů, která se používá ke čtení dat. Tato skupina spotřebitelů musí být jedinečná pro prostředí. Pokud je skupina spotřebitelů sdílena, základní centrum událostí automaticky odpojí jednoho z čtenářů náhodně. Poskytněte jedinečnou skupinu spotřebitelů, ze které si můžete číst přehledy time series.

- Vaše ID časové řady vlastnost zadaná v době zřizování je nesprávná, chybějící nebo null.

    K tomuto problému může dojít, pokud id časové řady vlastnost je nakonfigurován nesprávně v době zřizování prostředí. Další informace naleznete v [doporučených postupech pro výběr ID časové řady](./time-series-insights-update-how-to-id.md). V tuto chvíli nelze aktualizovat existující prostředí Time Series Insights použít jiné ID časové řady.

## <a name="problem-some-data-shows-but-some-is-missing"></a>Problém: Některá data se zobrazují, ale některá chybí

Je možné, že odesíláte data bez ID časové řady.

- K tomuto problému může dojít při odesílání událostí bez pole ID časové řady v datové části. Další informace naleznete [v dokumentu Podporované obrazce JSON](./how-to-shape-query-json.md).
- K tomuto problému může dojít, protože je omezené prostředí.

    > [!NOTE]
    > V tuto chvíli Time Series Insights podporuje maximální rychlost přijímání 6 Mb/s.

## <a name="problem-data-was-showing-but-now-ingestion-has-stopped"></a>Problém: Data se zobrazovala, ale nyní se požití zastavilo

- Klíč zdroje událostí byl pravděpodobně regenerován a prostředí Náhledu potřebuje nový klíč zdroje událostí.

K tomuto problému dochází, když klíč poskytnutý při vytváření zdroje událostí již není platný. Ve vašem centru se zobrazí telemetrická data, ale žádné příchozí přenosy přijatých zpráv v přehledech časové řady. Pokud si nejste jisti, zda byl klíč znovu vygenerován, můžete v protokolu aktivit centra událostí vyhledat "Vytvořit nebo aktualizovat autorizační pravidla oboru názvů" nebo hledat "Vytvořit nebo aktualizovat prostředek IotHub" pro službu IoT hub. 

Chcete-li aktualizovat prostředí Náhled time series insights pomocí nového klíče, otevřete svůj prostředek centra na webu Azure portal a zkopírujte nový klíč. Přejděte na zdroj TSI a klikněte na Zdroje událostí. 

   [![Aktualizovat klíč.](media/preview-troubleshoot/update-hub-key-step-1.png)](media/preview-troubleshoot/update-hub-key-step-1.png#lightbox)

Vyberte zdroj událostí, které mají, ze kterého bylo požití zastaveno, vložte nový klíč a klikněte na Uložit.

   [![Aktualizovat klíč.](media/preview-troubleshoot/update-hub-key-step-2.png)](media/preview-troubleshoot/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>Problém: Název vlastnosti časového razítka zdroje události nefunguje.

Ujistěte se, že název a hodnota odpovídají následujícím pravidlům:

* Název vlastnosti časového razítka rozlišuje malá a velká písmena.
* Hodnota vlastnosti Timestamp, která pochází ze zdroje událostí jako `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`řetězec JSON, má formát . Příkladem takového řetězce je `“2008-04-12T12:53Z”`.

Nejjednodušší způsob, jak zajistit, aby byl název vlastnosti časového razítka zachycen a správně fungoval, je použít průzkumník náhledu přehledů časové řady. V průzkumníku náhledu přehledů časové řady vyberte pomocí grafu časové období po zadání názvu vlastnosti časové razítko. Klikněte pravým tlačítkem myši na výběr a vyberte možnost **prozkoumat události.** Záhlaví prvního sloupce je název vlastnosti časového razítka. To by `($ts)` mělo mít `Timestamp`vedle slova , spíše než:

* `(abc)`, což znamená, že Time Series Insights čte hodnoty dat jako řetězce.
* Ikona **kalendáře,** která označuje, že Time Series Insights přečte hodnotu dat jako datetime.
* `#`, což znamená, že Time Series Insights čte hodnoty dat jako celé číslo.

Pokud vlastnost Časové razítko není explicitně zadána, použije se jako výchozí časové razítko centrum IoT hub nebo centrum událostí Enqueued Time.

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>Problém: Nelze zobrazit data z mého teplého úložiště v průzkumníku

- Možná jste nedávno zřídit i mizérové úložiště a data stále proudí.
- Je možné, že jste odstranili váš teplý obchod, v takovém případě byste ztratili data.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>Problém: Nemohu zobrazit nebo upravit model časové řady

- Možná přistupujete k prostředí Time Series Insights S1 nebo S2.

   Modely časových řad jsou podporovány pouze v prostředích s průběžnmi platbami. Další informace o tom, jak získat přístup k prostředí S1 nebo S2 z průzkumníka Náhled časových řad, najdete v části [Visualize data v průzkumníku](./time-series-insights-update-explorer.md).

   [![Žádné události v prostředí.](media/preview-troubleshoot/troubleshoot-no-events.png)](media/preview-troubleshoot/troubleshoot-no-events.png#lightbox)

- Pravděpodobně nemáte oprávnění k zobrazení a úpravám modelu.

   Uživatelé potřebují přístup na úrovni přispěvatele, aby mohly upravovat a zobrazovat svůj model časové řady. Pokud chcete ověřit aktuální úrovně přístupu a udělit další přístup, přejděte do části **Zásady přístupu k datům** v prostředku Time Series Insights na webu Azure Portal.

## <a name="problem-all-my-instances-in-the-preview-explorer-lack-a-parent"></a>Problém: Všechny mé instance v průzkumníku náhledu postrádají nadřazenou položku

K tomuto problému může dojít, pokud vaše prostředí nemá definována hierarchie modelu časové řady. Další informace naleznete v článek [Práce s modely časových řad](./time-series-insights-update-how-to-tsm.md).

  [![Nenadřazené instance zobrazí upozornění.](media/preview-troubleshoot/unparented-instances.png)](media/preview-troubleshoot/unparented-instances.png#lightbox)

## <a name="next-steps"></a>Další kroky

- Přečtěte si [článek Práce s modely časových řad](./time-series-insights-update-how-to-tsm.md).

- Informace o [podporovaných obrazcích JSON](./how-to-shape-query-json.md).

- Projděte si [plánování a omezení](./time-series-insights-update-plan.md) ve verzi Azure Time Series Insights Preview.
