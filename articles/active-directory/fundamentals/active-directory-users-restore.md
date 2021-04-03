---
title: Obnovit nebo trvale odebrat nedávno odstraněný uživatel – Azure AD
description: Jak zobrazit uživatele obnovitelné, obnovit odstraněné uživatele nebo trvale odstranit uživatele pomocí Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/23/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd4d42e1cafa58aaf6a47188c9e86c24ac175047
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92514385"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Obnovení nebo odebrání nedávno odstraněného uživatele pomocí Azure Active Directory
Po odstranění uživatele zůstane účet v pozastaveném stavu po dobu 30 dnů. Během tohoto 30denní okna se může uživatelský účet obnovit spolu se všemi jeho vlastnostmi. Po uplynutí 30 dnů se automaticky spustí proces trvalého odstranění.

Můžete si Zobrazit uživatele obnovitelné, obnovit odstraněného uživatele nebo trvale odstranit uživatele pomocí Azure Active Directory (Azure AD) v Azure Portal.

>[!Important]
>Ani vy ani zákaznická podpora Microsoftu nemůžete obnovit trvale odstraněného uživatele.

## <a name="required-permissions"></a>Požadovaná oprávnění
Chcete-li obnovit a trvale odstranit uživatele, musíte mít jednu z následujících rolí.

- Globální správce

- Podpora partnerů Tier1

- Podpora partnerů 2

- Správce uživatelů

## <a name="view-your-restorable-users"></a>Zobrazit uživatele obnovitelné
Můžete zobrazit všechny uživatele, kteří byli odstraněni před méně než 30 dny. Tyto uživatele je možné obnovit.

### <a name="to-view-your-restorable-users"></a>Zobrazení uživatelů obnovitelné
1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí účtu globálního správce organizace.

2. Vyberte **Azure Active Directory**, vyberte **Uživatelé** a pak vyberte **odstraněné uživatele**.

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

    ![Uživatelé – stránka odstraněné uživatele s zvýrazněnou možností odstranit uživatele](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>Další kroky
Po obnovení nebo odstranění uživatelů můžete:

- [Přidání nebo odstranění uživatelů](add-users-azure-active-directory.md)

- [Přiřazení rolí uživatelům](active-directory-users-assign-role-azure-portal.md)

- [Přidat nebo změnit informace o profilu](active-directory-users-profile-azure-portal.md)

- [Přidat uživatele typu host z jiné organizace](../external-identities/what-is-b2b.md)

Další informace o dalších dostupných úkolech správy uživatelů najdete v [dokumentaci správy uživatelů Azure AD](../enterprise-users/index.yml).
