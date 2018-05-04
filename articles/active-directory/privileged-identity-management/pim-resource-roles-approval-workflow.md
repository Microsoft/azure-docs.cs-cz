---
title: Pracovní postup schválení pro prostředků Azure role v Privileged Identity managementu | Microsoft Docs
description: Popisuje pracovní postup schválení pro prostředky Azure.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 7781c858a5c0e4db8593df0cf77b868b6fd23622
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="approval-workflow-for-azure-resource-roles-in-privileged-identity-management"></a>Pracovní postup schválení pro prostředků Azure role v Privileged Identity Management

S schválení pracovního postupu v Privileged Identity Management (PIM) pro role prostředků Azure můžete správci další chránit nebo omezení přístupu k důležitým prostředkům. To znamená správce může vyžadovat schválení aktivovat přiřazení role. 

Koncept hierarchie prostředků je jedinečné pro role prostředků Azure. Díky této hierarchii mohou dědičnosti přiřazení role z nadřazeného objektu prostředků dolů, aby všechny podřízené prostředky v rámci nadřazeného kontejneru. 

Příklad: Bob, správce prostředků používá PIM přiřadit Alice jako oprávněné člena k roli vlastníka v rámci předplatného společnosti Contoso. K tomuto přiřazení Alice je vhodné vlastníkem všechny kontejnery skupiny prostředků v rámci předplatného společnosti Contoso. Alice je také vhodné vlastníka všech prostředků (např. virtuální počítače) v rámci jednotlivých skupin prostředků předplatného. 

Předpokládejme, existují tři skupiny prostředků v předplatném Contoso: testování Fabrikam, Fabrikam vývoj a produkčnímu Fabrikam. Každá z těchto skupin prostředků obsahuje jeden virtuální počítač.

Nastavení PIM jsou nakonfigurována pro každou roli prostředku. Na rozdíl od přiřazení tato nastavení nejsou zděděné a použít výhradně k roli prostředků. [Další informace o přiřazení oprávněné a viditelnost prostředku](pim-resource-roles-eligible-visibility.md).

Pokračování příkladu s: Bob používá PIM tak, aby vyžadovala všechny členy v roli vlastníka žádost o schválení předplatného společnosti Contoso a aktivovat. K ochraně prostředky ve skupině prostředků Fabrikam produkčnímu Bob taky vyžaduje schválení pro členy role vlastníka tohoto prostředku. Role vlastník v testovacích Fabrikam a vývojáře Fabrikam nevyžadují schválení pro aktivaci.

