---
title: Prozkoumat chyby pomocí řídicího panelu
description: Tento dokument obsahuje informace o chybách na řídicím panelu ITSMC.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: ebd59e637e498b8088fe9a302b1bb12efdf2c173
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2021
ms.locfileid: "99089271"
---
# <a name="investigate-errors-by-using-the-itsmc-dashboard"></a>Prozkoumání chyb pomocí řídicího panelu ITSMC

Tento článek obsahuje informace o řídicím panelu ITSMC (IT Service Management Connector). Řídicí panel vám pomůže prozkoumat stav ITSMC.

## <a name="view-the-dashboard"></a>Zobrazení řídicího panelu

Pomocí těchto kroků otevřete řídicí panel.

1. Vyberte **všechny prostředky** a pak vyhledejte **partnera (*název pracovního prostoru*)**.

   ![Snímek obrazovky, který zobrazuje prostředky ve službách Azure.](media/itsmc-definition/create-new-connection-from-resource.png)

1. V levém podokně vyberte **zdroje dat pracovního prostoru** a pak vyberte **připojení ITSM**.

   ![Snímek obrazovky zobrazující výběr připojení ITSM v části zdroje dat na pracovišti](media/itsmc-overview/add-new-itsm-connection.png)

1. V části **Souhrn** vyberte **Zobrazit souhrn** pro zobrazení souhrnného grafu.

    ![Snímek obrazovky znázorňující možnost zobrazení souhrnu v části Souhrn](media/itsmc-resync-servicenow/dashboard-view-summary.png)

1. Výběrem grafu v části **Souhrn** otevřete řídicí panel.

    ![Snímek obrazovky, který zobrazuje výběr grafu souhrnu.](media/itsmc-resync-servicenow/dashboard-graph-click.png)

1. Zkontrolujte řídicí panel pro stav a případné chyby ve vašem konektoru.
    ![Snímek obrazovky zobrazující řídicí panel](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="understand-dashboard-elements"></a>Principy prvků řídicího panelu

Řídicí panel obsahuje informace o výstrahách, které byly odeslány do nástroje ITSM pomocí tohoto konektoru.

Řídicí panel je rozdělen na čtyři části:

- **Pracovní položky byly vytvořeny**: v grafu a tabulce se zobrazuje počet pracovních položek podle typu. Pokud chcete získat další informace o pracovních položkách, vyberte graf nebo tabulku.
      ![Snímek obrazovky se zobrazeným oddílem pracovní položky](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)
- **Ovlivněné počítače**: tabulka obsahuje podrobnosti o položkách konfigurace, které vytvořily pracovní položky.
      Kliknutím na řádky v tabulkách zobrazíte další podrobnosti o položkách konfigurace.
      Tabulka obsahuje omezený počet řádků. Pokud chcete zobrazit celý seznam, vyberte **Zobrazit vše**.
      ![Snímek obrazovky, který zobrazuje část ovlivněné počítače.](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)
- **Stav konektoru**: graf a tabulka obsahují informace o stavu konektoru. Kliknutím na graf nebo zprávy v tabulce zobrazíte další podrobnosti. V tabulce je zobrazen omezený počet řádků. Pokud chcete zobrazit celý seznam, vyberte **Zobrazit vše**.
      ![Snímek obrazovky zobrazující oddíl stav konektoru](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)
- **Pravidla výstrah**: v této části jsou uvedeny informace o počtu zjištěných pravidel výstrahy. Vyberte řádky v tabulkách, kde najdete další podrobnosti o zjištěných pravidlech. Tabulka má omezený počet řádků. Pokud chcete zobrazit celý seznam, vyberte **Zobrazit vše**.
      ![Snímek obrazovky, který zobrazuje část pravidla upozornění](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)

## <a name="next-steps"></a>Další kroky

Podívejte se na [běžné chyby stavu konektoru](itsmc-dashboard-errors.md).
