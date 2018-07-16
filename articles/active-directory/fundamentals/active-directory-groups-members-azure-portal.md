---
title: Správa členů skupiny v Azure AD | Microsoft Docs
description: Jak přidat nebo odebrat uživatele a zařízení ve skupině v Azure Active Directory
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 08/28/2017
ms.author: lizross
ms.custom: it-pro
ms.reviewer: krbain
ms.openlocfilehash: 947b0c11aba211530e3ae25d6617079bcaf2995f
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860345"
---
# <a name="manage-group-membership-for-users-in-your-azure-active-directory-tenant"></a>Správa členství uživatelů ve skupinách v tenantovi Azure Active Directory
Tento článek vysvětluje, jak spravovat členy skupiny v Azure Active Directory (Azure AD).

## <a name="how-do-i-find-the-members-and-manage-them"></a>Jak zjistím členy a jak je můžu spravovat?
1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com) prostřednictvím účtu, který má k adresáři oprávnění globálního správce.
2. Vyberte **Všechny služby**, do textového pole zadejte **Uživatelé a skupiny** a potom stiskněte **Enter**.

   ![Otevírání správy uživatelů](./media/active-directory-groups-members-azure-portal/search-user-management.png)
3. V okně **Uživatelé a skupiny** vyberte **Všechny skupiny**.

   ![Otevření okna skupin](./media/active-directory-groups-members-azure-portal/view-groups-blade.png)
4. V okně **Uživatelé a skupiny – Všechny skupiny** vyberte skupinu.
5. V okně **Skupina – *název_skupiny*** vyberte **Členové**.

   ![Otevření okna Členové](./media/active-directory-groups-members-azure-portal/view-group-members.png)
6. Pokud chcete do skupiny přidat členy, v okně **Skupina – Členové** vyberte **Přidat členy**.

   ![Příkaz pro přidání členů](./media/active-directory-groups-members-azure-portal/add-group-members-command.png)
7. V okně **Členové** vyberte minimálně jednoho uživatele nebo zařízení, které chcete přidat do skupiny, a v dolní části okna kliknutím na tlačítko **Vybrat** je přidejte do skupiny. Položky v poli **Uživatel** se filtrují podle toho, jak se vaše zadání shoduje s jakoukoli částí jména uživatele nebo názvu zařízení. V tomto poli není možné používat žádné zástupné znaky.
8. Pokud chcete ze skupiny odebrat členy, v okně **Skupina – Členové** vyberte daného člena.
9. V okně ***Jméno_člena*** vyberte příkaz **Odebrat** a po zobrazení výzvy tuto volbu potvrďte.

   ![Příkaz pro odebrání členů](./media/active-directory-groups-members-azure-portal/remove-group-members-command.png)
10. Po dokončení změn členů skupiny vyberte **Uložit**.

## <a name="additional-information"></a>Další informace
Následující články poskytují další informace o službě Azure Active Directory.

* [Zobrazení existujících skupin](active-directory-groups-view-azure-portal.md)
* [Vytvoření nové skupiny a přidání členů](active-directory-groups-create-azure-portal.md)
* [Správa nastavení skupiny](active-directory-groups-settings-azure-portal.md)
* [Správa členství ve skupině](active-directory-groups-membership-azure-portal.md)
* [Správa dynamických pravidel pro uživatele ve skupině](../users-groups-roles/groups-dynamic-membership.md)
