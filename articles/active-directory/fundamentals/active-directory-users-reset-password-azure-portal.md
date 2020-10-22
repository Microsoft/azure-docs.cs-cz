---
title: Resetování hesla uživatele – Azure Active Directory | Microsoft Docs
description: Pokyny, jak resetovat heslo uživatele pomocí Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 09/05/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 397c74203aae2f52ce81844695266cc36fdf3042
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370895"
---
# <a name="reset-a-users-password-using-azure-active-directory"></a>Resetování hesla uživatele pomocí Azure Active Directory

Jako správce můžete resetovat heslo uživatele, pokud je zapomenuté heslo, pokud se uživatel zamkne ze zařízení nebo pokud uživatel nikdy neobdržel heslo.

>[!Note]
>Pokud je váš tenant služby Azure AD domovským adresářem pro uživatele, nebudete moci resetovat heslo. To znamená, že pokud se uživatel přihlašuje k vaší organizaci pomocí účtu z jiné organizace, účet Microsoft nebo účtu Google, nebudete moci resetovat heslo.<br><br>Pokud má váš uživatel zdrojovou autoritu jako Windows Server Active Directory, budete moct heslo resetovat jenom v případě, že jste zapnuli zpětný zápis hesla.<br><br>Pokud má váš uživatel zdrojovou autoritu jako externí službu Azure AD, nebudete moct heslo resetovat. Heslo může resetovat jenom uživatel nebo správce v externí službě Azure AD.

>[!Note]
>Pokud nejste správcem a chcete najít pokyny, jak resetovat vlastní pracovní nebo školní heslo, přečtěte si téma [resetování svého pracovního nebo školního](../user-help/active-directory-passwords-update-your-own-password.md)hesla.

## <a name="to-reset-a-password"></a>Resetování hesla

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) jako správce uživatele nebo správce hesel. Další informace o dostupných rolích najdete v tématu [přiřazení rolí správce v Azure Active Directory](../roles/permissions-reference.md#available-roles) .

2. Vyberte **Azure Active Directory**, vyberte **Uživatelé**, vyhledejte a vyberte uživatele, který vyžaduje resetování, a pak vyberte **resetovat heslo**.

    Zobrazí se stránka **Alain Charon-Profile** s možností **resetovat heslo** .

    ![Stránka profilu uživatele s zvýrazněnou možností resetovat heslo](media/active-directory-users-reset-password-azure-portal/user-profile-reset-password-link.png)

3. Na stránce **resetovat heslo** vyberte **resetovat heslo**.

    > [!Note]
    > Při použití Azure Active Directory se pro uživatele automaticky vygeneruje dočasné heslo. Při používání služby Active Directory v místním prostředí můžete pro uživatele vytvořit heslo.

4. Zkopírujte heslo a poskytněte ho uživateli. Uživatel bude muset změnit heslo při příštím procesu přihlašování.

    >[!Note]
    >Dočasné heslo je platné i po vypršení platnosti. Až se uživatel příště přihlásí, heslo bude pořád fungovat bez ohledu na to, kolik času uplynulo od vygenerování dočasného hesla.

## <a name="next-steps"></a>Další kroky

Po resetování hesla uživatele můžete provádět následující základní procesy:

- [Přidání nebo odstranění uživatelů](add-users-azure-active-directory.md)

- [Přiřazení rolí uživatelům](active-directory-users-assign-role-azure-portal.md)

- [Přidat nebo změnit informace o profilu](active-directory-users-profile-azure-portal.md)

- [Vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md)

Nebo můžete provádět složitější uživatelské scénáře, jako je přiřazení delegátů, používání zásad a sdílení uživatelských účtů. Další informace o dalších dostupných akcích najdete v tématu [Azure Active Directory dokumentace správy uživatelů](../enterprise-users/index.yml).
