---
title: Oprávněné přiřazení a viditelnost prostředků Azure v Privileged Identity Management | Dokumentace Microsoftu
description: Popisuje, jak přiřazovat členy jako oprávněné pro role prostředků při použití PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 336453c1ef6ef8d0295d00f31afc6a5e7e42e8b6
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621544"
---
# <a name="eligible-assignments-and-resource-visibility-with-privileged-identity-management"></a>Oprávněné přiřazení a viditelnost prostředku s Privileged Identity Management

Privileged Identity Management (PIM) pro role prostředků Azure poskytuje vylepšené zabezpečení pro organizace, které mají důležité prostředky Azure. Správci prostředků můžete přiřazovat členy jako oprávněné pro role prostředků PIM. Další informace o typech různých přiřazení a stavy přiřazení pro role prostředků Azure v následujících částech. 

## <a name="assignment-types"></a>Typy přiřazení

PIM pro prostředky Azure poskytuje dva typy různých přiřazení:

- Oprávnění
- Aktivní

Oprávněnými přiřazeními vyžadují členem role provádět akci, kterou chcete použít roli. Akce může obsahovat buď úspěšné kontroly ověřování službou Multi-Factor Authentication, poskytuje obchodní odůvodnění nebo z určených schvalovatelů odesílání žádostí o schválení.

Aktivní přiřazení nevyžadují člen podnikat žádné kroky, chcete-li použít roli. Členové přiřazeno jako aktivní mají oprávnění přiřazená k roli za všech okolností.

## <a name="assignment-duration"></a>Doba trvání přiřazení

Správci prostředků můžete zvolit ze dvou možností pro každý typ přiřazení při konfiguraci nastavení PIM pro role. Tyto možnosti se výchozí maximální doba, kdy je člen přiřazeny k roli v PIM. 

Správce zvolit jednu z těchto typů přiřazení:

- Povolit trvalé přiřazení oprávnění
- Povolit trvalé aktivní přiřazení

Nebo může správce zvolit jednu z těchto typů přiřazení:

- Platnost oprávněných přiřazení po
- Ukončit platnost aktivních přiřazení po

Pokud správce prostředků **Povolit trvalé oprávněné přiřazení** nebo **Povolit trvalé aktivní přiřazení**, všechny správce, které přiřazení členů k prostředku přiřadit trvalé členství ve skupinách.

Pokud správce prostředků **platnost oprávněných přiřazení po** nebo **ukončit platnost aktivních přiřazení po**, určuje správce prostředků životního cyklu přiřazení tak, že vyžaduje, všechny přiřazení mít zadaný počáteční a koncové datum.

> [!NOTE] 
> Všechna přiřazení, které mají zadaného koncového data můžete obnovit ve Správci prostředků. Navíc členy můžete zahájit samoobslužných žádostí do [rozšíření nebo obnovení přiřazení](pim-resource-roles-renew-extend.md).


## <a name="assignment-states"></a>Stavy přiřazení

PIM pro prostředky Azure má dva odlišné přiřazení stavy, které se zobrazují na **aktivních rolí** kartu **Moje role**, **role**, a **členy**zobrazení PIM. Tyto stavy jsou:

- Přiřazené
- Aktivované

Při prohlížení členství, který je uveden v **aktivních rolí**, můžete použít hodnotu v **stavu** sloupec rozlišovat mezi uživatele, kteří jsou **přiřazeno** jako aktivní a Uživatelé, který **aktivováno** oprávněného přiřazení a jsou nyní aktivní.

## <a name="next-steps"></a>Další postup

[Přiřadit role v Privileged Identity Manager](pim-resource-roles-assign-roles.md)
