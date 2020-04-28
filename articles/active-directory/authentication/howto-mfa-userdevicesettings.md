---
title: Správa uživatelských nastavení pro Azure Multi-Factor Authentication – Azure Active Directory
description: Přečtěte si, jak můžete nakonfigurovat Azure Active Directory nastavení uživatele pro Azure Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 048224a55c2bbcbc99281d070d88d34e2dc77168
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81309757"
---
# <a name="manage-user-settings-for-azure-multi-factor-authentication"></a>Správa uživatelských nastavení pro Azure Multi-Factor Authentication

Pro usnadnění správy uživatelů Azure Multi-Factor Authentication můžete vyžadovat, aby uživatelé resetoval heslo, znovu zaregistrovali MFA nebo odvolali stávající relace MFA. Pro uživatele, kteří mají definovaná hesla aplikací, můžete také tato hesla odstranit, což způsobí, že se starší verze ověřování v těchto aplikacích nezdařila. Tyto akce mohou být nezbytné v případě, že potřebujete poskytnout pomoc uživateli nebo chcete resetovat stav zabezpečení.

## <a name="manage-user-authentication-options"></a>Spravovat možnosti ověřování uživatelů

Pokud jste přiřadili roli *Správce ověřování* , můžete vyžadovat, aby uživatelé resetoval heslo, znovu zaregistrovali MFA nebo odvolali stávající relace MFA z jejich uživatelského objektu. Pokud chcete spravovat uživatelská nastavení, proveďte následující kroky:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně vyberte **Azure Active Directory** > **Uživatelé** > **Všichni uživatelé**.
1. Zvolte uživatele, u kterého chcete provést akci, a vyberte **metody ověřování**. V horní části okna vyberte jednu z následujících možností pro uživatele:
   - **Resetovat heslo** obnoví heslo uživatele a přiřadí dočasné heslo, které se musí při příštím přihlášení změnit.
   - **Vyžadovat opětovnou registraci MFA** znamená, že když se uživatel poprvé přihlásí, vyžádá si, aby nastavil novou metodu ověřování MFA.
   - **Odvolání relací MFA** vymaže relace uživatele, které se pamatuje, a vyžaduje, aby při dalším požadavku na zařízení prováděli MFA.

   ![Správa metod ověřování z Azure Portal](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

## <a name="delete-users-existing-app-passwords"></a>Odstranit stávající hesla uživatelů aplikací

V případě potřeby můžete odstranit všechna hesla aplikací, která uživatel vytvořil. Neprohlížečové aplikace, které jsou přidružené k těmto heslům aplikace, přestanou fungovat, dokud se nevytvoří nové heslo aplikace. K provedení této akce jsou vyžadována oprávnění *globálního správce* .

Pokud chcete odstranit hesla aplikací uživatele, proveďte následující kroky:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně vyberte **Azure Active Directory** > **Uživatelé** > **Všichni uživatelé**.
1. Vyberte **Multi-Factor Authentication**. Pokud chcete zobrazit tuto možnost nabídky, možná se budete muset posunout doprava. Kliknutím na ukázkový snímek obrazovky zobrazíte celé Azure Portal okno a umístění nabídky:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Výběr Multi-Factor Authentication v okně uživatelé ve službě Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Zaškrtněte políčko u uživatelů, které chcete spravovat. Zobrazí se seznam možností rychlého kroku na pravé straně.
1. Vyberte **Spravovat uživatelská nastavení**a potom zaškrtněte políčko **Odstranit všechna existující hesla aplikací vytvořená vybranými uživateli**, jak je znázorněno v následujícím příkladu: odstranit ![všechna existující hesla aplikací.](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. Vyberte **Uložit**a pak **Zavřít**.

## <a name="next-steps"></a>Další kroky

Tento článek pomohl nakonfigurovat jednotlivá uživatelská nastavení. Pokud chcete nakonfigurovat nastavení služby Azure Multi-Factor Authentication, přečtěte si téma [Konfigurace nastavení azure Multi-Factor Authentication](howto-mfa-mfasettings.md) .

Pokud uživatelé potřebují nápovědu, přečtěte si [příručku pro uživatele pro Azure Multi-Factor Authentication](../user-help/multi-factor-authentication-end-user.md).
