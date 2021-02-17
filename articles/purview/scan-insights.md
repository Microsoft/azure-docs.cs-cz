---
title: Prohledání vašich dat v Azure dosah
description: V této příručce se dozvíte, jak zobrazit a používat sestavy dosah Insights pro kontrolu vašich dat.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: 7807659a30127f39bb79ad99bdb733c12eb1d25d
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2021
ms.locfileid: "100548663"
---
# <a name="scan-insights-on-your-data-in-azure-purview"></a>Prohledání vašich dat v Azure dosah

Tato příručka popisuje, jak získat přístup, zobrazit a filtrovat sestavy služby Azure dosah Scan Insight pro vaše data.

V této příručce se dozvíte, jak:

> [!div class="checklist"]
> * Podívejte se na přehledy z vašeho účtu dosah.
> * Získejte pohled na vaše kontroly na pohled z očí.

## <a name="prerequisites"></a>Požadavky

Než začnete s dosah přehledy, ujistěte se, že jste dokončili následující kroky:

* Nastavte prostředky Azure a naplňte účet daty.
* Nastavte a dokončete kontrolu na zdroji dat.

Další informace najdete v tématu [Správa zdrojů dat ve službě Azure dosah (Preview)](manage-data-sources.md).

## <a name="use-purview-scan-insights"></a>Použití dosah Scan Insights

V Azure dosah můžete registrovat a kontrolovat zdrojové typy. Stav kontroly můžete zobrazit v průběhu času v přehledech kontroly. Přehledy vám poskytnou počet neúspěšných a úspěšných kontrol nebo zrušených v určitém časovém období.

### <a name="view-scan-insights"></a>Zobrazení přehledů prohledávání

1. Přejít na obrazovku **Azure dosah** instance v Azure Portal a vyberte svůj účet dosah.

1. Na stránce **Přehled** **v části Začínáme** vyberte dlaždici **otevřít dosah Studio** .

   :::image type="content" source="./media/scan-insights/portal-access.png" alt-text="Spustit dosah z Azure Portal":::

1. Na **domovské** stránce dosah vyberte dlaždici **Zobrazit přehledy** , abyste měli přístup k oblasti **Insights** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: .

   :::image type="content" source="./media/scan-insights/view-insights.png" alt-text="Podívejte se na přehledy v Azure Portal":::

1. V oblasti **přehledy** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: Vyberte možnost **kontroly** . zobrazí se sestava dosah **Scan Insights** .

### <a name="view-high-level-kpis-to-show-count-of-scans-by-status-and-deep-dive-into-each-scan"></a>Zobrazit klíčové ukazatele výkonu a zobrazit tak počet kontrol podle stavu a hloubky podrobně do každé kontroly
 
1. Klíčové ukazatele výkonu zobrazují celkový počet kontrol spouštěných v rámci určitého období. Časové období je ve výchozím nastavení v posledních 30 dnech. Můžete ale také vybrat posledních sedm dní. Na základě filtru času hodnoty klíčových ukazatelů výkonu odrážejí odpovídající počet kontrol.


1. V závislosti na vybrané hodnotě filtru času můžete zobrazit distribuci úspěšných, neúspěšných a zrušených kontrol podle týdnů nebo dnů v grafu.

1. V dolní části grafu je k dispozici odkaz **Zobrazit další informace** , který můžete prozkoumat. Odkaz otevře stránku  **stavu prohledávání** v rámci možnosti kontroly přehledů. Tady vidíte název kontroly a počet pokusů o jeho úspěch, selhání nebo zrušení v posledních 30 dnech.

    :::image type="content" source="./media/scan-insights/main-graph.png" alt-text="Zobrazit stav kontroly v čase":::

4. Konkrétní kontrolu můžete prozkoumat ještě tak, že kliknete na **název kontroly** , který vás připojí k historii prohledávání v rámci zkušeností **zdrojů** Azure dosah. Na stránce historie spuštění můžete získat ID spuštění, které vám pomůže při dalším zkoumání selhání.

    :::image type="content" source="./media/scan-insights/scan-status.png" alt-text="Zobrazit podrobnosti o prověřování":::

5. Nakonec se můžete vrátit na stránku **stav** kontroly přehledů vyhledávání pomocí crumbs chleba v levém horním rohu stránky historie spuštění.

    :::image type="content" source="./media/scan-insights/scan-history.png" alt-text="Zobrazení historie prohledávání"::: 

## <a name="next-steps"></a>Další kroky

* Další informace o Azure dosah **Insights** pomocí služby [data Insights](./concept-insights.md)

* Další informace o zkušenostech se **zdroji** dat Azure dosah a [Správa zdrojů dat](./manage-data-sources.md)