---
title: Pracovní postup schválení pro role prostředků Azure v Privileged Identity Management | Dokumentace Microsoftu
description: Popisuje proces pracovního postupu schvalování pro prostředky Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: dcb306bda8ef7d93314390bd9a90327ffdd14a0e
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39619633"
---
# <a name="approval-workflow-for-azure-resource-roles-in-privileged-identity-management"></a>Pracovní postup schválení pro role prostředků Azure v Privileged Identity Management

S schvalovací pracovní postup v Privileged Identity Management (PIM) pro role prostředků Azure můžete správci dále chránit nebo omezit přístup k důležitým prostředkům. To znamená správci můžou k aktivaci vyžadovat schválení přiřazení rolí. 

Koncept hierarchii prostředků je jedinečné pro role prostředků Azure. Díky této hierarchii mohou dědičnosti přiřazení rolí z nadřazeného objektu prostředku směrem dolů na všechny podřízené prostředky v rámci nadřazeného kontejneru. 

Příklad: Bob, správce prostředků, používá PIM přiřadit Alice jako oprávněného člena do role vlastník předplatného Contoso. Alice je tohoto přiřazení oprávnění vlastníka u všech kontejnerů skupiny prostředků v rámci předplatného Contoso. Alice je také vhodné vlastníkem všech prostředků (jako jsou virtuální počítače) v rámci jednotlivých skupin prostředků předplatného. 

Předpokládejme, existují tři skupiny prostředků v předplatném Contoso: Fabrikam testu, vývojáře Fabrikam a Fabrikam Prod. Každá z těchto skupin prostředků obsahuje jeden virtuální počítač.

Nastavení PIM jsou nakonfigurována pro každou roli prostředku. Na rozdíl od přiřazení tato nastavení nejsou děděna a platí výhradně pro role prostředků. [Další informace o viditelnosti prostředku a oprávněnými přiřazeními](pim-resource-roles-eligible-visibility.md).

Pokračování příkladu s: Bob používá PIM tak, aby vyžadovala všem členům v roli vlastník žádosti o schválení předplatného Contoso aktivovat. Pomáhá chránit prostředky ve skupině prostředků výrobní společnosti Fabrikam, Bob také vyžaduje schválení pro členy role vlastníka tohoto prostředku. Role vlastník ve společnosti Fabrikam testování a vývoj Fabrikam nevyžadují schválení pro aktivaci.

Když Alice žádosti o aktivaci její roli vlastníka předplatného Contoso, approver musí schválit nebo zamítnout svůj požadavek předtím, než uživatel v roli aktivní. Pokud se rozhodne Alice [oboru její aktivace](pim-resource-roles-activate-your-roles.md#apply-just-enough-administration-practices) do skupiny prostředků výrobní společnost Fabrikam musí Schvalte nebo zamítněte tuto žádost příliš schvalovatele. Ale pokud se Alice rozhodne k určení rozsahu jednoho nebo obou Fabrikam nebo vývojáře Fabrikam její aktivace, schválení není potřeba.

Pracovní postup schválení nemusí být nezbytné pro všechny členy role. Představte si třeba situaci, kde vaše organizace zaměstná několik smlouvy přidruží k vývoji aplikace, která se spustí v rámci předplatného Azure. Jako správce prostředků chcete zaměstnance, kteří mají mít oprávněného přístupu bez vyžadovaným schválením, ale přidruží kontraktu musí žádost o schválení. Konfigurace pracovního postupu schvalování pro pouze kontraktu associates, můžete vytvořit vlastní role se stejnými oprávněními jako role přiřazená na zaměstnance. Může vyžadovat schválení pro aktivaci této vlastní role. [Další informace o vlastních rolích](pim-resource-roles-custom-role-policy.md).

Ke konfiguraci pracovního postupu schvalování a určit, který můžete schválit nebo odmítnout požadavky, použijte následující postupy.

## <a name="require-approval-to-activate"></a>K aktivaci vyžadovat schválení

1. Přejděte do PIM na portálu Azure portal a vyberte prostředek ze seznamu.

   !["Azure resources" podokno s vybraný prostředek](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

2. V levém podokně vyberte **nastavení Role**.

3. Vyhledejte a vyberte roli a pak vyberte **upravit** ke změně nastavení.

   ![Tlačítko "Upravit" pro roli operátora](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_view_settings.png)

4. V **aktivace** vyberte **k aktivaci vyžadovat schválení** zaškrtávací políčko.

   ![Část "Aktivace" nastavení role](media/azure-pim-resource-rbac/aadpim_rbac_settings_require_approval_checkbox.png)

## <a name="specify-approvers"></a>Zadat schvalovatele

Klikněte na tlačítko **vybrat schvalovatele** otevřít **vyberte uživatele nebo skupiny** podokně.

>[!NOTE]
>Musíte vybrat aspoň jeden uživatel nebo skupina se aktualizovat nastavení. Neexistují žádné výchozí schvalovatele.

Správci prostředků můžete přidat libovolnou kombinací uživatelů a skupin do seznamu schvalovatele. 

!["Vybrat uživatele nebo skupinu" podokno s uživatelem vybraného](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_select_approvers.png)

## <a name="request-approval-to-activate"></a>Žádost o schválení pro aktivaci

Odesílání žádostí o schválení nemá žádný vliv na postup, který člen musí postupovat při aktivaci. [Projděte si postup, chcete-li aktivovat roli](pim-resource-roles-activate-your-roles.md).

Pokud člen požadovaný aktivace role, která vyžaduje schválení a role se už nevyžaduje, můžete zrušit člen žádosti v PIM.

Pokud chcete zrušit, přejděte do PIM a vyberte **Moje žádosti**. Vyhledat žádosti a vyberte **zrušit**.

![Podokno "Požadavky"](media/azure-pim-resource-rbac/aadpim_rbac_role_approval_request_pending.png)

## <a name="approve-or-deny-a-request"></a>Schválit nebo zamítnout žádost

Chcete-li schválit nebo odmítnout žádost, musí být členem seznamu schvalovatele. 

1. V PIM, vyberte **schvalovat žádosti o** z karty v nabídce vlevo a vyhledejte požadavku.

   ![Podokno "Schvalovat žádosti o"](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

2. Vyberte žádosti, uveďte její odůvodnění pro rozhodnutí a vyberte **schválit** nebo **Odepřít**. Pak vyřeší požadavek.

   ![Vybrané žádosti s podrobnými informacemi](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>Oznámení pro pracovní postupy

Tady jsou některé údaje o oznámení pracovního postupu:

- Všichni členové schvalovatel seznamu se zobrazí oznámení e-mailem žádost o roli čeká na kontrolu. E-mailová oznámení zahrnují přímý odkaz na žádost, ve kterém můžete schvalující schválit nebo zamítnout.
- Požadavky jsou vyřešeny prvního člena seznamu, který potvrdí nebo odmítne. 
- Když schvalující odpoví na žádost, se zobrazí všechny členy seznamu schvalovatel oznámení akce. 
- Správci prostředků se zobrazí oznámení, když schváleného člena stane aktivní v jejich role. 

>[!Note]
>Správce prostředků, který se řídí zásadou, že by neměl být aktivní schváleného člena odebrat přiřazení active role v PIM. Přestože správci prostředků nejsou dostávají oznámení o čekajících žádostí Pokud nejsou členy seznamu schvalovatele, mohou zobrazit a zrušit čekající žádosti o pro všechny uživatele tak, že zobrazení čekající žádosti v PIM. 

## <a name="next-steps"></a>Další postup

[Nastavení PIM jedinečných skupin uživatelů](pim-resource-roles-custom-role-policy.md)
