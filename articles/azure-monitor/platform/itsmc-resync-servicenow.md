---
title: Ruční oprava problémů s synchronizací ServiceNow
description: Resetujte připojení na ServiceNow, takže výstrahy v Microsoft Azure můžou znovu volat ServiceNow.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: a3382f93990612b0ab34eb0848cbf3d6577c44ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87087930"
---
# <a name="how-to-manually-fix-servicenow-sync-problems"></a>Ruční oprava problémů s synchronizací ServiceNow

Azure Monitor se můžou připojit k poskytovatelům IT služeb třetích stran (ITSM). ServiceNow je jedním z těchto poskytovatelů.

Z bezpečnostních důvodů možná budete muset aktualizovat ověřovací token, který se používá pro připojení ke službě ServiceNow.
K opětovné aktivaci připojení a aktualizaci tokenu použijte následující proces synchronizace:


1. Vyhledejte řešení v horním banneru hledání a pak vyberte relevantní řešení.

    ![Nové připojení](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. Na obrazovce řešení zvolte v filtru předplatného možnost Vybrat vše a potom filtr podle "partnera".

    ![Nové připojení](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. Vyberte řešení pro připojení ITSM.
1. V nápisu vlevo vyberte připojení ITSM.

    ![Nové připojení](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. Vyberte jednotlivé konektory ze seznamu. 
    1. Klikněte na název konektoru, aby se nakonfiguroval.
    1. Odstranit všechny konektory, které se už nepoužívají

    1. Aktualizujte pole podle [těchto definic](./itsmc-connections.md) na základě typu partnera.

    1. Klikněte na synchronizovat.

       ![Nové připojení](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Klikněte na Uložit.

        ![Nové připojení](media/itsmc-resync-servicenow/save-8bit.png)

f.    Zkontrolujte oznámení a zjistěte, jestli proces skončil s úspěchem. 

## <a name="next-steps"></a>Další kroky

Další informace o [připojeních správy služeb IT](itsmc-connections.md)
