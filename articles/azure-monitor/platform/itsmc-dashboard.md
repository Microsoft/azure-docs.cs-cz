---
title: Prozkoumání chyb pomocí řídicího panelu ITSMC
description: Naučte se používat řídicí panel IT Service Management Connector k prozkoumání chyb.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: 5cbd501b9ccb408ee23cb5c8ed9cde2689ef79fb
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492531"
---
# <a name="investigate-errors-by-using-the-itsmc-dashboard"></a>Prozkoumání chyb pomocí řídicího panelu ITSMC

Tento článek obsahuje informace o řídicím panelu ITSMC (IT Service Management Connector). Řídicí panel vám pomůže prozkoumat stav konektoru.

## <a name="view-errors"></a>Zobrazit chyby

Zobrazení chyb na řídicím panelu:

1. Vyberte **všechny prostředky** a pak vyhledejte **partnera (*název pracovního prostoru*)**.

   ![Snímek obrazovky, který zobrazuje prostředky ve službách Azure.](media/itsmc-definition/create-new-connection-from-resource.png)

2. V části **zdroje dat pracovního prostoru** v levém podokně vyberte **připojení ITSM**:

   ![Snímek obrazovky zobrazující výběr připojení ITSM v části zdroje dat na pracovišti](media/itsmc-overview/add-new-itsm-connection.png)

3. V části **Souhrn** v oblasti **Service Management Connector** vyberte **Zobrazit souhrn**:

   ![Snímek obrazovky zobrazující tlačítko Zobrazit souhrn](media/itsmc-resync-servicenow/dashboard-view-summary.png)

4. Po zobrazení grafu v oblasti **IT Connector pro IT** vyberte tuto možnost:

   ![Snímek obrazovky, který zobrazuje výběr grafu](media/itsmc-resync-servicenow/dashboard-graph-click.png)

5. Zobrazí se řídicí panel. Použijte ho ke kontrole stavu a chyb v konektoru.
   
   ![Snímek obrazovky zobrazující stav konektoru na řídicím panelu](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="understand-dashboard-elements"></a>Principy prvků řídicího panelu

Řídicí panel obsahuje informace o výstrahách, které byly odeslány do nástroje ITSM prostřednictvím tohoto konektoru. Řídicí panel je rozdělen na čtyři části.

### <a name="created-work-items"></a>Vytvořené pracovní položky 

V oblasti **vytvořené pracovní položky** je v grafu a v tabulce níže obsažen počet pracovních položek na typ. Pokud vyberete graf nebo tabulku, můžete zobrazit další podrobnosti o pracovních položkách.

![Snímek obrazovky, který zobrazuje vytvořenou pracovní položku.](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)

### <a name="affected-computers"></a>Ovlivněné počítače 

V oblasti **ovlivněné počítače** v tabulce jsou uvedeny počítače a jejich přidružené pracovní položky. Když vyberete řádky v tabulkách, můžete získat další podrobnosti o počítačích.

Tabulka obsahuje omezený počet řádků. Pokud chcete zobrazit všechny řádky, vyberte **Zobrazit vše**.

![Snímek obrazovky zobrazující ovlivněné počítače](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)

### <a name="connector-status"></a>Stav konektoru 

V oblasti **stav konektoru** se v grafu a tabulce nacházejí zprávy o stavu konektoru. Když vyberete graf nebo řádky v tabulce, můžete získat další podrobnosti o zprávách.

Tabulka obsahuje omezený počet řádků. Pokud chcete zobrazit všechny řádky, vyberte **Zobrazit vše**.

Další informace o zprávách v tabulce najdete v [tomto článku](itsmc-dashboard-errors.md).

![Snímek obrazovky zobrazující stav konektoru](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)

### <a name="alert-rules"></a>Pravidla upozornění 

V oblasti **pravidla výstrahy** obsahuje tabulka informace o počtu zjištěných pravidel upozornění. Když vyberete řádky v tabulce, můžete získat další podrobnosti o zjištěných pravidlech.
    
Tabulka obsahuje omezený počet řádků. Pokud chcete zobrazit všechny řádky, vyberte **Zobrazit vše**.

![Snímek obrazovky, který zobrazuje pravidla výstrah.](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)
