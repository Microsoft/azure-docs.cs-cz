---
title: Přidat nebo odebrat členy skupiny – Azure Active Directory | Microsoft Docs
description: Pokyny k přidání nebo odebrání členů ze skupiny pomocí Azure Active Directory.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "68561972"
---
# <a name="add-or-remove-group-members-using-azure-active-directory"></a>Přidat nebo odebrat členy skupiny pomocí Azure Active Directory
Pomocí Azure Active Directory můžete pokračovat v přidávání a odebírání členů skupiny.

## <a name="to-add-group-members"></a>Přidání členů skupiny

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu globálního správce daného adresáře.

2. Vyberte **Azure Active Directory**a pak vyberte **skupiny**.

3. Na stránce **skupiny – všechny skupiny** vyhledejte a vyberte skupinu, do které chcete člena přidat. V takovém případě použijte naši dříve vytvořenou skupinu **– zásada MDM – západ**.

    ![Skupiny – stránka všechny skupiny, zvýrazněný název skupiny](media/active-directory-groups-members-azure-portal/group-all-groups-screen.png)

4. Na stránce s **přehledem skupiny MDM policy – West** vyberte **Členové** v oblasti **Spravovat**.

    ![Zásady MDM – Stránka s přehledem, se zvýrazněnou možností členové](media/active-directory-groups-members-azure-portal/group-overview-blade.png)

5. Vyberte **přidat členy**a potom vyhledejte a vyberte jednotlivé členy, které chcete přidat do skupiny, a pak zvolte **Vybrat**.

    Zobrazí se zpráva oznamující, že členové byli úspěšně přidáni.

    ![Stránka přidat členy se zobrazeným hledaným členem](media/active-directory-groups-members-azure-portal/update-members.png)

6. Aktualizujte obrazovku, aby se zobrazily všechny názvy členů přidané do skupiny.

## <a name="to-remove-group-members"></a>Odebrání členů skupiny

1. Na stránce **skupiny – všechny skupiny** vyhledejte a vyberte skupinu, ze které chcete člena odebrat. Znovu použijeme **zásady MDM – západ**.

2. V oblasti **Spravovat** vyberte **členy** , vyhledejte a vyberte název člena, který chcete odebrat, a pak vyberte **Odebrat**.

    ![Stránka informace o členovi, s možností odebrat](media/active-directory-groups-members-azure-portal/remove-members-from-group.png)

## <a name="next-steps"></a>Další kroky

- [Zobrazení skupin a členů](active-directory-groups-view-azure-portal.md)

- [Úprava nastavení skupiny](active-directory-groups-settings-azure-portal.md)

- [Správa přístupu k prostředkům pomocí skupin](active-directory-manage-groups.md)

- [Správa dynamických pravidel pro uživatele ve skupině](../users-groups-roles/groups-create-rule.md)

- [Přidružte nebo přidejte předplatné Azure k Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
