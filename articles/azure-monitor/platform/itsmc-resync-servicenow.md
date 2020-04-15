---
title: Ruční oprava problémů se synchronizací služby ServiceNow
description: Resetujte připojení k ServiceNow, aby výstrahy v Microsoft Azure mohly znovu volat ServiceNow.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: f09f5010c18f5ea064b02f0fbbae107bf473e1f8
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313667"
---
# <a name="how-to-manually-fix-servicenow-sync-problems"></a>Ruční oprava problémů se synchronizací služby ServiceNow

Azure Monitor se může připojit k poskytovatelům správy IT služeb (ITSM) od jiných výrobců. ServiceNow je jedním z těchto poskytovatelů.

Z bezpečnostních důvodů může být nutné aktualizovat ověřovací token používaný pro vaše připojení k ServiceNow.
K opětovné aktivaci připojení a aktualizaci tokenu použijte následující proces synchronizace:


1. Vyhledejte řešení v horní vyhledávací banner, pak vyberte příslušná řešení

    ![Nové připojení](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. Na obrazovce řešení zvolte ve filtru předplatného možnost Vybrat vše a poté filtrovat podle služby ServiceDesk.

    ![Nové připojení](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. Vyberte řešení připojení ITSM.
1. V levém banneru vyberte připojení ITSM.

    ![Nové připojení](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. Vyberte jednotlivé spojnice ze seznamu. 
    1. Chcete-li jej nakonfigurovat, klepněte na název konektoru.
    1. Odstranění všech konektorů, které se již nepoužívají

    1. Aktualizace polí podle [těchto definic](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-connections) na základě typu partnera

    1. Klikněte na synchronizaci

       ![Nové připojení](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Klikněte na uložit

        ![Nové připojení](media/itsmc-resync-servicenow/save-8bit.png)

f.    Zkontrolujte oznámení a zjistěte, zda byl proces dokončen s úspěchem 

## <a name="next-steps"></a>Další kroky

Další informace o [připojeních pro správu služeb IT](itsmc-connections.md)
