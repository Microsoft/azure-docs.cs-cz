---
title: Oprávněné přiřazení a viditelnost prostředku v PIM – Azure | Dokumentace Microsoftu
description: Popisuje, jak přiřazovat členy jako oprávněné pro role prostředků Azure v Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: 59d51ba8edadd1fd71255271623b144cab94fc97
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344279"
---
# <a name="eligible-assignments-and-resource-visibility-in-pim"></a>Oprávněné přiřazení a viditelnost prostředku v PIM

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

## <a name="azure-resource-role-approval-workflow"></a>Pracovní postup schválení pro role prostředků Azure

S schvalovací pracovní postup v PIM pro role prostředků Azure můžete správci dál chránit nebo omezit přístup k důležitým prostředkům. To znamená správci můžou k aktivaci vyžadovat schválení přiřazení rolí.

Koncept hierarchii prostředků je jedinečné pro role prostředků Azure. Díky této hierarchii mohou dědičnosti přiřazení rolí z nadřazeného objektu prostředku směrem dolů na všechny podřízené prostředky v rámci nadřazeného kontejneru. 

Příklad: Bob, správce prostředků, používá PIM přiřadit Alice jako oprávněného člena do role vlastník předplatného Contoso. Alice je tohoto přiřazení oprávnění vlastníka u všech kontejnerů skupiny prostředků v rámci předplatného Contoso. Alice je také vhodné vlastníkem všech prostředků (jako jsou virtuální počítače) v rámci jednotlivých skupin prostředků předplatného.

Předpokládejme, existují tři skupiny prostředků v předplatném Contoso: Fabrikam testu, vývojáře Fabrikam a Fabrikam Prod. Každá z těchto skupin prostředků obsahuje jeden virtuální počítač.

Nastavení PIM jsou nakonfigurována pro každou roli prostředku. Na rozdíl od přiřazení tato nastavení nejsou děděna a platí výhradně pro role prostředků.

Pokračování příkladu s: Bob používá PIM tak, aby vyžadovala všem členům v roli vlastník žádosti o schválení předplatného Contoso aktivovat. Pomáhá chránit prostředky ve skupině prostředků výrobní společnosti Fabrikam, Bob také vyžaduje schválení pro členy role vlastníka tohoto prostředku. Role vlastník ve společnosti Fabrikam testování a vývoj Fabrikam nevyžadují schválení pro aktivaci.

Když Alice žádosti o aktivaci její roli vlastníka předplatného Contoso, approver musí schválit nebo zamítnout svůj požadavek předtím, než uživatel v roli aktivní. Pokud se rozhodne Alice [oboru její aktivace](pim-resource-roles-activate-your-roles.md#apply-just-enough-administration-practices) do skupiny prostředků výrobní společnost Fabrikam musí Schvalte nebo zamítněte tuto žádost příliš schvalovatele. Ale pokud se Alice rozhodne k určení rozsahu jednoho nebo obou Fabrikam nebo vývojáře Fabrikam její aktivace, schválení není potřeba.

Pracovní postup schválení nemusí být nezbytné pro všechny členy role. Představte si třeba situaci, kde vaše organizace zaměstná několik smlouvy přidruží k vývoji aplikace, která se spustí v rámci předplatného Azure. Jako správce prostředků chcete zaměstnance, kteří mají mít oprávněného přístupu bez vyžadovaným schválením, ale přidruží kontraktu musí žádost o schválení. Konfigurace pracovního postupu schvalování pro pouze kontraktu associates, můžete vytvořit vlastní role se stejnými oprávněními jako role přiřazená na zaměstnance. Může vyžadovat schválení pro aktivaci této vlastní role. [Další informace o vlastních rolích](pim-resource-roles-custom-role-policy.md).

## <a name="next-steps"></a>Další postup

- [Přiřazení role prostředků Azure v PIM](pim-resource-roles-assign-roles.md)
