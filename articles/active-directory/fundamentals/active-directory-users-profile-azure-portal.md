---
title: Jak přidat nebo aktualizovat informace o profilu uživatele v Azure Active Directory | Dokumentace Microsoftu
description: Přidejte informace o profilu uživatele, včetně profilový obrázek, informace specifické pro úlohy a některá nastavení v Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.openlocfilehash: 7f37ee513de016e024327698d5a8181c9c62d7aa
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45604803"
---
# <a name="how-to-add-or-update-a-users-profile-information-using-azure-active-directory"></a>Postupy: Přidat nebo aktualizovat informace o profilu uživatele pomocí služby Azure Active Directory
Přidejte informace o profilu uživatele, včetně profilový obrázek, informace specifické pro úlohy a některá nastavení pomocí Azure Active Directory (Azure AD). Další informace o přidávání nových uživatelů najdete v tématu [postup přidání nebo odstranění uživatelů ve službě Azure Active Directory](add-users-azure-active-directory.md).

## <a name="add-or-change-profile-information"></a>Přidání nebo změně informací profilu
Jak uvidíte, existuje další informace k dispozici v profilu uživatele, než co budete moci přidat při vytváření uživatele. Tyto další informace je volitelné a můžete přidat podle potřeby vaší organizací.

## <a name="to-add-or-change-profile-information"></a>Přidání nebo změně informací profilu
1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako globální správce nebo Správce uživatelů adresáře.

2. Vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte uživatele. Například _Alain Charon_.

    **Alain Charon – profil** se zobrazí stránka.

    ![Stránka profilu uživatele, včetně upravitelné informace](media/active-directory-users-profile-azure-portal/user-profile-all-blade.png)

3. Vyberte **upravit** volitelně přidat nebo aktualizovat informace obsažené v každém dostupné oddíly.

    ![Stránka profilu uživatele, zobrazuje upravitelné oblasti](media/active-directory-users-profile-azure-portal/user-profile-edit.png)

    - **Profilový obrázek.** Vyberte obrázek miniatury pro uživatelský účet. Tento obrázek se zobrazí v Azure Active Directory a na stránkách osobní uživatele, jako je například myapps.microsoft.com stránky.

    - **Identita.** Přidáte všechny informace související s účtem, například vdaná příjmení nebo změněných uživatelské jméno. 

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
