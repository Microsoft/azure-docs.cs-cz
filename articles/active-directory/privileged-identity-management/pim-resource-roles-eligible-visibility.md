---
title: Privileged Identity Management pro prostředky Azure - oprávněné přiřazení a viditelnost prostředku | Microsoft Docs
description: Popisuje, jak přiřadit role prostředků členy jako vhodné.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 273b06c91d68a764fe814374c0eca6ed1698cc2e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="eligible-assignments-and-resource-visibility"></a>Oprávněné přiřazení a viditelnost prostředku

PIM pro role prostředků Azure poskytuje vylepšené zabezpečení pro organizace s kritické resoruces Azure. PIM umožňuje správci prostředků přiřadit členy jako způsobilých role prostředků. Další informace o přiřazení různé typy a stavy pro role prostředků Azure níže. 

## <a name="assignment-types"></a>Přiřazení typy

PIM pro prostředky Azure nabízí dva typy odlišné přiřazení:

- Oprávnění
- Aktivní

Oprávněné přiřazení vyžadují členem role provést akci, kterou chcete použít roli. Tyto akce mohou zahrnovat úspěšné kontrolu služby Multi-Factor Authentication, poskytování zarovnání do bloku a požaduje schválení ze schvalovatelů určené.

Aktivní přiřazení nevyžadují, aby člen provádět veškeré akce použít roli. Členové přiřazen jako aktivní mají oprávnění poskytované roli za všech okolností.

## <a name="assignment-duration"></a>Doba trvání přiřazení

Správci prostředků můžete zvolit jednu ze dvou možností pro každý typ přiřazení, při nastavení PIM v roli. Tyto možnosti se výchozí maximální doba, když je členem přiřazení role v PIM.

- Povolit trvalé oprávněné přiřazení
- Povolit aktivní trvalého přiřazení

nebo

- Oprávněné přiřazení po vypršení platnosti
- Aktivní přiřazení po vypršení platnosti

Pokud správce prostředků vybere možnost "Povolit trvalé oprávněné přiřazení" nebo "Povolit trvalé active přiřazení", všechny správce, kteří členové přiřadit prostředek bude mít přiřadit trvalé členství.

Volba "Oprávněné přiřazení pro vypršení platnosti po" nebo "Active přiřazení pro vypršení platnosti po" umožňuje kontrolu nad životním cyklu přiřazení tím, že vyžaduje, že všechna přiřazení zadaný počáteční a koncové datum.

>[!NOTE] 
>Všechna přiřazení s zadané koncové datum můžete obnovit ve Správci prostředků a členy můžete iniciovat samoobslužných žádostí k [rozšířit nebo obnovení přiřazení](pim-resource-roles-renew-extend.md).


## <a name="assignment-states"></a>Přiřazení stavy

PIM pro prostředky Azure má dva jedinečné přiřazení stavy, které se zobrazují na kartě "Aktivní role" v mé role, role a členy zobrazení PIM. Tyto stavy jsou:

- Přiřazené
- Aktivované

Při zobrazení členství uvedené v "Aktivní role" sloupci "Stavu" umožňuje rozlišit mezi uživatele, kteří jsou "Přiřazeno" jako aktivní a uživatelé tohoto "aktivované" oprávněné přiřazení a jsou teď aktivní.

## <a name="next-steps"></a>Další postup

[Přiřazení role v PIM](pim-resource-roles-assign-roles.md)

