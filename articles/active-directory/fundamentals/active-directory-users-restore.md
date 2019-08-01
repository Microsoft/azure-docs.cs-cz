---
title: Obnovit nebo trvale odebrat nedávno odstraněného uživatele – Azure Active Directory | Dokumentace Microsoftu
description: Postup zobrazení obnovitelné uživatelů, obnovení odstraněného uživatele nebo trvale odstranit uživatele v Azure Active Directory.
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
ms.openlocfilehash: 9864634020ad115a48bdb70a6736733919d0f0d3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561675"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Obnovení nebo odeberte nedávno odstraněného uživatele pomocí služby Azure Active Directory
Po odstranění uživatele účet zůstane v pozastaveném stavu po dobu 30 dnů. Během tohoto časového období 30 dnů uživatelský účet můžete obnovit, spolu se všemi vlastnostmi. Po uplynutí 30 dnů okno uživatele je automaticky a chcete trvale odstranit.

Můžete zobrazit obnovitelné uživatelům, obnovení odstraněného uživatele nebo trvale odstranit uživatele pomocí Azure Active Directory (Azure AD) na webu Azure Portal.

>[!Important]
>Uživatel se odstranil natrvalo můžete obnovit ani zákaznickou podporu Microsoftu.

## <a name="required-permissions"></a>Požadovaná oprávnění
Musí mít jeden z následujících rolí pro obnovení a trvale odstranit uživatele.

- Globální správce

- Podpora partnerů úrovně 1

- Podpora partnerů úrovně 2

- Správce uživatelů

## <a name="view-your-restorable-users"></a>Zobrazit obnovitelné uživatele
Zobrazí se všichni uživatelé, které byly odstraněny před méně než 30 dny. Tito uživatelé můžou obnovit.

### <a name="to-view-your-restorable-users"></a>Chcete-li zobrazit obnovitelné uživatele
1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí účtu globálního správce organizace.

2. Vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **odstraněných uživatelů**.

    Projděte si seznam uživatelů, kteří jsou k dispozici pro obnovení.

    ![Uživatelé – stránka odstraněné uživatele s uživateli, které je možné obnovit](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Obnovení nedávno odstraněného uživatele

Když se uživatelský účet z organizace odstraní, účet je v pozastaveném stavu a zachová se všechny související informace o organizaci. Při obnovení uživatele se obnoví i informace o této organizaci.

> [!Note]
> Po obnovení uživatele se licence, které byly přiřazeny uživateli v době odstranění, obnoví i v případě, že pro tyto licence nejsou k dispozici žádná místa. Pokud pak budete spotřebovávat více licencí, než jste koupili, může být vaše organizace dočasně nekompatibilní s používáním licencí.

### <a name="to-restore-a-user"></a>Chcete-li obnovit uživatele
1. Na **uživatelé – uživatelé se odstranili** stránky, vyhledejte a vyberte jednu z dostupných uživatelů. Například _Mary Parker_.

2. Vyberte **obnovení uživatele**.

    ![Uživatelé – stránka odstraněných uživatelů se zvýrazněnou možností obnovení uživatele](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>Trvale odstranit uživatele
Uživatele můžete z vaší organizace trvale odstranit, aniž byste čekali na automatické odstranění po dobu 30 dnů. Trvale odstraněného uživatele nelze obnovit, jiný správce, ani zákaznickou podporu Microsoftu.

>[!Note]
>Pokud je trvale odstranit uživatele omylem, budete mít k vytvoření nového uživatele a všechny předchozí informace zadat ručně. Další informace o vytvoření nového uživatele najdete v tématu [přidání nebo odstranění uživatelů](add-users-azure-active-directory.md).

### <a name="to-permanently-delete-a-user"></a>Chcete-li trvale odstranit uživatele

1. Na **uživatelé – uživatelé se odstranili** stránky, vyhledejte a vyberte jednu z dostupných uživatelů. Například _Rae Huff_.

2. Vyberte **trvale odstranit**.

    ![Uživatelé – stránka odstraněných uživatelů se zvýrazněnou možností obnovení uživatele](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>Další postup
Poté, co jste obnovit nebo odstranit uživatele, můžete provést následující základní procesy:

- [Přidání nebo odstranění uživatelů](add-users-azure-active-directory.md)

- [Přiřazení rolí uživatelům](active-directory-users-assign-role-azure-portal.md)

- [Přidání nebo změně informací profilu](active-directory-users-profile-azure-portal.md)

- [Přidat uživatele typu host z jiné organizace](../b2b/what-is-b2b.md)

Další informace o dalších dostupných úkolech správy uživatelů najdete v [dokumentaci správy uživatelů Azure AD](../users-groups-roles/index.yml).
