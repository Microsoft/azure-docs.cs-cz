---
title: Privileged Identity Management – pracovní postup schválení pro role prostředků Azure | Microsoft Docs
description: Popisuje, jak zpracovat pracovní postup schválení pro prostředky Azure.
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
ms.openlocfilehash: c02d595d75b2d63558896054c185102ebb23cc9e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---approve"></a>Správa privilegovaných identit - role prostředků – schválení

S pracovní postup schválení v PIM pro role prostředků Azure můžete správci další chránit nebo omezení přístupu k důležitým prostředkům vyžádáním souhlasu aktivovat přiřazení role. Jedinečné pro role prostředků Azure je koncept hierarchie prostředků. Díky této hierarchii mohou dědičnosti přiřazení role z nadřazeného objektu prostředků dolů, aby všechny podřízené podřízené prostředky v rámci nadřazeného kontejneru. 

Například Bob, správce prostředků používá PIM přiřadit Alice jako oprávněné člena k roli vlastníka v rámci předplatného společnosti Contoso. K tomuto přiřazení Alice je také vhodné vlastníkem všechny kontejnery skupiny prostředků v rámci předplatného společnosti Contoso a všechny prostředky (třeba virtuální počítače) obsažené v rámci jednotlivých skupin prostředků, předplatného. Předpokládejme, že existují tři skupiny prostředků v předplatném Contoso; Společnost Fabrikam testování, vývoj Fabrikam a produkčnímu Fabrikam. Každá z těchto skupin prostředků obsahuje jeden virtuální počítač.

Nastavení PIM jsou nakonfigurovány pro každou roli prostředku a na rozdíl od přiřazení tato nastavení nejsou zděděné a použít výhradně k roli prostředků. [Další informace o přiřazení oprávněné a viditelnost prostředku.](pim-resource-roles-eligible-visibility.md)

Pomocí výše uvedeném příkladu Bob používá PIM tak, aby vyžadovala všechny členy v roli vlastníka žádost o schválení předplatného společnosti Contoso a aktivovat. Pokud chcete chránit prostředky obsažené v rámci skupiny prostředků produkčnímu Fabrikam, Bob vyžaduje schválení pro členy role vlastníka tohoto prostředku také. Role vlastník v testovacích Fabrikam a vývojáře Fabrikam nevyžadují schválení aktivovat.

Když Alice požadavky aktivace jeho vlastníka role pro předplatné Contoso approver musí schválit nebo svůj požadavek je odepřen, než se stane aktivní v roli. Kromě toho, pokud se rozhodne Alice [obor jeho aktivace](pim-resource-roles-activate-your-roles.md#just-enough-administration) ke skupině prostředků Fabrikam produkčnímu musí schválit nebo zamítnout tento požadavek příliš schvalovatele. Ale pokud Alice rozhodne k určení rozsahu jeho aktivace obojím Fabrikam testovací nebo vývojáře Fabrikam, schválení se nevyžaduje.

Pracovní postup schválení nemusí být potřebné pro všechny členy role. Vezměte v úvahu scénář, kde vaše organizace najímá několik partnerů kontrakt jako pomůcku při vývoji aplikace, která se spustí v předplatné Azure. Jako správce prostředků chcete zaměstnanci mají způsobilé přístup bez schválení vyžadované, ale partnerů kontrakt musíte požádat o schválení. Konfigurovat pracovní postup schválení pro pouze kontrakt partnerů, můžete vytvořit vlastní role se stejnými oprávněními jako role přiřazené zaměstnanci a vyžadovat schválení aktivovat tuto vlastní roli. [Další informace o vlastních rolích](pim-resource-roles-custom-role-policy.md).

Použijte následující postup ke konfiguraci pracovní postup schválení a určit, který můžete schválit nebo odmítnout požadavky.

## <a name="require-approval-to-activate"></a>K aktivaci vyžadovat schválení

Přejděte do PIM na portálu Azure a vybrat prostředku ze seznamu.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

V levém navigační nabídce vyberte **nastavení Role**.

Vyhledejte a vyberte roli a klikněte na **upravit** změnit nastavení.

![](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_view_settings.png)

Při aktivaci oddílu, zaškrtněte políčko **vyžadovat schválení aktivovat**.

![](media/azure-pim-resource-rbac/aadpim_rbac_settings_require_approval_checkbox.png)

## <a name="specify-approvers"></a>Zadejte schvalovatelů

Klikněte na tlačítko **vyberte schvalovatelů** otevřete obrazovku pro výběr.

>[!NOTE]
>Musíte vybrat alespoň jeden uživatel nebo skupina se aktualizovat nastavení. Neexistují žádné výchozí schvalovatelů.

Správci prostředků můžete přidat libovolnou kombinaci uživatelů a skupin do seznamu schvalovatelů. 

![](media/azure-pim-resource-rbac/aadpim_rbac_role_settings_select_approvers.png)

## <a name="request-approval-to-activate"></a>Požadovat schválení aktivovat

Požadování schválení nemá žádný vliv na postup musí následovat členem aktivovat. [Projděte si kroky pro aktivaci role](pim-resource-roles-activate-your-roles.md).

Pokud člen požadovaný aktivace role, který vyžaduje schválení a role se už nevyžaduje, člen zrušit jejich požadavku v PIM.

Pokud chcete zrušit, přejděte do PIM a vyberte "Požadavky". Najděte žádost a klikněte na tlačítko Storno.

![](media/azure-pim-resource-rbac/aadpim_rbac_role_approval_request_pending.png)

## <a name="approve-or-deny-a-request"></a>Schválí nebo zamítne žádost

Schválí nebo zamítne žádost musí být členem skupiny seznamu schvalovatel. V PIM na kartě v levé navigační nabídce vyberte "Schvalovat žádosti o" a vyhledejte žádost.

![](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

Vyberte žádosti, uveďte její odůvodnění pro rozhodnutí a vyberte schválení nebo zamítnutí požadavků, řešení žádosti.

![](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>Oznámení pro pracovní postupy

- Všichni členové seznamu schvalovatel se e-mailem upozorní, když požadavek pro roli čeká na jejich revize. E-mailová oznámení zahrnují přímý odkaz na žádost, kde můžou schvalovatel schválit nebo odmítnout.
- Požadavky jsou vyřešeny první členem seznamu, který schválí nebo odmítne. 
- Jestliže schvalovatele odpoví na žádost, všichni členové seznamu schvalovatel upozorněni akce. 
- Správci prostředků se upozorní, když člena schválené stane aktivní v jejich role. 

>[!Note]
>V události kde správce prostředků dochází k závěru, že schválené člena nesmí být aktivní se může odebrat přiřazení active role v PIM. I když správci prostředků nejsou informováni o žádosti čekající na vyřízení, pokud nejsou členy seznamu schvalovatel, se může zobrazit a zrušit nevyřízené žádosti o všech uživatelů zobrazením čekající požadavky v PIM. 

## <a name="next-steps"></a>Další postup

[Nastavení PIM jedinečný skupiny uživatelů](pim-resource-roles-custom-role-policy.md)
