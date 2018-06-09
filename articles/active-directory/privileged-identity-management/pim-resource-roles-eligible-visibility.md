---
title: Oprávněné přiřazení a viditelnost prostředku pro Azure v Privileged Identity managementu | Microsoft Docs
description: Popisuje, jak přiřadit členy jako vhodné pro role prostředků při použití PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: c100cf5421539c985b8132bfd8af59ed0ac2e01c
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35233420"
---
# <a name="eligible-assignments-and-resource-visibility-with-privileged-identity-management"></a>Oprávněné přiřazení a viditelnost prostředku s Privileged Identity Management

Privileged Identity Management (PIM) pro role prostředků Azure poskytuje vylepšené zabezpečení pro organizace, které mají důležité prostředky Azure. Správci prostředků můžete používat PIM přiřadit členy jako vhodné pro role prostředků. Další informace o přiřazení různé typy a stavy přiřazení rolí prostředků Azure v následujících částech. 

## <a name="assignment-types"></a>Přiřazení typy

PIM pro prostředky Azure nabízí dva typy odlišné přiřazení:

- Oprávnění
- Aktivní

Oprávněné přiřazení vyžadují členem role provést akci, kterou chcete použít roli. Akce mohou zahrnovat úspěšné kontrolu služby Multi-Factor authentication, poskytuje obchodního oprávnění nebo požaduje schválení ze schvalovatelů určené.

Aktivní přiřazení nevyžadují člen provádět veškeré akce použít roli. Členové přiřazen jako aktivní mají oprávnění přiřazena k roli za všech okolností.

## <a name="assignment-duration"></a>Doba trvání přiřazení

Správci prostředků můžete vybrat z dvě možnosti pro každý typ přiřazení při konfiguraci nastavení PIM pro roli. Tyto možnosti se výchozí maximální doba, když je členem přiřazení role v PIM. 

Může správce zvolit jednu z těchto typů přiřazení:

- Povolit trvalé přiřazení oprávnění
- Povolit trvalé aktivní přiřazení

Nebo může správce zvolit jednu z těchto typů přiřazení:

- Oprávněné přiřazení po vypršení platnosti
- Ukončit platnost aktivních přiřazení po

Pokud se rozhodne správce prostředků **povolit trvalého přiřazení oprávněné** nebo **povolit trvalého přiřazení active**, všechny správce, kteří členové přiřadit prostředku můžete přiřadit trvalé členství ve skupinách.

Pokud se rozhodne správce prostředků **vyprší oprávněné přiřazení po** nebo **vyprší active přiřazení po**, správce prostředků řídí životního cyklu přiřazení vyžadováním, všechny přiřazení mít zadaný počáteční a koncové datum.

> [!NOTE] 
> Všechna přiřazení, které mají zadané koncové datum můžete obnovit ve Správci prostředků. Navíc členy můžete zahájit samoobslužných žádostí k [rozšířit nebo obnovení přiřazení](pim-resource-roles-renew-extend.md).


## <a name="assignment-states"></a>Přiřazení stavy

PIM pro prostředky Azure má dva odlišné přiřazení stavy, které se zobrazují na **aktivní role** ve **Moje role**, **role**, a **členy**zobrazení PIM. Tyto stavy jsou:

- Přiřazené
- Aktivováno

Při prohlížení členství, která je uvedena v **aktivní role**, můžete použít hodnotu v **stavu** sloupec k rozlišení mezi uživatele, kteří jsou **přiřazeno** jako aktivní a Uživatelé, **aktivované** oprávněné přiřazení a jsou teď aktivní.

## <a name="next-steps"></a>Další postup

[Přiřazení role v Privileged Identity Manager](pim-resource-roles-assign-roles.md)
