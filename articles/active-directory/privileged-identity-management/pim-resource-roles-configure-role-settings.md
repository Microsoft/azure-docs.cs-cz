---
title: Konfigurace nastavení role prostředků Azure v PIM | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat nastavení role prostředků Azure v Azure AD Privileged Identity Management (PIM).
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
ms.openlocfilehash: 901eb5ef43ddb2840ed7a3d83fc08f2f05849461
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189731"
---
# <a name="configure-azure-resource-role-settings-in-pim"></a>Konfigurace nastavení role prostředků Azure v PIM

Při konfiguraci nastavení role definovat výchozí nastavení, které se použijí pro přiřazení v prostředí Privileged Identity Management (PIM). Chcete-li definovat toto nastavení pro váš prostředek, vyberte **nastavení Role** kartu v levém podokně. Chcete-li zobrazit aktuální možnosti můžete také vybrat tlačítko Nastavení role z panelu akcí (v libovolnou roli).

## <a name="overview"></a>Přehled

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

## <a name="next-steps"></a>Další postup

- [Vyžadovat vícefaktorové ověřování pro role prostředků Azure v PIM](pim-resource-roles-require-mfa.md)
- [Konfigurace výstrah zabezpečení pro role prostředků Azure v PIM](pim-resource-roles-configure-alerts.md)
