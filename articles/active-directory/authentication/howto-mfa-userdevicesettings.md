---
title: Správa metod ověřování pro Azure Multi-Factor Authentication-Azure Active Directory
description: Přečtěte si, jak můžete nakonfigurovat Azure Active Directory nastavení uživatele pro Azure Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6309ef6793858051ceaf3c3b33edb9f830b26710
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93378039"
---
# <a name="manage-user-authentication-methods-for-azure-multi-factor-authentication"></a>Správa metod ověřování uživatelů pro Azure Multi-Factor Authentication

Uživatelé v Azure AD mají dvě různé sady kontaktních údajů:  

- Kontaktní informace pro veřejný profil, které jsou spravovány v profilu uživatele a viditelné pro členy vaší organizace. U uživatelů synchronizovaných z místní služby Active Directory se tyto informace spravují v místních Active Directory Domain Services Windows serveru.
- Metody ověřování, které jsou vždycky zachované jako soukromé a používají se jenom pro ověřování, včetně služby Multi-Factor Authentication (MFA). Správci mohou spravovat tyto metody v okně metoda ověřování uživatele a uživatelé mohou spravovat své metody na stránce s informacemi o zabezpečení MyAccount.

Při správě metod Azure Multi-Factor Authentication pro uživatele můžou správci ověřování provádět tyto akce: 

1. Přidejte metody ověřování pro konkrétního uživatele, včetně telefonních čísel používaných pro MFA.
1. Resetování hesla uživatele
1. Vyžaduje, aby uživatel znovu zaregistroval vícefaktorové ověřování.
1. Odvolat existující relace MFA
1. Odstranit existující hesla aplikací uživatele  

## <a name="add-authentication-methods-for-a-user"></a>Přidání metod ověřování pro uživatele 

Metody ověřování pro uživatele můžete přidat prostřednictvím Azure Portal nebo Microsoft Graph.  

> [!NOTE]
> Z bezpečnostních důvodů by se neměla používat pole kontaktní informace veřejnosti pro uživatele k provádění vícefaktorového ověřování. Místo toho by uživatelé měli vyplnit čísla metod ověřování, která se mají použít pro VÍCEFAKTOROVÉ ověřování.  

:::image type="content" source="media/howto-mfa-userdevicesettings/add-authentication-method-detail.png" alt-text="Přidat metody ověřování z Azure Portal":::

Postup přidání metod ověřování pro uživatele prostřednictvím Azure Portal:  

1. Přihlaste se k **portálu Azure Portal**. 
1. Přejděte na **Azure Active Directory**  >  **Uživatelé**  >  **Všichni uživatelé**. 
1. Zvolte uživatele, pro kterého chcete přidat metodu ověřování, a vyberte **metody ověřování**.  
1. V horní části okna vyberte **+ Přidat metodu ověřování**.
   1. Vyberte metodu (telefonní číslo nebo e-mail). E-mail se dá použít pro resetování hesla, ale ne pro ověřování. Když přidáváte telefonní číslo, vyberte typ telefonu a zadejte telefonní číslo s platným formátem (např. + 1 4255551234).
   1. Vyberte **Add** (Přidat).

> [!NOTE]
> Prostředí Preview umožňuje správcům přidat všechny dostupné metody ověřování pro uživatele, zatímco původní prostředí umožňuje aktualizovat jenom telefon a alternativní telefonní metody.

### <a name="manage-methods-using-powershell"></a>Správa metod pomocí prostředí PowerShell:  

Nainstalujte modul Microsoft. Graph. identity. nenašla PowerShell pomocí následujících příkazů. 

```powershell
Install-module Microsoft.Graph.Identity.Signins
Connect-MgGraph -Scopes UserAuthenticationMethod.ReadWrite.All
Select-MgProfile -Name beta
```

Vypíše metody ověřování na základě telefonu pro konkrétního uživatele.

```powershell
Get-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com
```

Vytvořte metodu ověřování mobilního telefonu pro konkrétního uživatele.

```powershell
New-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -phoneType “mobile” -phoneNumber "+1 7748933135"
```

Odebrat konkrétní telefonní metodu pro uživatele

```powershell
Remove-MgUserAuthenticationPhoneMethod -UserId balas@contoso.com -PhoneAuthenticationMethodId 3179e48a-750b-4051-897c-87b9720928f7
```

Metody ověřování se dají spravovat taky pomocí Microsoft Graph rozhraní API. Další informace najdete v článku [Přehled rozhraní API metody ověřování Azure AD](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true) .

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
   
    :::image type="content" source="media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png" alt-text="Správa metod ověřování z Azure Portal":::

## <a name="delete-users-existing-app-passwords"></a>Odstranit existující hesla aplikací pro uživatele

Pro uživatele, kteří mají definovaná hesla aplikací, můžou správci taky tato hesla odstranit, což způsobí, že se starší verze ověřování v těchto aplikacích nezdařila. Tyto akce můžou být nezbytné v případě, že potřebujete poskytnout pomoc uživateli nebo musíte resetovat své metody ověřování. Neprohlížečové aplikace, které jsou přidružené k těmto heslům aplikací, přestanou fungovat, dokud se nevytvoří nové heslo aplikace. 

Pokud chcete odstranit hesla aplikací uživatele, proveďte následující kroky:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Na levé straně vyberte **Azure Active Directory**  >  **Uživatelé**  >  **Všichni uživatelé**.
1. Vyberte **Multi-Factor Authentication**. Pokud chcete zobrazit tuto možnost nabídky, možná se budete muset posunout doprava. Kliknutím na ukázkový snímek obrazovky zobrazíte celé Azure Portal okno a umístění nabídky: [ ![ Vyberte Multi-Factor Authentication z okna uživatelé ve službě Azure AD.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Zaškrtněte políčko u uživatelů, které chcete spravovat. Zobrazí se seznam možností rychlého kroku na pravé straně.
1. Vyberte **Spravovat uživatelská nastavení** a potom zaškrtněte políčko **Odstranit všechna existující hesla aplikací vytvořená vybranými uživateli** , jak je znázorněno v následujícím příkladu: ![ Odstranit všechna existující hesla aplikací.](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. Vyberte **Uložit** a pak **Zavřít**.

## <a name="next-steps"></a>Další kroky

Tento článek ukazuje, jak nakonfigurovat jednotlivá uživatelská nastavení. Pokud chcete nakonfigurovat celkové nastavení služby Azure Multi-Factor Authentication, přečtěte si téma [Konfigurace nastavení azure Multi-Factor Authentication](howto-mfa-mfasettings.md).

Pokud uživatelé potřebují nápovědu, přečtěte si [příručku pro uživatele pro Azure Multi-Factor Authentication](../user-help/multi-factor-authentication-end-user-first-time.md).
