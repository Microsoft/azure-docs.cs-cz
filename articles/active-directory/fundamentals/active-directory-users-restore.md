---
title: Obnovení nebo trvalé odebrání naposledy odstraněného uživatele – Azure AD
description: Jak zobrazit obnovitelné uživatele, obnovit odstraněného uživatele nebo trvale odstranit uživatele pomocí služby Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 573269da1ca6b9ee09b493f4e758e78121d6c2f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422865"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Obnovení nebo odebrání naposledy odstraněného uživatele pomocí služby Azure Active Directory
Po odstranění uživatele zůstane účet v pozastaveném stavu po dobu 30 dnů. Během tohoto 30denního okna lze uživatelský účet obnovit spolu se všemi jeho vlastnostmi. Po uplynutí 30denního okna je uživatel automaticky a trvale odstraněn.

Můžete zobrazit obnovitelné uživatele, obnovit odstraněného uživatele nebo trvale odstranit uživatele pomocí Služby Azure Active Directory (Azure AD) na webu Azure Portal.

>[!Important]
>Vy ani zákaznická podpora společnosti Microsoft nemůžete trvale odstraněného uživatele obnovit.

## <a name="required-permissions"></a>Požadovaná oprávnění
Chcete-li obnovit a trvale odstranit uživatele, musíte mít jednu z následujících rolí.

- Globální správce

- Podpora partnerské úrovně1

- Podpora partnerské úrovně2

- Správce uživatele

## <a name="view-your-restorable-users"></a>Zobrazení uživatelů s obnovitelnou službou
Můžete zobrazit všechny uživatele, kteří byli odstraněni před méně než 30 dny. Tito uživatelé mohou být obnoveny.

### <a name="to-view-your-restorable-users"></a>Zobrazení uživatelů s obnovitelnou službou
1. Přihlaste se k [portálu Azure](https://portal.azure.com/) pomocí globálního účtu správce pro organizaci.

2. Vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **Odstranění uživatelé**.

    Prohlédněte si seznam uživatelů, kteří jsou k dispozici k obnovení.

    ![Uživatelé – stránka Smazaní uživatelé s uživateli, které lze stále obnovit](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Obnovení naposledy odstraněného uživatele

Při odstranění uživatelského účtu z organizace je účet v pozastaveném stavu a všechny související informace o organizaci jsou zachovány. Při obnovení uživatele se obnoví také tyto informace o organizaci.

> [!Note]
> Po obnovení uživatele jsou licence, které byly uživateli přiřazeny v době odstranění, obnoveny také i v případě, že pro tyto licence nejsou k dispozici žádné licence. Pokud pak spotřebováváte více licencí, než jste zakoupili, může být vaše organizace dočasně mimo předpisy pro využití licencí.

### <a name="to-restore-a-user"></a>Obnovení uživatele
1. Na stránce **Uživatelé – smazaní uživatelé** vyhledejte a vyberte jednoho z dostupných uživatelů. Například _Mary Parker_.

2. Vyberte **možnost Obnovit uživatele**.

    ![Stránka Smazané uživatele se zvýrazněnou možností Obnovit uživatele](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>Trvalé odstranění uživatele
Uživatele můžete trvale odstranit z vaší organizace, aniž byste čekali 30 dní na automatické odstranění. Trvale odstraněného uživatele nemůžete obnovit vy, jiný správce ani zákaznická podpora společnosti Microsoft.

>[!Note]
>Pokud trvale odstraníte uživatele omylem, budete muset vytvořit nového uživatele a ručně zadat všechny předchozí informace. Další informace o vytvoření nového uživatele naleznete v tématu [Přidání nebo odstranění uživatelů](add-users-azure-active-directory.md).

### <a name="to-permanently-delete-a-user"></a>Trvalé odstranění uživatele

1. Na stránce **Uživatelé – smazaní uživatelé** vyhledejte a vyberte jednoho z dostupných uživatelů. Například _Rae Huff_.

2. Vyberte **trvale odstranit**.

    ![Stránka Smazané uživatele se zvýrazněnou možností Obnovit uživatele](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>Další kroky
Po obnovení nebo odstranění uživatelů můžete provést následující základní procesy:

- [Přidání nebo odstranění uživatelů](add-users-azure-active-directory.md)

- [Přiřazení rolí uživatelům](active-directory-users-assign-role-azure-portal.md)

- [Přidání nebo změna informací o profilu](active-directory-users-profile-azure-portal.md)

- [Přidání uživatelů typu Host z jiné organizace](../b2b/what-is-b2b.md)

Další informace o dalších dostupných úlohách správy uživatelů, [dokumentaci pro správu uživatelů Azure AD](../users-groups-roles/index.yml).
