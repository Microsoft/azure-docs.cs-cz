---
title: Obnovit nebo trvale odebrat nedávno odstraněný uživatel – Azure AD
description: Jak zobrazit uživatele obnovitelné, obnovit odstraněné uživatele nebo trvale odstranit uživatele pomocí Azure Active Directory.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75422865"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Obnovení nebo odebrání nedávno odstraněného uživatele pomocí Azure Active Directory
Po odstranění uživatele zůstane účet v pozastaveném stavu po dobu 30 dnů. Během tohoto 30denní okna se může uživatelský účet obnovit spolu se všemi jeho vlastnostmi. Po uplynutí tohoto 30denní okna se uživatel automaticky a trvale odstraní.

Můžete si Zobrazit uživatele obnovitelné, obnovit odstraněného uživatele nebo trvale odstranit uživatele pomocí Azure Active Directory (Azure AD) v Azure Portal.

>[!Important]
>Ani vy ani zákaznická podpora Microsoftu nemůžete obnovit trvale odstraněného uživatele.

## <a name="required-permissions"></a>Požadovaná oprávnění
Chcete-li obnovit a trvale odstranit uživatele, musíte mít jednu z následujících rolí.

- Globální správce

- Podpora partnerů Tier1

- Podpora partnerů 2

- Správce uživatele

## <a name="view-your-restorable-users"></a>Zobrazit uživatele obnovitelné
Můžete zobrazit všechny uživatele, kteří byli odstraněni před méně než 30 dny. Tyto uživatele je možné obnovit.

### <a name="to-view-your-restorable-users"></a>Zobrazení uživatelů obnovitelné
1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí účtu globálního správce organizace.

2. Vyberte **Azure Active Directory**, vyberte **Uživatelé**a pak vyberte **odstraněné uživatele**.

    Zkontrolujte seznam uživatelů, které jsou k dispozici pro obnovení.

    ![Uživatelé – stránka odstraněné uživatele s uživateli, kteří se pořád můžou obnovit](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Obnovení nedávno odstraněného uživatele

Když se uživatelský účet z organizace odstraní, účet je v pozastaveném stavu a zachová se všechny související informace o organizaci. Při obnovení uživatele se obnoví i informace o této organizaci.

> [!Note]
> Po obnovení uživatele se licence, které byly přiřazeny uživateli v době odstranění, obnoví i v případě, že pro tyto licence nejsou k dispozici žádná místa. Pokud pak budete spotřebovávat více licencí, než jste koupili, může být vaše organizace dočasně nekompatibilní s používáním licencí.

### <a name="to-restore-a-user"></a>Obnovení uživatele
1. Na stránce **Uživatelé – odstraně uživatelé** vyhledejte a vyberte jednoho z dostupných uživatelů. Například _Marie Parker_.

2. Vyberte **Obnovit uživatele**.

    ![Uživatelé – stránka odstraněné uživatele s zvýrazněnou možností obnovit uživatele](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>Trvalé odstranění uživatele
Uživatele můžete z vaší organizace trvale odstranit, aniž byste čekali na automatické odstranění po dobu 30 dnů. Trvale odstraněného uživatele nemůžete obnovit vámi, jiným správcem ani zákaznickou podporou Microsoftu.

>[!Note]
>Pokud uživatele trvale odstraníte omylem, budete muset vytvořit nového uživatele a ručně zadat všechny předchozí informace. Další informace o vytvoření nového uživatele najdete v tématu [Přidání nebo odstranění uživatelů](add-users-azure-active-directory.md).

### <a name="to-permanently-delete-a-user"></a>Trvalé odstranění uživatele

1. Na stránce **Uživatelé – odstraně uživatelé** vyhledejte a vyberte jednoho z dostupných uživatelů. Například _Rae Huff_.

2. Vyberte možnost **odstranit trvale**.

    ![Uživatelé – stránka odstraněné uživatele s zvýrazněnou možností obnovit uživatele](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>Další kroky
Po obnovení nebo odstranění uživatelů můžete provádět následující základní procesy:

- [Přidání nebo odstranění uživatelů](add-users-azure-active-directory.md)

- [Přiřazení rolí uživatelům](active-directory-users-assign-role-azure-portal.md)

- [Přidat nebo změnit informace o profilu](active-directory-users-profile-azure-portal.md)

- [Přidat uživatele typu host z jiné organizace](../b2b/what-is-b2b.md)

Další informace o dalších dostupných úkolech správy uživatelů najdete v [dokumentaci správy uživatelů Azure AD](../users-groups-roles/index.yml).
