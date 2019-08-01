---
title: Přidat nebo aktualizovat informace o profilu uživatele – Azure Active Directory | Dokumentace Microsoftu
description: Pokyny ohledně toho, jak přidat informace do profilu uživatele v Azure Active Directory, včetně obrázků a úlohy podrobnosti.
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
ms.openlocfilehash: 4002c14ed6340a0e1a89ee295d47c98436515775
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561782"
---
# <a name="add-or-update-a-users-profile-information-using-azure-active-directory"></a>Přidat nebo aktualizovat informace o profilu uživatele pomocí služby Azure Active Directory
Přidejte informace o profilu uživatele, včetně profilový obrázek, informace specifické pro úlohy a některá nastavení pomocí Azure Active Directory (Azure AD). Další informace o přidávání nových uživatelů najdete v tématu [postup přidání nebo odstranění uživatelů ve službě Azure Active Directory](add-users-azure-active-directory.md).

## <a name="add-or-change-profile-information"></a>Přidání nebo změně informací profilu
Jak uvidíte, existuje další informace k dispozici v profilu uživatele, než co budete moci přidat při vytváření uživatele. Tyto další informace je volitelné a můžete přidat podle potřeby vaší organizací.

## <a name="to-add-or-change-profile-information"></a>Přidání nebo změně informací profilu
1. Přihlaste se k [Azure Portal](https://portal.azure.com/) jako správce uživatele pro organizaci.

2. Vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte uživatele. Například _Alain Charon_.

    **Alain Charon – profil** se zobrazí stránka.

    ![Stránka profilu uživatele, včetně upravitelné informace](media/active-directory-users-profile-azure-portal/user-profile-all-blade.png)

3. Vyberte **upravit** volitelně přidat nebo aktualizovat informace obsažené v každém dostupné oddíly.

    ![Stránka profilu uživatele, zobrazuje upravitelné oblasti](media/active-directory-users-profile-azure-portal/user-profile-edit.png)

    - **Profilový obrázek.** Vyberte obrázek miniatury pro uživatelský účet. Tento obrázek se zobrazí v Azure Active Directory a na stránkách osobní uživatele, jako je například myapps.microsoft.com stránky.

    - **Identita.** Přidejte nebo aktualizujte další hodnotu identity pro uživatele, například název svatby příjmení. Tento název můžete nastavit nezávisle na hodnotách jméno a příjmení. Můžete ho například použít k zahrnutí iniciály, názvu společnosti nebo ke změně pořadí zobrazených názvů. V jiném příkladu můžete pro dva uživatele, jejichž názvy mají "Novák zelený", použít řetězec identity k nastavení jejich názvů na "Novák B. zelený" ' Novák R. zelená (Contoso).

    - **Informace o úloze.** Přidáte jakýchkoli informací souvisejících s úlohami, například pracovní pozice uživatele, oddělení nebo správce.

    - **Nastavení.** Rozhodněte, jestli uživatel může přihlásit k tenantovi Azure Active Directory. Můžete také zadat globální umístění uživatele.

    - **Kontaktní informace.** Přidáte všechny příslušné kontaktní informace pro uživatele. Například adresu ulice nebo číslo mobilního telefonu.

    - **Kontaktní údaje pro ověření.** Zkontrolujte tyto informace, abyste měli jistotu, že není aktivní telefonní číslo a e-mailovou adresu uživatele. Tyto informace slouží službou Azure Active Directory, abyste měli jistotu, že uživatel je ve skutečnosti uživatele během přihlášení. Kontaktní údaje pro ověření můžete aktualizovat jenom globální správce.

4. Vyberte **Uložit**.

    Všechny změny se uloží pro uživatele.

    >[!Note]
    >Identity a kontaktní údaje nebo informace o úloze pro uživatele, jehož zdrojem autorita je Windows Server Active Directory aktualizovat, musíte použít Windows Server Active Directory. Po dokončení aktualizace, je nutné počkat na další cyklus synchronizace dokončit předtím, než uvidíte změny.

## <a name="next-steps"></a>Další postup
Po aktualizaci profily uživatelů, můžete provést následující základní procesy:

- [Přidání nebo odstranění uživatelů](add-users-azure-active-directory.md)

- [Přiřazení rolí uživatelům](active-directory-users-assign-role-azure-portal.md)

- [Vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md)

Nebo můžete provádět další úkoly při správě uživatelů, jako je například přiřazení delegáty, pomocí zásad, sdílení a uživatelské účty. Další informace o dalších dostupných akcí najdete v tématu [dokumentace ke službě Azure Active Directory uživatele management](../users-groups-roles/index.yml).
