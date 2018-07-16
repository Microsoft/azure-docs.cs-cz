---
title: Správa skupin v Azure AD, do kterých vaše skupina patří | Microsoft Docs
description: Skupiny v Azure Active Directory můžou obsahovat jiné skupiny. Tady je postup, jak tato členství spravovat.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 10/10/2017
ms.author: lizross
ms.custom: it-pro
ms.reviewer: krbain
ms.openlocfilehash: 8a71677ae3ceb5617f0a817a8eff438d5e3f2774
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860331"
---
# <a name="manage-to-which-groups-a-group-belongs-in-your-azure-active-directory-tenant"></a>Správa skupin ve vašem tenantovi Azure Active Directory, do kterých vaše skupina patří
Skupiny v Azure Active Directory můžou obsahovat jiné skupiny. Tady je postup, jak tato členství spravovat.

## <a name="how-do-i-find-the-groups-of-which-my-group-is-a-member"></a>Jak zjistím, ve kterých skupinách má moje skupina členství?
1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který má k adresáři oprávnění globálního správce.
2. Vyberte **Uživatelé a skupiny**.

   ![Obrázek otevření uživatelů a skupin](./media/active-directory-groups-membership-azure-portal/search-user-management.png)
1. Vyberte **Všechny skupiny**.

   ![Obrázek výběru skupin](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)
1. Vyberte skupinu.
2. Vyberte **Členství ve skupinách**.

   ![Obrázek otevření členství ve skupinách](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)
1. Pokud chcete přidat členství vaší skupiny v jiné skupině, vyberte v okně **Skupina – Členství ve skupinách** příkaz **Přidat**.
2. Vyberte skupinu v okně **Vybrat skupinu** a pak v dolní části okna vyberte tlačítko **Vybrat**. Skupinu nelze přidat do více skupin najednou. Položky v poli **Uživatel** se filtrují podle toho, jak se vaše zadání shoduje s jakoukoli částí jména uživatele nebo názvu zařízení. V tomto poli není možné používat žádné zástupné znaky.

   ![Přidání členství ve skupinách](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)
8. Pokud chcete odebrat členství vaší skupiny v jiné skupině, vyberte danou skupinu v okně **Skupina – Členství ve skupinách**.
9. Vyberte příkaz **Odebrat** a po zobrazení výzvy tuto volbu potvrďte.

   ![Příkaz pro odebrání členství](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)
10. Až změny členství vaší skupiny v jiných skupinách dokončíte, vyberte **Uložit**.

## <a name="additional-information"></a>Další informace
Následující články poskytují další informace o službě Azure Active Directory.

* [Zobrazení existujících skupin](active-directory-groups-view-azure-portal.md)
* [Vytvoření nové skupiny a přidání členů](active-directory-groups-create-azure-portal.md)
* [Správa nastavení skupiny](active-directory-groups-settings-azure-portal.md)
* [Správa členů skupiny](active-directory-groups-members-azure-portal.md)
* [Správa dynamických pravidel pro uživatele ve skupině](../users-groups-roles/groups-dynamic-membership.md)
