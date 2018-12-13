---
title: Přidání nebo odebrání členů skupiny – Azure Active Directory | Dokumentace Microsoftu
description: Pokyny ohledně toho, jak přidat nebo odebrat členy ze skupiny pomocí služby Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.openlocfilehash: 42b80b53ca7c860143f1de1005a609b3e921e650
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53076138"
---
# <a name="add-or-remove-group-members-using-azure-active-directory"></a>Přidání nebo odebrání členů skupiny pomocí služby Azure Active Directory
Pomocí Azure Active Directory, můžete pokračovat k přidání a odebrání členů skupiny.

## <a name="to-add-group-members"></a>Chcete-li přidat členy skupiny

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu globálního správce daného adresáře.

2. Vyberte **Azure Active Directory**a pak vyberte **skupiny**.

3. Z **skupiny – všechny skupiny** stránky, vyhledejte a vyberte skupinu, kterou chcete přidat člen, který chcete. V takovém případě použijte naše dříve vytvořenou skupinu **zásady MDM - západní**.

    ![Stránka skupiny – všechny skupiny, zvýrazněným názvem skupiny](media/active-directory-groups-members-azure-portal/group-all-groups-screen.png)

4. Na stránce s **přehledem skupiny MDM policy – West** vyberte **Členové** v oblasti **Spravovat**.

    ![Zásady MDM – stránka s přehledem – Západ, se zvýrazněnou možností členy](media/active-directory-groups-members-azure-portal/group-overview-blade.png)

5. Vyberte **přidat členy**a pak vyhledejte a vyberte jednotlivé členy, které chcete přidat do skupiny, a klikněte na tlačítko **vyberte**.

    Zobrazí se zpráva s upozorněním, že členové se úspěšně přidaly.

    ![Přidat stránku členy s hledá člen zobrazí](media/active-directory-groups-members-azure-portal/update-members.png)

6. Aktualizujte obrazovku, aby se zobrazily všechny názvy členů přidán do skupiny.

## <a name="to-remove-group-members"></a>K odebrání členů skupiny

1. Z **skupiny – všechny skupiny** stránky, vyhledejte a vyberte skupinu, kterou chcete odebrat člena ze. Znovu použijeme, **zásady MDM - západní**.

2. Vyberte **členy** z **spravovat** oblasti, vyhledejte a vyberte název členu, který chcete odebrat a pak vyberte **odebrat**.

    ![Stránka informace o člena, s možností odstranit](media/active-directory-groups-members-azure-portal/remove-members-from-group.png)

## <a name="next-steps"></a>Další postup

- [Zobrazení skupin a členů](active-directory-groups-view-azure-portal.md)

- [Úprava nastavení skupiny](active-directory-groups-settings-azure-portal.md)

- [Správa přístupu k prostředkům pomocí skupin](active-directory-manage-groups.md)

- [Správa dynamických pravidel pro uživatele ve skupině](../users-groups-roles/groups-create-rule.md)

- [Přiřazení nebo přidání předplatného Azure do služby Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