Pokud Alice požádá o aktivaci jeho vlastníka role pro předplatné Contoso, approver musí schválit nebo svůj požadavek je odepřen, než se stane aktivní v roli. Pokud Alice rozhodne [obor jeho aktivace](pim-resource-roles-activate-your-roles.md#apply-just-enough-administration-practices) ke skupině prostředků Fabrikam produkčnímu musí schválit nebo zamítnout tento požadavek příliš schvalovatele. Ale pokud Alice rozhodne obor jeho aktivace obojím Fabrikam testovací nebo vývojáře Fabrikam, schválení není povinný.

Pracovní postup schválení nemusí být potřebné pro všechny členy role. Vezměte v úvahu scénář, kde vaše organizace najímá několik kontrakt partnerů k vývoji aplikace, která se spustí v předplatné Azure. Jako správce prostředků můžete chtít, aby zaměstnanci mají způsobilé přístup bez schválení vyžadované, ale partnerů kontrakt musíte požádat o schválení. Pokud chcete nakonfigurovat pracovní postup schválení pro pouze partnerů kontrakt, můžete vytvořit vlastní role se stejnými oprávněními jako role přiřazené zaměstnanci. Může vyžadovat schválení aktivovat tuto vlastní roli. [Další informace o vlastních rolích](pim-resource-roles-custom-role-policy.md).

Ke konfiguraci pracovního postupu schválení a určit, který můžete schválit nebo odmítnout požadavky, použijte následující postupy.

## <a name="require-approval-to-activate"></a>K aktivaci vyžadovat schválení

1. Přejděte do PIM na portálu Azure a vybrat prostředku ze seznamu.

   !["Prostředky azure" podokně s vybraného prostředku](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

2. V levém podokně vyberte **nastavení Role**.

3. Vyhledejte a vyberte roli a potom vyberte **upravit** změnit nastavení.

   ![Tlačítko pro roli operátora "upravit"](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_view_settings.png)

4. V **aktivace** vyberte **vyžadovat schválení aktivovat** zaškrtávací políčko.

   !["Aktivace" část nastavení role](media/azure-pim-resource-rbac/aadpim_rbac_settings_require_approval_checkbox.png)

## <a name="specify-approvers"></a>Zadejte schvalovatelů

Klikněte na tlačítko **vyberte schvalovatelů** otevřete **vyberte uživatele nebo skupinu** podokně.

>[!NOTE]
>Musíte vybrat alespoň jeden uživatel nebo skupina se aktualizovat nastavení. Neexistují žádné výchozí schvalovatelů.

Správci prostředků můžete přidat libovolnou kombinaci uživatelů a skupin do seznamu schvalovatelů. 

!["Vyberte uživatele nebo skupinu" podokně s uživatelem vybrané](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_select_approvers.png)

## <a name="request-approval-to-activate"></a>Požadovat schválení aktivovat

Vyžádání schválení nemá žádný vliv na postup, který je členem musí následovat aktivovat. [Projděte si kroky pro aktivaci role](pim-resource-roles-activate-your-roles.md).

Pokud členem požadované aktivace role, který vyžaduje schválení a role se už nevyžaduje, můžete zrušit člen žádosti v PIM.

Pokud chcete zrušit, procházet a vyberte PIM **Moje žádosti**. Vyhledejte požadavků a vyberte **zrušit**.

![Podokno "Požadavky"](media/azure-pim-resource-rbac/aadpim_rbac_role_approval_request_pending.png)

## <a name="approve-or-deny-a-request"></a>Schválí nebo zamítne žádost

Schválí nebo zamítne žádost, musí být členem skupiny seznamu schvalovatel. 

1. V PIM, vyberte **schvalovat žádosti o** z karty v nabídce vlevo a vyhledejte žádost.

   !["Schvalovat žádosti o" podokno](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

2. Vyberte žádosti, uveďte její odůvodnění pro rozhodnutí a vyberte **schválit** nebo **Odepřít**. Pak se vyřeší požadavek.

   ![Vybranou žádost o se podrobné informace](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>Oznámení pro pracovní postupy

Zde jsou některé faktů o oznámení pracovního postupu:

- Všichni členové seznamu schvalovatel se e-mailem upozorní, když požadavek pro roli čeká na jejich revize. E-mailová oznámení zahrnují přímý odkaz na žádost, kde můžete schvalovatel schválit nebo odmítnout.
- Požadavky jsou vyřešeny první člen v seznamu, který schválí nebo odmítne. 
- Jestliže schvalovatele odpoví na žádost, všichni členové seznamu schvalovatel upozorněni akce. 
- Správci prostředků se upozorní, když člena schválené stane aktivní v jejich role. 

>[!Note]
>Správce prostředků, který dochází k závěru, že nemají být člena schválené active můžete odebrat přiřazení active role v PIM. I když správci prostředků nejsou informováni o žádosti čekající na vyřízení, pokud nejsou členy seznamu schvalovatel, se mohou zobrazit a zrušit nevyřízené žádosti o všech uživatelů zobrazením čekající požadavky v PIM. 

## <a name="next-steps"></a>Další postup

[Nastavení PIM jedinečný skupiny uživatelů](pim-resource-roles-custom-role-policy.md)
