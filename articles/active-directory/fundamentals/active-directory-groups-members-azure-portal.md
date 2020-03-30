---
title: Přidání nebo odebrání členů skupiny – Služba Azure Active Directory | Dokumenty společnosti Microsoft
description: Pokyny, jak přidat nebo odebrat členy ze skupiny pomocí služby Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1c83c57be63ae9e2a4d4113accaefe8a2c2b525
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68561972"
---
# <a name="add-or-remove-group-members-using-azure-active-directory"></a>Přidání nebo odebrání členů skupiny pomocí služby Azure Active Directory
Pomocí služby Azure Active Directory můžete pokračovat v přidávání a odebírání členů skupiny.

## <a name="to-add-group-members"></a>Přidání členů skupiny

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu globálního správce daného adresáře.

2. Vyberte **Službu Azure Active Directory**a pak vyberte **Skupiny**.

3. Na stránce **Skupiny – všechny skupiny** vyhledejte a vyberte skupinu, do které chcete člena přidat. V takovém případě použijte naši dříve vytvořenou skupinu **MDM policy - West**.

    ![Stránka Skupiny -Všechny skupiny, zvýrazněný název skupiny](media/active-directory-groups-members-azure-portal/group-all-groups-screen.png)

4. Na stránce s **přehledem skupiny MDM policy – West** vyberte **Členové** v oblasti **Spravovat**.

    ![Zásady MDM – stránka Západní přehled se zvýrazněnou možností Členové](media/active-directory-groups-members-azure-portal/group-overview-blade.png)

5. Vyberte **Přidat členy**a pak prohledejte a vyberte všechny členy, které chcete přidat do skupiny, a pak zvolte **Vybrat**.

    Zobrazí se zpráva, že členové byli úspěšně přidáni.

    ![Přidat stránku členů se zobrazeným vyhledávaným členem](media/active-directory-groups-members-azure-portal/update-members.png)

6. Aktualizujte obrazovku, aby se zotřila všechna jména členů přidaná do skupiny.

## <a name="to-remove-group-members"></a>Odebrání členů skupiny

1. Na stránce **Skupiny – všechny skupiny** vyhledejte a vyberte skupinu, ze které chcete člena odebrat. Opět budeme používat, **MDM politiky - West**.

2. Vyberte **Členové** z oblasti **Spravovat,** vyhledejte a vyberte jméno člena, který chcete odebrat, a pak vyberte **Odebrat**.

    ![Stránka Informace o členech s možností Odebrat](media/active-directory-groups-members-azure-portal/remove-members-from-group.png)

## <a name="next-steps"></a>Další kroky

- [Zobrazení skupin a členů](active-directory-groups-view-azure-portal.md)

- [Úprava nastavení skupiny](active-directory-groups-settings-azure-portal.md)

- [Správa přístupu k prostředkům pomocí skupin](active-directory-manage-groups.md)

- [Správa dynamických pravidel pro uživatele ve skupině](../users-groups-roles/groups-create-rule.md)

- [Přidružení nebo přidání předplatného Azure do Služby Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
