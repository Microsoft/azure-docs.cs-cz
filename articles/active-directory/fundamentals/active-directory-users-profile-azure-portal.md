---
title: Přidání nebo aktualizace informací o profilu uživatele – Azure AD
description: Pokyny, jak přidat informace do profilu uživatele ve službě Azure Active Directory, včetně obrázku a podrobností o úloze.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ba51b0ad7f314058f34092dff4e26411020086a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422884"
---
# <a name="add-or-update-a-users-profile-information-using-azure-active-directory"></a>Přidání nebo aktualizace informací o profilu uživatele pomocí Azure Active Directory
Přidejte informace o profilu uživatele, včetně profilového obrázku, informací o konkrétních úlohách a některých nastavení pomocí služby Azure Active Directory (Azure AD). Další informace o přidávání nových uživatelů najdete v [tématu Přidání nebo odstranění uživatelů ve službě Azure Active Directory](add-users-azure-active-directory.md).

## <a name="add-or-change-profile-information"></a>Přidání nebo změna informací o profilu
Jak uvidíte, v profilu uživatele je k dispozici více informací, než kolik můžete přidat během vytváření uživatele. Všechny tyto další informace jsou volitelné a mohou být přidány podle potřeby vaší organizace.

## <a name="to-add-or-change-profile-information"></a>Přidání nebo změna informací o profilu
1. Přihlaste se k [portálu Azure](https://portal.azure.com/) jako správce uživatele pro organizaci.

2. Vyberte **Službu Azure Active Directory**, vyberte **Uživatelé**a pak vyberte uživatele. Například _Alain Charon_.

    Zobrazí se stránka **Alain Charon - Profil.**

    ![Stránka profilu uživatele, včetně upravitelných informací](media/active-directory-users-profile-azure-portal/user-profile-all-blade.png)

3. Vyberte **Upravit,** chcete-li volitelně přidat nebo aktualizovat informace obsažené v každém z dostupných oddílů.

    ![Stránka profilu uživatele s upravovanými oblastmi](media/active-directory-users-profile-azure-portal/user-profile-edit.png)

    - **Profilový obrázek.** Vyberte miniaturu uživatelského účtu. Tento obrázek se zobrazí ve službě Azure Active Directory a na osobních stránkách uživatele, jako je například stránka myapps.microsoft.com.

    - **Identity.** Přidejte nebo aktualizujte další hodnotu identity pro uživatele, například manželské příjmení. Tento název můžete nastavit nezávisle na hodnotách Jméno a Příjmení. Můžete jej například použít k zahrnutí iniciál, názvu společnosti nebo ke změně posloupnosti zobrazených názvů. V jiném příkladu pro dva uživatele, jejichž jména jsou "Chris Green", můžete použít řetězec Identity k nastavení jejich názvů na "Chris B. Green" 'Chris R. Green (Contoso).'

    - **Informace o práci.** Přidejte všechny informace týkající se úlohy, například pracovní pozici uživatele, oddělení nebo vedoucího.

    - **Nastavení.** Rozhodněte se, jestli se uživatel může přihlásit k tenantovi Služby Azure Active Directory. Můžete také určit globální umístění uživatele.

    - **Kontaktní údaje.** Přidejte všechny relevantní kontaktní informace pro uživatele, s výjimkou telefonních nebo mobilních kontaktních údajů uživatele (pouze globální správce může aktualizovat pro uživatele v rolích správce).

    - **Ověřovací kontaktní údaje.** Ověřte tyto informace, abyste se ujistili, že je pro uživatele aktivní telefonní číslo a e-mailová adresa. Tyto informace používá služba Azure Active Directory k ujistěte se, že uživatel je skutečně uživatel během přihlášení. Ověřovací kontaktní údaje může aktualizovat pouze globální správce.

4. Vyberte **Uložit**.

    Všechny změny jsou uloženy pro uživatele.

    >[!Note]
    >Pomocí služby Windows Server Active Directory je třeba aktualizovat identitu, kontaktní informace nebo informace o úloze pro uživatele, jejichž zdrojem oprávnění je služba Windows Server Active Directory. Po dokončení aktualizace je nutné počkat na dokončení dalšího cyklu synchronizace, než se změny zobrazí.

## <a name="next-steps"></a>Další kroky
Po aktualizaci profilů uživatelů můžete provést následující základní procesy:

- [Přidání nebo odstranění uživatelů](add-users-azure-active-directory.md)

- [Přiřazení rolí uživatelům](active-directory-users-assign-role-azure-portal.md)

- [Vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md)

Nebo můžete provádět další úlohy správy uživatelů, jako je například přiřazení delegátů, použití zásad a sdílení uživatelských účtů. Další informace o dalších dostupných akcích naleznete v [dokumentaci ke správě uživatelů služby Azure Active Directory](../users-groups-roles/index.yml).
