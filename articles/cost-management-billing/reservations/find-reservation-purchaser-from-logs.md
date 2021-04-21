---
title: Najít nákupčí rezervace z Azure Monitorch protokolů
description: Tento článek vám pomůže najít nákupčí rezervace s informacemi z protokolů Azure Monitor.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: troubleshooting
ms.date: 03/13/2021
ms.author: banders
ms.openlocfilehash: 965e90eed0690d57b6ad3cf3a1543b1329c0fe74
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773367"
---
# <a name="find-a-reservation-purchaser-from-azure-logs"></a>Vyhledání nákupu rezervací z protokolů Azure

Tento článek vám pomůže najít nákupčí rezervace s informacemi z protokolů adresáře. Protokol adresáře z Azure Monitor zobrazuje ID e-mailu uživatelů, kteří provedli nákupy rezervací.

## <a name="find-the-purchaser"></a>Najít kupujícího

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Přejděte na **sledování**  >  **aktivity protokolu aktivit**  >  .  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" alt-text="Snímek obrazovky znázorňující navigaci k aktivitě protokolu aktivit" lightbox="./media/find-reservation-purchaser-from-logs/activity-log-activity.png" :::
1. Vyberte **aktivita adresáře**. Pokud se zobrazí zpráva s oznámením, *že potřebujete oprávnění k zobrazení protokolů na úrovni adresáře*, vyberte [odkaz](../../role-based-access-control/elevate-access-global-admin.md) , kde se dozvíte, jak získat oprávnění.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" alt-text="Snímek obrazovky zobrazující aktivitu adresáře bez oprávnění k zobrazení protokolu" lightbox="./media/find-reservation-purchaser-from-logs/directory-activity-no-permission.png" :::
1. Jakmile budete mít oprávnění, vyfiltrujte **poskytovatele prostředků tenanta** pomocí **Microsoft. Capacity**. Pro vybrané časové období by se měly zobrazit všechny události související s rezervací. V případě potřeby změňte časový rozsah.  
    :::image type="content" source="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" alt-text="Snímek obrazovky zobrazující uživatele, který rezervaci zakoupil." lightbox="./media/find-reservation-purchaser-from-logs/user-that-purchased-reservation.png" :::
    V případě potřeby možná budete muset **Upravit sloupce** a vybrat **událost iniciovaná**.
   Uživatel, který provedl nákup rezervace, se zobrazí v části **událost iniciovaná nástrojem**.

## <a name="next-steps"></a>Další kroky

- V případě potřeby můžou správci fakturace [převzít vlastnictví rezervace](view-reservations.md#how-billing-administrators-can-view-or-manage-reservations).
