---
title: Chyba při šetření pomocí řídicího panelu
description: Tento dokument obsahuje informace o chybě při šetření pomocí řídicího panelu.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: 9a43318ed935dd1e71b6da75c9f078fbebbd29eb
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599018"
---
# <a name="error-investigation-using-the-dashboard"></a>Chyba při šetření pomocí řídicího panelu

Tato stránka obsahuje informace o řídicím panelu konektoru ITSM. Tento řídicí panel vám pomůže prozkoumat stav konektoru ITSM.

## <a name="how-to-view-the-dashboard"></a>Jak zobrazit řídicí panel

Chcete-li zobrazit chyby na řídicím panelu, postupujte podle následujících kroků:

1. Ve **všech prostředcích** vyhledejte **partnera (*název vašeho pracovního prostoru*)**:

   ![Snímek obrazovky, který zobrazuje poslední prostředky v Azure Portal.](media/itsmc-definition/create-new-connection-from-resource.png)

2. V části **zdroje dat pracovního prostoru** v levém podokně vyberte **připojení ITSM**:

   ![Snímek obrazovky zobrazující položku nabídky připojení ITSM](media/itsmc-overview/add-new-itsm-connection.png)

3. V části **Souhrn** v levém okně **konektor správy služby IT** vyberte **Zobrazit souhrn**:

    ![Snímek obrazovky, který zobrazuje souhrn zobrazení](media/itsmc-resync-servicenow/dashboard-view-summary.png)

4. V části **Souhrn** v levém okně **konektor Service Management Connector** klikněte na graf:

    ![Snímek obrazovky znázorňující kliknutí na graf](media/itsmc-resync-servicenow/dashboard-graph-click.png)

5. Pomocí tohoto řídicího panelu budete moct zkontrolovat stav a chyby ve vašem konektoru.
    ![Snímek obrazovky zobrazující stav konektoru](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="dashboard-elements"></a>Prvky řídicího panelu

Řídicí panel obsahuje informace o výstrahách, které byly odeslány do nástroje ITSM pomocí tohoto konektoru.
Řídicí panel je rozdělen na čtyři části:

1. Pracovní položka vytvořena: graf a níže uvedená tabulka obsahují počet pracovních položek na typ. Pokud kliknete na graf nebo v tabulce, můžete zobrazit další podrobnosti o pracovních položkách.
    ![Snímek obrazovky, který ukazuje vytvořenou pracovní položku.](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)
2. Ovlivněné počítače: tabulky obsahují podrobné informace o položkách konfigurace, které vytvořily položky konfigurace.
    Kliknutím na řádky v tabulkách můžete získat další podrobnosti o položkách konfigurace.
    Tabulka obsahuje omezený počet řádků, pokud chcete zobrazit všechny seznamy, můžete kliknout na Zobrazit vše.
    ![Snímek obrazovky zobrazující ovlivněné počítače](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)
3. Stav konektoru: graf a níže uvedená tabulka obsahují zprávy o stavu konektoru. Kliknutím na graf na řádcích v tabulce můžete získat další podrobnosti o zprávách o stavu konektoru.
    Tabulka obsahuje omezený počet řádků, pokud chcete zobrazit všechny seznamy, můžete kliknout na Zobrazit vše.

    Můžete zobrazit podrobnosti o zprávách v tabulce – [tady](itsmc-dashboard-errors.md).

    ![Snímek obrazovky zobrazující stav konektoru](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)
4. Pravidla výstrah: tabulky obsahují informace o počtu zjištěných pravidel výstrahy.
    Kliknutím na řádky v tabulkách můžete získat další podrobnosti o zjištěných pravidlech.
    Tabulka obsahuje omezený počet řádků, pokud chcete zobrazit všechny seznamy, můžete kliknout na Zobrazit vše.
    ![Snímek obrazovky, který zobrazuje pravidla výstrah.](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)