---
title: Prohledání vašich dat v Azure dosah
description: V této příručce se dozvíte, jak zobrazit a používat sestavy dosah Insights pro kontrolu vašich dat.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: 00f72e1de230cdc68f86010b7b25d86debaa5eb5
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575784"
---
# <a name="scan-insights-on-your-data-in-azure-purview"></a>Prohledání vašich dat v Azure dosah

Tato příručka popisuje, jak získat přístup, zobrazit a filtrovat sestavy služby Azure dosah Scan Insight pro vaše data.

V této příručce se dozvíte, jak:

> [!div class="checklist"]
> * Podívejte se na přehledy z vašeho účtu dosah.
> * Získejte pohled na vaše kontroly na pohled z očí.

## <a name="prerequisites"></a>Předpoklady

Než začnete s dosah přehledy, ujistěte se, že jste dokončili následující kroky:

* Nastavte prostředky Azure a naplňte účet daty.
* Nastavte a dokončete kontrolu na zdroji dat.

Další informace najdete v tématu [Správa zdrojů dat ve službě Azure dosah (Preview)](manage-data-sources.md).

## <a name="use-purview-scan-insights"></a>Použití dosah Scan Insights

V Azure dosah můžete registrovat a kontrolovat zdrojové typy. Stav kontroly můžete zobrazit v průběhu času v přehledech kontroly. Přehledy vám poskytnou počet neúspěšných a úspěšných kontrol nebo zrušených v určitém časovém období.

### <a name="view-scan-insights"></a>Zobrazit přehledy vyhledávání

1. Přejít na obrazovku **Azure dosah** instance v Azure Portal a vyberte svůj účet dosah.

1. Na stránce **Přehled** **v části Začínáme** vyberte dlaždici **otevřít dosah Studio** .

   :::image type="content" source="./media/scan-insights/portal-access.png" alt-text="Spustit dosah z Azure Portal":::

1. Na **domovské** stránce dosah vyberte dlaždici **Zobrazit přehledy** , abyste měli přístup k oblasti **Insights** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: .

   :::image type="content" source="./media/scan-insights/view-insights.png" alt-text="Podívejte se na přehledy v Azure Portal":::

1. V oblasti **přehledy** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: Vyberte možnost **kontroly** . zobrazí se sestava dosah **Scan Insights** .

### <a name="view-high-level-kpis-to-show-count-of-scans-by-status"></a>Zobrazit klíčové ukazatele výkonu pro zobrazení počtu kontrol podle stavu
 
Klíčové ukazatele výkonu zobrazují celkový počet kontrol spouštěných v rámci určitého období. Časové období je ve výchozím nastavení v posledních 30 dnech. Můžete ale také vybrat posledních sedm dní. Na základě filtru času hodnoty klíčových ukazatelů výkonu odrážejí odpovídající počet kontrol.


V závislosti na vybrané hodnotě filtru času můžete zobrazit distribuci úspěšných, neúspěšných a zrušených kontrol podle týdnů nebo dnů v grafu.

   :::image type="content" source="./media/scan-insights/scan-insights.png" alt-text="Zobrazit přehledy vyhledávání":::

## <a name="next-steps"></a>Další kroky

Další informace o sestavách Azure dosah Insight s [Asset Insights](./asset-insights.md)
