---
title: Přidání nebo odstranění uživatelů – Služba Azure Active Directory | Dokumenty společnosti Microsoft
description: Pokyny, jak přidat nové uživatele nebo odstranit stávající uživatele pomocí služby Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d72616422934501e042375edfb10a25aa27c527
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262110"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Přidání nebo odstranění uživatelů pomocí služby Azure Active Directory

Přidejte nové uživatele nebo odstraňte stávající uživatele z organizace Azure Active Directory (Azure AD). Chcete-li přidat nebo odstranit uživatele, musíte být správcem uživatele nebo globálním správcem.

## <a name="add-a-new-user"></a>Přidání nového uživatele

Nového uživatele můžete vytvořit pomocí portálu Azure Active Directory.

Chcete-li přidat nového uživatele, postupujte takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) jako správce uživatele pro organizaci.

1. Vyhledejte a vyberte *službu Azure Active Directory* na libovolné stránce.

1. Vyberte **Možnost Uživatelé**a potom vyberte **Nový uživatel**.

    ![Přidání uživatele prostřednictvím uživatelů – všichni uživatelé ve službě Azure AD](media/add-users-azure-active-directory/add-user-in-users-all-users.png)

1. Na stránce **Uživatel** zadejte informace pro tohoto uživatele:

   - **Název**. Povinná hodnota. Křestní jméno a příjmení nového uživatele. Například *Mary Parker*.

   - **Uživatelské jméno**. Povinná hodnota. Uživatelské jméno nového uživatele. Například, `mary@contoso.com`.

     Část domény uživatelského jména musí používat buď původní výchozí název domény, * \<název domény>.onmicrosoft.com*nebo vlastní název domény, například *contoso.com*. Další informace o tom, jak vytvořit vlastní název domény, najdete v [tématu Přidání vlastního názvu domény pomocí portálu Azure Active Directory](add-custom-domain.md).

   - **Skupiny**. Volitelně můžete přidat uživatele do jedné nebo více existujících skupin. Uživatele můžete také přidat do skupin později. Další informace o přidávání uživatelů do skupin najdete [v tématu Vytvoření základní skupiny a přidání členů pomocí služby Azure Active Directory](active-directory-groups-create-azure-portal.md).

   - **Role adresáře**: Pokud pro uživatele požadujete oprávnění správce Azure AD, můžete je přidat do role Azure AD. Můžete přiřadit uživatele jako globálního správce nebo jednu nebo více omezených rolí správce ve službě Azure AD. Další informace o přiřazování rolí naleznete v tématu [Jak přiřadit role uživatelům](active-directory-users-assign-role-azure-portal.md).

   - **Informace o úloze**: Zde můžete přidat další informace o uživateli nebo to udělat později. Další informace o přidávání informací o uživateli naleznete v [tématu Přidání nebo změna informací o profilu uživatele](active-directory-users-profile-azure-portal.md).

1. Zkopírujte automaticky vygenerované heslo uvedené v poli **Heslo.** Toto heslo budete muset uživateli zadat, aby se mohl poprvé přihlásit.

1. Vyberte **Vytvořit**.

Uživatel se vytvoří a přidá do vaší organizace Azure AD.

## <a name="add-a-new-guest-user"></a>Přidání nového uživatele typu Host

Můžete také pozvat nového uživatele typu Host ke spolupráci s vaší organizací výběrem **možnosti Pozvat uživatele** na stránce **Nový uživatel.** Pokud jsou nastavení externí spolupráce vaší organizace nakonfigurována tak, abyste mohli pozvat hosty, uživateli bude zaslána pozvánka, kterou musí přijmout, aby mohl začít spolupracovat. Další informace o pozvání uživatelů spolupráce B2B najdete [v tématu Pozvání uživatelů B2B do služby Azure Active Directory.](../b2b/add-users-administrator.md)

## <a name="add-a-consumer-user"></a>Přidání spotřebitele

Mohou existovat scénáře, ve kterých chcete ručně vytvořit spotřebitelské účty v adresáři Azure Active Directory B2C (Azure AD B2C). Další informace o vytváření spotřebitelských účtů najdete [v tématu Vytvoření a odstranění uživatelů příjemce v Azure AD B2C](../../active-directory-b2c/manage-users-portal.md).

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Přidání nového uživatele v hybridním prostředí

Pokud máte prostředí s Azure Active Directory (cloud) a Windows Server Active Directory (místní), můžete přidat nové uživatele synchronizací existujících dat uživatelského účtu. Další informace o hybridních prostředích a uživatelích najdete [v tématu Integrace místních adresářů pomocí služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Odstranění uživatele

Pomocí portálu Azure Active Directory můžete odstranit stávajícího uživatele.

Chcete-li odstranit uživatele, postupujte takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) pomocí účtu správce uživatele pro organizaci.

1. Vyhledejte a vyberte *službu Azure Active Directory* na libovolné stránce.

1. Vyhledejte a vyberte uživatele, kterého chcete odstranit z vašeho klienta Azure AD. Například _Mary Parker_.

1. Vyberte **Odstranit uživatele**.

    ![Uživatelé – stránka Všichni uživatelé se zvýrazněnou možností Odstranit uživatelem](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

Uživatel je odstraněn a již se nezobrazuje na stránce **Uživatelé – všichni uživatelé.** Uživatele můžete vidět na stránce **Smazané uživatele** po dobu následujících 30 dnů a během této doby může být obnoven. Další informace o obnovení uživatele najdete v [tématu Obnovení nebo odebrání naposledy odstraněného uživatele pomocí služby Azure Active Directory](active-directory-users-restore.md).

Při odstranění uživatele jsou všechny licence spotřebované uživatelem zpřístupněny ostatním uživatelům.

>[!Note]
>Ke aktualizaci identity, kontaktních informací nebo informací o úloze pro uživatele, jejichž zdrojem oprávnění je služba Windows Server Active Directory, je nutné použít službu Windows Server Active Directory. Po dokončení aktualizace je nutné počkat na dokončení dalšího cyklu synchronizace, než se změny zobrazí.

## <a name="next-steps"></a>Další kroky

Po přidání uživatelů můžete provést následující základní procesy:

- [Přidání nebo změna informací o profilu](active-directory-users-profile-azure-portal.md)

- [Přiřazení rolí uživatelům](active-directory-users-assign-role-azure-portal.md)

- [Vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md)

- [Práce s dynamickými skupinami a uživateli](../users-groups-roles/groups-create-rule.md)

Nebo můžete provést další úlohy správy uživatelů, například [přidat uživatele typu Host z jiného adresáře](../b2b/what-is-b2b.md) nebo obnovit [odstraněného uživatele](active-directory-users-restore.md). Další informace o dalších dostupných akcích naleznete v [dokumentaci ke správě uživatelů služby Azure Active Directory](../users-groups-roles/index.yml).
