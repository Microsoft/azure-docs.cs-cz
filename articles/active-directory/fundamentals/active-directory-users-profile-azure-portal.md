---
title: Přidání nebo aktualizace informací o profilu uživatele – Azure AD
description: Pokyny k přidání informací do profilu uživatele v Azure Active Directory, včetně podrobností o obrázku a úloze.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 04/11/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63b716aa22954af51a9eb53a0d04a8299d348c71
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87797233"
---
# <a name="add-or-update-a-users-profile-information-using-azure-active-directory"></a>Přidání nebo aktualizace informací o profilu uživatele pomocí Azure Active Directory
Přidejte informace o profilu uživatele, včetně profilového obrázku, informací specifických pro úlohu a některých nastavení pomocí Azure Active Directory (Azure AD). Další informace o přidání nových uživatelů najdete v tématu [Postup přidání nebo odstranění uživatelů v Azure Active Directory](add-users-azure-active-directory.md).

## <a name="add-or-change-profile-information"></a>Přidat nebo změnit informace o profilu
Jak vidíte, v profilu uživatele jsou k dispozici další informace, než kolik máte v průběhu vytváření uživatele možné přidat. Všechny tyto další informace jsou volitelné a je možné je podle potřeby přidat do vaší organizace.

## <a name="to-add-or-change-profile-information"></a>Přidání nebo změna informací o profilu
1. Přihlaste se k [Azure Portal](https://portal.azure.com/) jako správce uživatele pro organizaci.

2. Vyberte **Azure Active Directory**, vyberte **uživatele**a pak vyberte uživatele. Například _Alain Charon_.

    Zobrazí se stránka **Alain Charon-Profile** .

    ![Stránka profilu uživatele, včetně upravitelných informací](media/active-directory-users-profile-azure-portal/user-profile-all-blade.png)

3. Pokud chcete přidat nebo aktualizovat informace obsažené v jednotlivých dostupných oddílech, vyberte **Upravit** .

    ![Stránka profilu uživatele zobrazující upravitelné oblasti](media/active-directory-users-profile-azure-portal/user-profile-edit.png)

    - **Profilový obrázek** Vyberte obrázek miniatury pro uživatelský účet. Tento obrázek se zobrazí v Azure Active Directory a na osobních stránkách uživatele, jako je například stránka myapps.microsoft.com.

    - **Odcizen.** Přidejte nebo aktualizujte další hodnotu identity pro uživatele, například název svatby příjmení. Tento název můžete nastavit nezávisle na hodnotách jméno a příjmení. Můžete ho například použít k zahrnutí iniciály, názvu společnosti nebo ke změně pořadí zobrazených názvů. V jiném příkladu můžete pro dva uživatele, jejichž názvy mají "Novák zelený", použít řetězec identity k nastavení jejich názvů na "Novák B. zelený" ' Novák R. zelená (Contoso).

    - **Informace o úloze** Přidejte jakékoli informace související s úlohou, jako je například pracovní pozice, oddělení nebo manažer uživatele.

    - **Možnost.** Rozhodněte, jestli se uživatel může přihlásit k Azure Active Directory tenanta. Můžete také zadat globální umístění uživatele.

    - **Kontaktní údaje.** Přidejte jakékoli relevantní kontaktní informace pro uživatele, s výjimkou telefonu nebo mobilního kontaktu některých uživatelů (pro uživatele v rolích správce se může aktualizovat jenom globální správce).

    - **Kontaktní údaje pro ověření** Ověřte tyto informace a ujistěte se, že pro uživatele existuje aktivní telefonní číslo a e-mailová adresa. Tyto informace používá Azure Active Directory k tomu, abyste se ujistili, že uživatel je během přihlašování skutečně uživatelem. Kontaktní údaje pro ověření se dají aktualizovat jenom globálním správcem.

4. Vyberte **Uložit**.

    Všechny vaše změny se uloží pro uživatele.

    >[!Note]
    >Abyste mohli aktualizovat identitu, kontaktní údaje nebo informace o úlohách pro uživatele, jejichž zdroj oprávnění je Windows Server Active Directory, musíte použít Windows Server Active Directory. Po dokončení aktualizace musíte počkat na dokončení dalšího cyklu synchronizace, než se změny projeví.

## <a name="next-steps"></a>Další kroky
Po aktualizaci profilů uživatelů můžete provádět následující základní procesy:

- [Přidání nebo odstranění uživatelů](add-users-azure-active-directory.md)

- [Přiřazení rolí uživatelům](active-directory-users-assign-role-azure-portal.md)

- [Vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md)

Nebo můžete provádět další úlohy správy uživatelů, jako je přiřazení delegátů, používání zásad a sdílení uživatelských účtů. Další informace o dalších dostupných akcích najdete v tématu [Azure Active Directory dokumentace správy uživatelů](../users-groups-roles/index.yml).
