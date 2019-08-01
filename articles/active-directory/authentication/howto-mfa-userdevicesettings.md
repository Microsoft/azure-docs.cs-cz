---
title: Správci spravují uživatele a zařízení – Azure MFA – Azure Active Directory
description: Jak správci můžou měnit uživatelská nastavení, třeba vynucení opětovného provedení procesu ověření uživatele.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: d500f6c74bb62322a5e14ce3c489e8dba11dc2f2
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68555795"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Správa uživatelských nastavení pomocí Azure Multi-Factor Authentication v cloudu

Jako správce můžete spravovat následující nastavení uživatelů a zařízení:

* Vyžadovat od uživatelů opětovné poskytnutí metod kontaktu
* Odstranit hesla aplikací
* Vyžadovat MFA na všech důvěryhodných zařízeních

## <a name="manage-authentication-methods"></a>Správa metod ověřování

Jako správce, který má přiřazenou roli Správce ověřování, můžete vyžadovat, aby uživatelé resetoval heslo, znovu zaregistrovali MFA nebo odvolali stávající relace MFA z uživatelského objektu.

![Správa metod ověřování z Azure Portal](./media/howto-mfa-userdevicesettings/manage-authentication-methods.png)

1. Při resetování hesla dojde k resetování hesla uživatele a přiřazení dočasného hesla, které musí být při příštím přihlášení změněno.
2. Když se uživatel poprvé přihlásí, bude se vyžadovat, aby si ho znovu zaregistroval. v takovém případě bude požádán o nastavení nové metody ověřování MFA.
3. Odvolání relací MFA vymaže relace uživatele, které se pamatuje, a vyžaduje, aby při dalším požadavku na zařízení prováděli MFA.

## <a name="require-users-to-provide-contact-methods-again"></a>Vyžadovat od uživatelů opětovné poskytnutí metod kontaktu

Toto nastavení vynutí, aby uživatel znovu dokončil proces registrace. Neprohlížečové aplikace budou fungovat i v případě, že pro ně uživatel obsahuje hesla aplikací.  Hesla aplikací uživatelů můžete odstranit také tak, že vyberete možnost **Odstranit všechna existující hesla aplikací vygenerovaná vybranými uživateli**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Jak vyžadovat, aby uživatelé mohli znovu zadat metody kontaktu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **Uživatelé** > **Všichni uživatelé**.
3. Na pravé straně vyberte **Multi-Factor Authentication** na panelu nástrojů. Otevře se stránka Multi-Factor Authentication.
4. Zaškrtněte políčko u uživatelů, které chcete spravovat. Zobrazí se seznam možností rychlého kroku na pravé straně.
5. Vyberte **Spravovat uživatelská nastavení**.
6. Zaškrtněte políčko, pokud chcete, aby vybraní **uživatelé znovu poskytovali metody kontaktu**.
   ![Vyžadovat od uživatelů opětovné poskytnutí metod kontaktu](./media/howto-mfa-userdevicesettings/reproofup.png)
7. Klikněte na **Uložit**.
8. Klikněte na **Zavřít**.

Organizace mohou tento postup provést pomocí prostředí PowerShell následujícím způsobem jako vodítko pro vymazání `StrongAuthenticationMethods` atributu:

```PowerShell
$Upn = "theuser@domain.com"
$noMfaConfig = @()
Set-MsolUser -UserPrincipalName $Upn -StrongAuthenticationMethods $noMfaConfig
```

## <a name="delete-users-existing-app-passwords"></a>Odstranit stávající hesla uživatelů aplikací

Toto nastavení odstraní všechna hesla aplikací, která uživatel vytvořil. Neprohlížečové aplikace, které jsou přidružené k těmto heslům aplikace, přestanou fungovat, dokud se nevytvoří nové heslo aplikace.

### <a name="how-to-delete-users-existing-app-passwords"></a>Jak odstranit existující hesla aplikací pro uživatele

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **Uživatelé** > **Všichni uživatelé**.
3. Na pravé straně vyberte **Multi-Factor Authentication** na panelu nástrojů. Otevře se stránka Multi-Factor Authentication.
4. Zaškrtněte políčko u uživatelů, které chcete spravovat. Zobrazí se seznam možností rychlého kroku na pravé straně.
5. Vyberte **Spravovat uživatelská nastavení**.
6. Zaškrtněte políčko pro **odstranění všech existujících hesel aplikací generovaných vybranými uživateli**.
   ![Odstranit všechna existující hesla aplikací](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. Klikněte na **Uložit**.
8. Klikněte na **Zavřít**.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Obnovení MFA na všech zapamatovaných zařízeních pro uživatele

Jednou z konfigurovatelných funkcí služby Azure Multi-Factor Authentication je poskytnout uživatelům možnost označit zařízení jako důvěryhodnou. Další informace najdete v tématu [Konfigurace nastavení služby Azure Multi-Factor Authentication](howto-mfa-mfasettings.md#remember-multi-factor-authentication).

Uživatelé se můžou na jejich běžných zařízeních odhlásit pomocí dvoustupňového ověřování na konfigurovatelný počet dnů. Pokud dojde k ohrožení bezpečnosti účtu nebo dojde ke ztrátě důvěryhodného zařízení, je nutné, abyste byli schopni odebrat důvěryhodný stav a znovu vyžadovat dvoustupňové ověřování.

Při zaškrtnutí políčka **obnovit vícefaktorové ověřování u všech zapamatovaných zařízení** , které uživatelé potřebují k provedení dvoustupňového ověřování při příštím přihlášení, a to i v případě, že jsou zařízení označená jako důvěryhodná.

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Postup obnovení MFA na všech pozastavených zařízeních pro uživatele

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **Uživatelé** > **Všichni uživatelé**.
3. Na pravé straně vyberte **Multi-Factor Authentication** na panelu nástrojů. Otevře se stránka Multi-Factor Authentication.
4. Zaškrtněte políčko u uživatelů, které chcete spravovat. Zobrazí se seznam možností rychlého kroku na pravé straně.
5. Vyberte **Spravovat uživatelská nastavení**.
6. Zaškrtněte políčko pro **obnovení Multi-Factor Authentication u všech zapamatovaných zařízení**
   ![obnovit vícefaktorové ověřování na všech zapamatovaných zařízeních.](./media/howto-mfa-userdevicesettings/rememberdevices.png)
7. Klikněte na **Uložit**.
8. Klikněte na **Zavřít**.

## <a name="next-steps"></a>Další postup

- Získat další informace o tom, jak [nakonfigurovat nastavení Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)
- Pokud uživatelé potřebují nápovědu, najeďte je k [uživatelské příručce pro dvoustupňové ověřování](../user-help/multi-factor-authentication-end-user.md) .
