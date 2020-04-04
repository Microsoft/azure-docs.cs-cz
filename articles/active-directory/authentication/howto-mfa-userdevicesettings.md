---
title: Správa uživatelů a zařízení Azure MFA – Azure Active Directory
description: Jak mohou správci změnit nastavení uživatele, například nutit uživatele, aby znovu prodělali proces ověření.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d94fa761980151c420984eb7e8c3254a3509ef
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653493"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Správa uživatelského nastavení pomocí služby Azure Multi-Factor Authentication v cloudu

Jako správce můžete spravovat následující nastavení uživatelů a zařízení:

* Vyžadovat, aby uživatelé znovu zadali metody kontaktu
* Odstranění hesel aplikací
* Vyžadovat vícefaktorové základě na všech důvěryhodných zařízeních

## <a name="manage-authentication-methods"></a>Správa metod ověřování

Jako správce přiřadil roli správce ověřování, můžete od uživatelů požadovat, aby resetovali své heslo, znovu se zaregistrovali pro vícefaktorové ověřování nebo odvolali existující relace Vícefaktorové ověřování z objektu uživatele.

![Správa metod ověřování z webu Azure Portal](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Vlevo vyberte **možnost Uživatelé** > **služby** > Azure Active Directory**Všichni uživatelé**.
1. Vyberte uživatele, u kterýchcete provést akci, a vyberte **možnost Metody ověřování**.
   - **Resetovat heslo** obnoví heslo uživatele a přiřadí dočasné heslo, které musí být změněno při příštím přihlášení.
   - **Vyžadovat znovu zaregistrovat Vícefaktorové** ověřování bude dělat to tak, že když se uživatel přihlásí příště, budou požádáni o nastavení nové metody ověřování Vícefaktorové ověřování.
   - **Odvolat relace vícefaktorové ověřování** vymaže zapamatované relace Vícefaktorové ověřování uživatele a vyžaduje, aby provedly vícefaktorové ověřování při příštím vyžadování zásad na zařízení.

## <a name="delete-users-existing-app-passwords"></a>Odstranění existujících hesel aplikací uživatelů

Toto nastavení odstraní všechna hesla aplikací, která uživatel vytvořil. Aplikace, které nejsou v prohlížeči a které byly přidruženy k těmto heslům aplikací, přestanou fungovat, dokud se nevytvoří nové heslo aplikace. K provedení této akce jsou vyžadována oprávnění globálního správce.

### <a name="how-to-delete-users-existing-app-passwords"></a>Jak odstranit existující hesla aplikací uživatelů

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vlevo vyberte **možnost Uživatelé** > **služby** > Azure Active Directory**Všichni uživatelé**.
3. Na pravé straně vyberte na panelu nástrojů **vícefaktorové ověřování.** Otevře se stránka vícefaktorového ověřování.
4. Zaškrtněte políčko vedle uživatele nebo uživatelů, které chcete spravovat. Vpravo se zobrazí seznam možností rychlého kroku.
5. Vyberte **Spravovat uživatelská nastavení**.
6. Zaškrtněte políčko **Odstranit všechna existující hesla aplikací generovaná vybranými uživateli**.
   ![Odstranění všech existujících hesel aplikací](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Klepněte na **tlačítko Uložit**.
8. Klepněte na **tlačítko Zavřít**.

## <a name="next-steps"></a>Další kroky

- Další informace o [konfiguraci nastavení azure multifaktorového ověřování](howto-mfa-mfasettings.md)
- Pokud uživatelé potřebují pomoc, namiřte je na [uživatelskou příručku pro dvoustupňové ověření](../user-help/multi-factor-authentication-end-user.md)
