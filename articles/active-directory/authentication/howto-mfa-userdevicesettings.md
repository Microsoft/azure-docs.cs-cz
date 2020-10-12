---
title: Správa uživatelských nastavení pro Azure Multi-Factor Authentication – Azure Active Directory
description: Přečtěte si, jak můžete nakonfigurovat Azure Active Directory nastavení uživatele pro Azure Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49c389170e9d586a2001009226c6542d4d152f7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87418215"
---
# <a name="manage-user-settings-for-azure-multi-factor-authentication"></a>Správa uživatelských nastavení pro Azure Multi-Factor Authentication

Pro usnadnění správy uživatelů Azure Multi-Factor Authentication můžete vyžadovat, aby uživatelé resetoval heslo, znovu zaregistrovali MFA nebo odvolali stávající relace MFA. Pro uživatele, kteří mají definovaná hesla aplikací, můžete také tato hesla odstranit, což způsobí, že se starší verze ověřování v těchto aplikacích nezdařila. Tyto akce mohou být nezbytné v případě, že potřebujete poskytnout pomoc uživateli nebo chcete resetovat stav zabezpečení.

## <a name="manage-user-authentication-options"></a>Spravovat možnosti ověřování uživatelů

Pokud jste přiřadili roli *Správce ověřování* , můžete vyžadovat, aby uživatelé resetoval heslo, znovu zaregistrovali MFA nebo odvolali stávající relace MFA z jejich uživatelského objektu. Pokud chcete spravovat uživatelská nastavení, proveďte následující kroky:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Vlevo vyberte **Azure Active Directory** > **Uživatelé** > **Všichni uživatelé**.
1. Zvolte uživatele, u kterého chcete provést akci, a vyberte **metody ověřování**. V horní části okna vyberte jednu z následujících možností pro uživatele:
   - **Resetovat heslo** obnoví heslo uživatele a přiřadí dočasné heslo, které se musí při příštím přihlášení změnit.
   - **Vyžadovat opětovnou registraci MFA** znamená, že když se uživatel poprvé přihlásí, vyžádá si, aby nastavil novou metodu ověřování MFA.
   
      > [!NOTE]
      > Metody ověřování uživatele, které jsou aktuálně registrovány, se neodstraní, pokud správce vyžaduje znovu registraci pro MFA. Po opětovné registraci uživatele pro MFA doporučujeme, abyste provedli kontrolu svých bezpečnostních údajů a odstranili všechny dříve registrované metody ověřování, které již nejsou použitelné.
   
   - **Odvolání relací MFA** vymaže relace uživatele, které se pamatuje, a vyžaduje, aby při dalším požadavku na zařízení prováděli MFA.

   ![Správa metod ověřování z Azure Portal](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

## <a name="delete-users-existing-app-passwords"></a>Odstranit stávající hesla uživatelů aplikací

V případě potřeby můžete odstranit všechna hesla aplikací, která uživatel vytvořil. Neprohlížečové aplikace, které jsou přidružené k těmto heslům aplikace, přestanou fungovat, dokud se nevytvoří nové heslo aplikace. K provedení této akce jsou vyžadována oprávnění *globálního správce* .

Pokud chcete odstranit hesla aplikací uživatele, proveďte následující kroky:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Na levé straně vyberte **Azure Active Directory**  >  **Uživatelé**  >  **Všichni uživatelé**.
1. Vyberte **Multi-Factor Authentication**. Pokud chcete zobrazit tuto možnost nabídky, možná se budete muset posunout doprava. Kliknutím na ukázkový snímek obrazovky zobrazíte celé Azure Portal okno a umístění nabídky: [ ![ Vyberte Multi-Factor Authentication z okna uživatelé ve službě Azure AD.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Zaškrtněte políčko u uživatelů, které chcete spravovat. Zobrazí se seznam možností rychlého kroku na pravé straně.
1. Vyberte **Spravovat uživatelská nastavení**a potom zaškrtněte políčko **Odstranit všechna existující hesla aplikací vytvořená vybranými uživateli**, jak je znázorněno v následujícím příkladu: ![ Odstranit všechna existující hesla aplikací.](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. Vyberte **Uložit**a pak **Zavřít**.

## <a name="next-steps"></a>Další kroky

Tento článek ukazuje, jak nakonfigurovat jednotlivá uživatelská nastavení. Pokud chcete nakonfigurovat celkové nastavení služby Azure Multi-Factor Authentication, přečtěte si téma [Konfigurace nastavení azure Multi-Factor Authentication](howto-mfa-mfasettings.md).

Pokud uživatelé potřebují nápovědu, přečtěte si [příručku pro uživatele pro Azure Multi-Factor Authentication](../user-help/multi-factor-authentication-end-user-first-time.md).
