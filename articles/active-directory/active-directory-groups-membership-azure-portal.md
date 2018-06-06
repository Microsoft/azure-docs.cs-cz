---
title: Správa skupin vaše skupina patří v Azure Active Directory | Microsoft Docs
description: Skupiny mohou obsahovat jiné skupiny ve službě Azure Active Directory. Chcete-li spravovat jejich členství.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: article
ms.date: 10/10/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017;it-pro
ms.reviewer: piotrci
ms.openlocfilehash: 9865f871f925b4ab1dd290b0afba3258581f0bf6
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714319"
---
# <a name="manage-to-which-groups-a-group-belongs-in-your-azure-active-directory-tenant"></a>Spravovat do skupiny, které patří skupiny v klientovi služby Azure Active Directory
Skupiny mohou obsahovat jiné skupiny ve službě Azure Active Directory. Chcete-li spravovat jejich členství.

## <a name="how-do-i-find-the-groups-of-which-my-group-is-a-member"></a>Jak lze najít skupiny, jejichž členem je mé skupině?
1. Přihlaste se k [centru pro správu Azure AD](https://aad.portal.azure.com) pomocí účtu, který má k adresáři oprávnění globálního správce.
2. Vyberte **uživatelů a skupin**.

   ![Otevírání uživatelů a skupin image](./media/active-directory-groups-membership-azure-portal/search-user-management.png)
1. Vyberte **všechny skupiny**.

   ![Vybrat skupiny image](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)
1. Vyberte skupinu.
2. Vyberte **členství ve skupinách**.

   ![Obrázek členství skupiny otevírání](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)
1. Přidání skupiny jako členem jiné skupiny, na **skupiny – členství ve skupinách** okně, vyberte **přidat** příkaz.
2. Vyberte skupinu z **vybrat skupinu** a pak vyberte **vyberte** tlačítko v dolní části okna. Skupiny můžete přidat pouze do jedné skupiny najednou. **Uživatele** pole filtry zobrazení na základě shody zadání vašeho jakékoliv části názvu uživatele nebo zařízení. V tomto poli, jsou přijaty žádné zástupné znaky.

   ![Přidat členství ve skupině](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)
8. Odebrání skupiny jako členem jiné skupiny, na **skupiny – členství ve skupinách** okně vyberte skupinu.
9. Vyberte **odebrat** příkazů a potvrďte svou volbu příkazového řádku.

   ![Odeberte příkaz členství](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)
10. Po dokončení změn členství ve skupinách pro skupinu, vyberte **Uložit**.

## <a name="additional-information"></a>Další informace
Následující články poskytují další informace o službě Azure Active Directory.

* [Zobrazení existujících skupin](active-directory-groups-view-azure-portal.md)
* [Vytvoření nové skupiny a přidávání členů](active-directory-groups-create-azure-portal.md)
* [Spravovat nastavení skupiny](active-directory-groups-settings-azure-portal.md)
* [Správa členů skupiny](active-directory-groups-members-azure-portal.md)
* [Dynamická pravidla pro uživatele ve skupině pro správu](active-directory-groups-dynamic-membership-azure-portal.md)
