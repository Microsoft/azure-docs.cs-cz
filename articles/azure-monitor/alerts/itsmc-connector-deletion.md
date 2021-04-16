---
title: Odstranit nepoužívané konektory ITSM
description: Tento článek popisuje, jak odstranit konektory ITSM a skupiny akcí, které jsou k němu přidružené.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: 3dc84ea6def9b762527226dbeb3e2eaab78ec200
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387934"
---
# <a name="delete-unused-itsm-connectors"></a>Odstranit nepoužívané konektory ITSM

Proces odstraňování nepoužívaných konektorů správa IT služeb (ITSM) má dvě fáze. Odstraníte všechny akce, které jsou přidruženy k ITSM konektoru, a pak odstraníte samotný konektor. Nejprve tyto akce odstraníte, protože akce bez konektoru můžou způsobit chyby ve vašem předplatném.

## <a name="delete-associated-actions"></a>Odstranit přidružené akce

1. V Azure Portal vyberte **monitorovat**.
  
    ![Snímek obrazovky výběru monitoru](media/itsmc-connector-deletion/itsmc-monitor-selection.png)

2. Vyberte **výstrahy**.
   
    ![Snímek obrazovky s výběrem výstrah.](media/itsmc-connector-deletion/itsmc-alert-selection.png)

3. Vyberte **Spravovat akce**.
   
    ![Snímek obrazovky s výběrem akcí správy](media/itsmc-connector-deletion/itsmc-actions-selection.png)

4. Vyberte skupinu akcí, která je přidružená k ITSM konektoru, který chcete odstranit. Tento článek používá příklad konektoru Cherwell.
   
    ![Snímek obrazovky s akcemi, které jsou přidruženy ke konektoru Cherwell](media/itsmc-connector-deletion/itsmc-actions-screen.png)

5. Zkontrolujte informace a pak vyberte **Odstranit skupinu akcí**.

    ![Snímek obrazovky s informacemi o skupině akcí a tlačítkem pro odstranění skupiny](media/itsmc-connector-deletion/itsmc-action-deletion.png)

## <a name="delete-the-connector"></a>Odstraní konektor.

1. Na panelu hledání vyhledejte **partnera**. Pak v seznamu prostředků vyberte **partnera** .

    ![Snímek obrazovky pro hledání a výběr partnera](media/itsmc-connector-deletion/itsmc-connector-selection.png)

2. Vyberte **připojení ITSM** a pak vyberte konektor Cherwell.

    ![Snímek obrazovky konektoru Cherwell I T S M](media/itsmc-connector-deletion/itsmc-cherwell-connector.png)

3. Vyberte **Odstranit**.

    ![Snímek obrazovky s tlačítkem odstranit pro konektor I T S M](media/itsmc-connector-deletion/itsmc-connector-deletion.png)

## <a name="next-steps"></a>Další kroky

* [Řešení problémů v konektoru ITSM](./itsmc-resync-servicenow.md)
