---
title: Správci spravují uživatele a zařízení – Azure MFA – Azure Active Directory
description: Jak správci můžou měnit uživatelská nastavení, třeba vynucení opětovného provedení procesu ověření uživatele.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 808faaed76ff63d69feb8170eaac72021c7bd49d
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73042109"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Správa uživatelských nastavení pomocí Azure Multi-Factor Authentication v cloudu

Jako správce můžete spravovat následující nastavení uživatelů a zařízení:

* Vyžadovat od uživatelů opětovné poskytnutí metod kontaktu
* Odstranit hesla aplikací
* Vyžadovat MFA na všech důvěryhodných zařízeních

## <a name="manage-authentication-methods"></a>Správa metod ověřování

Jako správce, který má přiřazenou roli Správce ověřování, můžete vyžadovat, aby uživatelé resetoval heslo, znovu zaregistrovali MFA nebo odvolali stávající relace MFA z uživatelského objektu.

![Správa metod ověřování z Azure Portal](./media/howto-mfa-userdevicesettings/manage-authentication-methods.png)

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. Na levé straně vyberte **Azure Active Directory**  > **Uživatelé**  > **všech uživatelích**.
1. Zvolte uživatele, u kterého chcete provést akci, a vyberte **metody ověřování**.
   - Při **resetování hesla** dojde k resetování hesla uživatele a přiřazení dočasného hesla, které musí být při příštím přihlášení změněno.
   - Když se uživatel poprvé přihlásí, bude se vyžadovat, aby si ho **znovu zaregistroval** . v takovém případě bude požádán o nastavení nové metody ověřování MFA.
   - **Odvolání relací MFA** vymaže relace uživatele, které se pamatuje, a vyžaduje, aby při dalším požadavku na zařízení prováděli MFA.

## <a name="delete-users-existing-app-passwords"></a>Odstranit stávající hesla uživatelů aplikací

Toto nastavení odstraní všechna hesla aplikací, která uživatel vytvořil. Neprohlížečové aplikace, které jsou přidružené k těmto heslům aplikace, přestanou fungovat, dokud se nevytvoří nové heslo aplikace. K provedení této akce jsou vyžadována oprávnění globálního správce.

### <a name="how-to-delete-users-existing-app-passwords"></a>Jak odstranit existující hesla aplikací pro uživatele

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory**  > **Uživatelé**  > **všech uživatelích**.
3. Na pravé straně vyberte **Multi-Factor Authentication** na panelu nástrojů. Otevře se stránka Multi-Factor Authentication.
4. Zaškrtněte políčko u uživatelů, které chcete spravovat. Zobrazí se seznam možností rychlého kroku na pravé straně.
5. Vyberte **Spravovat uživatelská nastavení**.
6. Zaškrtněte políčko pro **odstranění všech existujících hesel aplikací generovaných vybranými uživateli**.
   ![odstranit všechna existující hesla aplikací](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Klikněte na **Uložit**.
8. Klikněte na **Zavřít**.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [konfiguraci nastavení Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)
- Pokud uživatelé potřebují nápovědu, najeďte je k [uživatelské příručce pro dvoustupňové ověřování](../user-help/multi-factor-authentication-end-user.md) .
