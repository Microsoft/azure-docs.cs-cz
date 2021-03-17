---
title: Přiřazení rolí Azure AD uživatelům – Azure Active Directory
description: Přečtěte si, jak udělit přístup uživatelům v Azure Active Directory přiřazením rolí Azure AD.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 03/07/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ced586db1b4e417e623431c137c43dac8ba56f
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466634"
---
# <a name="assign-azure-ad-roles-to-users"></a>Přiřazení rolí Azure AD uživatelům

V centru pro správu Azure AD teď můžete zobrazit a spravovat všechny členy rolí správce. Pokud často spravujete přiřazení rolí, budete pravděpodobně chtít vyzkoušet toto prostředí. Tento článek popisuje, jak přiřadit role Azure AD pomocí centra pro správu Azure AD.

## <a name="assign-a-role"></a>Přiřazení role

1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí oprávnění správce globálního správce nebo privilegované role.

1. Vyberte **Azure Active Directory**.

1. Výběrem **rolí a správců** zobrazíte seznam všech dostupných rolí.

    ![Snímek obrazovky se stránkou role a správci](./media/manage-roles-portal/roles-and-administrators.png)

1. Výběrem role zobrazíte její přiřazení.

    Abychom vám pomohli najít roli, kterou potřebujete, může Azure AD zobrazit podmnožiny rolí na základě kategorií rolí. Podívejte se na filtr **typu** , abyste zobrazili jenom role ve vybraném typu.

1. Vyberte **Přidat přiřazení** a potom vyberte uživatele, které chcete této roli přiřadit.

    Pokud vidíte něco jiného než na následujícím obrázku, přečtěte si poznámku v [Privileged Identity Management (PIM)](#privileged-identity-management-pim) a ověřte, jestli používáte PIM.

    ![seznam oprávnění pro roli správce](./media/manage-roles-portal/add-assignments.png)

1. Vyberte **Přidat** a přiřaďte roli.

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

Můžete vybrat **spravovat v PIM** pro další možnosti správy pomocí [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md). Správci privilegovaných rolí můžou změnit přiřazení "trvalé" (vždy aktivní v roli) přiřazení na "opravňující" (v roli jenom při zvýšeném oprávnění). Pokud nemáte Privileged Identity Management, můžete přesto vybrat **spravovat v PIM** , abyste se mohli zaregistrovat ke zkušební verzi. Privileged Identity Management vyžaduje [plán licencí Azure AD Premium P2](../privileged-identity-management/subscription-requirements.md).

![Snímek obrazovky zobrazující stránku správce uživatele – přiřazení s vybranou akcí spravovat v PIM](./media/manage-roles-portal/member-list-pim.png)

Pokud jste globální správce nebo správce privilegovaných rolí, můžete snadno přidávat nebo odebírat členy, filtrovat seznam nebo vybrat člena a zobrazit jejich aktivní přiřazené role.

> [!Note]
> Pokud máte licenci Azure AD Premium P2 a už používáte Privileged Identity Management, provedou se všechny úlohy správy rolí ve správě identit oprávnění, nikoli v Azure AD.
>
> ![Role Azure AD spravované v PIM pro uživatele, kteří už používají PIM a mají licenci Premium P2](./media/manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="next-steps"></a>Další kroky

* Můžete s námi sdílet na [fóru role pro správu Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Další informace o rolích najdete v tématu [předdefinované role Azure AD](permissions-reference.md).
* Pro výchozí uživatelská oprávnění si přečtěte téma [Porovnání výchozích oprávnění typu Host a uživatel](../fundamentals/users-default-permissions.md).
