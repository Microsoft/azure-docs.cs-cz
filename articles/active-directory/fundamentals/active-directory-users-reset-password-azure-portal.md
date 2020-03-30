---
title: Resetování hesla uživatele – Služba Azure Active Directory | Dokumenty společnosti Microsoft
description: Pokyny k resetování hesla uživatele pomocí služby Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4fdbbd4d71a9c97259678413cd9e59ee8aeae6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69032668"
---
# <a name="reset-a-users-password-using-azure-active-directory"></a>Resetování hesla uživatele pomocí služby Azure Active Directory

Jako správce můžete obnovit heslo uživatele, pokud je heslo zapomenuto, pokud se uživatel uzamkne ze zařízení nebo pokud uživatel heslo nikdy neobdržel.

>[!Note]
>Pokud váš klient Azure AD je domovský adresář pro uživatele, nebudete moct resetovat své heslo. To znamená, že pokud se uživatel přihlašuje k vaší organizaci pomocí účtu jiné organizace, účtu Microsoft nebo účtu Google, nebudete moci resetovat jeho heslo.<br><br>Pokud má uživatel zdroj oprávnění jako služba Windows Server Active Directory, budete moci obnovit heslo pouze v případě, že jste zapnuli zpětný zápis hesla.<br><br>Pokud má váš uživatel zdroj autority jako externí Azure AD, nebudete moct obnovit heslo. Heslo může resetovat jenom uživatel nebo správce v externí azure ad.

>[!Note]
>Pokud nejste správce a místo toho hledáte pokyny, jak resetovat vlastní pracovní nebo školní heslo, přečtěte si článek [Resetování pracovního nebo školního hesla](../user-help/active-directory-passwords-update-your-own-password.md).

## <a name="to-reset-a-password"></a>Resetování hesla

1. Přihlaste se k [portálu Azure](https://portal.azure.com/) jako správce uživatele nebo správce hesel. Další informace o dostupných rolích najdete v tématu [Přiřazení rolí správce ve službě Azure Active Directory.](../users-groups-roles/directory-assign-admin-roles.md#available-roles)

2. Vyberte **Azure Active Directory**, vyberte **Uživatelé**, vyhledejte a vyberte uživatele, který potřebuje resetování, a pak vyberte **Obnovit heslo**.

    Zobrazí se stránka **Alain Charon - Profil** s možností **Obnovit heslo.**

    ![Stránka profilu uživatele se zvýrazněnou možností Obnovit heslo](media/active-directory-users-reset-password-azure-portal/user-profile-reset-password-link.png)

3. Na stránce **Obnovit heslo** vyberte **Obnovit heslo**.

    > [!Note]
    > Při použití služby Azure Active Directory se automaticky generuje dočasné heslo pro uživatele. Při místním použití služby Active Directory vytvořte heslo pro uživatele.

4. Zkopírujte heslo a předajte ho uživateli. Uživatel bude muset změnit heslo během dalšího procesu přihlášení.

    >[!Note]
    >Platnost dočasného hesla nikdy nevyprší. Při příštím přihlášení bude heslo stále fungovat bez ohledu na to, kolik času uplynulo od vygenerování dočasného hesla.

## <a name="next-steps"></a>Další kroky

Po resetování hesla uživatele můžete provést následující základní procesy:

- [Přidání nebo odstranění uživatelů](add-users-azure-active-directory.md)

- [Přiřazení rolí uživatelům](active-directory-users-assign-role-azure-portal.md)

- [Přidání nebo změna informací o profilu](active-directory-users-profile-azure-portal.md)

- [Vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md)

Nebo můžete provádět složitější uživatelské scénáře, jako je například přiřazení delegátů, použití zásad a sdílení uživatelských účtů. Další informace o dalších dostupných akcích naleznete v [dokumentaci ke správě uživatelů služby Azure Active Directory](../users-groups-roles/index.yml).
