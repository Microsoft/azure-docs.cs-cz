---
title: Obnovit nebo trvale odebrat nedávno odstraněného uživatele – Azure Active Directory | Dokumentace Microsoftu
description: Postup zobrazení obnovitelné uživatelů, obnovení odstraněného uživatele nebo trvale odstranit uživatele v Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d69bd931f2f8c72fd1e6fc79c16662ea367617d6
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58802010"
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
1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) pomocí účtu globálního správce organizace.

2. Vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **odstraněných uživatelů**.

    Projděte si seznam uživatelů, kteří jsou k dispozici pro obnovení.

    ![Uživatelé – stránka odstraněné uživatele s uživateli, které je možné obnovit](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Obnovení nedávno odstraněného uživatele

Při odstranění uživatelského účtu z organizace. účet je v pozastaveném stavu a všechny související organizace informace je zachováno. Při obnovení uživatele se také obnoví informace o této organizaci.

> [!Note]
> Po obnovení uživatele se licence, které byly přiřazeny uživateli v okamžiku odstranění také obnoví i v případě, že nejsou k dispozici pro tyto licence žádné licence. Pokud pak spotřebovávají více, než jste zakoupili více licencí, vaše organizace může být dočasně nesplňuje požadavky pro využití licencí.

### <a name="to-restore-a-user"></a>Chcete-li obnovit uživatele
1. Na **uživatelé – uživatelé se odstranili** stránky, vyhledejte a vyberte jednu z dostupných uživatelů. Například _Mary Parker_.

2. Vyberte **obnovení uživatele**.

    ![Uživatelé – stránka odstraněných uživatelů se zvýrazněnou možností obnovení uživatele](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>Trvale odstranit uživatele
Bez čekání na 30 dnů pro automatické odstranění můžete trvale odstranit uživatele z vaší organizace. Trvale odstraněného uživatele nelze obnovit, jiný správce, ani zákaznickou podporu Microsoftu.

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

- [Přidat uživatele typu Host z jiné organizace](../b2b/what-is-b2b.md)

Další informace o dalších úlohách správy k dispozici uživatele [Správa dokumentace pro uživatele Azure AD](../users-groups-roles/index.yml).
