---
title: Stavů uživatele Microsoft Azure Multi-Factor Authentication
description: Další informace o stavu uživatele v Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39659df99951850ced07be14f81348ae9c1c1be5
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56428598"
---
# <a name="how-to-require-two-step-verification-for-a-user"></a>Jak vyžadovat dvoustupňové ověřování pro uživatele

Můžete využít jeden ze dvou následujících metod pro vyžadováním dvoustupňového ověřování, které vyžadují pomocí účtu globálního správce. První možností je povolit jednotlivé uživatele pro Azure Multi-Factor Authentication (MFA). Pokud uživatelé jsou povolené jednotlivě, jejich provedení dvoustupňového ověřování pokaždé, když se přihlásí (s několika výjimkami, jako je například, když se přihlásí z důvěryhodných IP adres nebo když _zapamatovaných zařízeních_ funkce je zapnutá). Druhou možností je nastavit zásady podmíněného přístupu, která vyžaduje dvoustupňové ověření za určitých podmínek.

> [!TIP]
> Vyberte jednu z těchto metod a vyžadovat dvoustupňové ověřování, ne obojí. Povolení uživatele pro ověřování Azure Multi-Factor Authentication potlačí všechny zásady podmíněného přístupu.

## <a name="choose-how-to-enable"></a>Zvolte, jak povolit

**Povolené tak, že změníte stav uživatele** – Toto je tradiční metody pro vyžadováním dvoustupňového ověřování a je popsána v tomto článku. Funguje to i Azure MFA v cloudu a Azure MFA serveru. Pomocí této metody vyžaduje, aby uživatelé dvoustupňové ověřování **pokaždé, když** přihlásit a přepisuje zásady podmíněného přístupu. Toto je metoda pro ty, které mají licence Office 365 nebo Microsoft 365 Business používat, protože neobsahují funkce podmíněného přístupu.

Povolené zásady podmíněného přístupu – Toto je nejflexibilnější způsob, jak zapnout dvoustupňové ověřování pro vaše uživatele. Povolení používat pouze zásady podmíněného přístupu se dá použít pro Azure MFA v cloudu a je Prémiová funkce služby Azure AD. Další informace o této metody můžete najít v [nasazení cloudového ověřování Azure Multi-Factor Authentication](howto-mfa-getstarted.md).

Povolená služba Azure AD Identity Protection - Tato metoda používá zásady rizik služby Azure AD Identity Protection a vyžadovat dvoustupňové ověřování jen podle rizika přihlašování pro všechny cloudové aplikace. Tato metoda vyžaduje licencování Azure Active Directory P2. Další informace o této metody můžete najít v [Azure Active Directory Identity Protection](../identity-protection/howto-sign-in-risk-policy.md)

> [!Note]
> Další informace o licencí a cenách najdete na [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) a [ověřování službou Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) stránkách s cenami.

## <a name="enable-azure-mfa-by-changing-user-state"></a>Povolit Azure MFA změnou stavu uživatele

Uživatelské účty v Azure Multi-Factor Authentication mají následující tři různé stavy:

| Status | Popis | Neprohlížečové aplikace, které jsou ovlivněny | Vliv prohlížečových aplikací | Vliv na moderní ověřování |
|:---:|:---:|:---:|:--:|:--:|
| Zakázáno |Výchozí stav nového uživatele není zaregistrované v Azure MFA. |Ne |Ne |Ne |
| Povoleno |Uživatel byl zaregistrován v Azure MFA, ale nebyla zaregistrována. Zobrazí se výzva k registraci při příštím přihlášení. |Ne.  Nadále fungovat až do dokončení procesu registrace. | Ano. Po vypršení platnosti relace, je požadována registrace Azure MFA.| Ano. Po vypršení platnosti přístupového tokenu, je požadována registrace Azure MFA. |
| Vynuceno |Uživatel byl zaregistrován a dokončil proces registrace pro Azure MFA. |Ano. Aplikace potřebujete hesla aplikace. |Ano. Azure MFA je povinný při přihlášení. | Ano. Azure MFA je povinný při přihlášení. |

Stav uživatele odráží, jestli správce zaregistroval je v Azure MFA a určuje, zda jsou dokončit proces registrace.

Všichni uživatelé začínají *zakázané*. Při registraci uživatele v Azure MFA, jejich stav se změní na *povoleno*. Při povolení uživatelé přihlásit a dokončit proces registrace, jejich stav se změní na *vynucené*.  

### <a name="view-the-status-for-a-user"></a>Zobrazení stavu pro uživatele

Přístup ke stránce, kde můžete zobrazit a spravovat stavů uživatele pomocí následujících kroků:

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Přejděte na **Azure Active Directory** > **uživatelů a skupin** > **všichni uživatelé**.
3. Vyberte **ověřování službou Multi-Factor Authentication**.
   ![Vyberte metodu ověřování službou Multi-Factor Authentication](./media/howto-mfa-userstates/selectmfa.png)
4. Otevře se nová stránka, která zobrazuje stavy uživatele.
   ![Stav služby Multi-Factor authentication uživatele – snímek obrazovky](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Změňte stav uživatele

1. Použít k ověřování Azure Multi-Factor Authentication v předchozích krocích **uživatelé** stránky.
2. Najdete uživatele, kterého chcete povolit pro Azure MFA. Můžete potřebovat změnit zobrazení v horní části.
   ![Najít uživatele – snímek obrazovky](./media/howto-mfa-userstates/enable1.png)
3. Zaškrtněte políčko vedle svého názvu.
4. Na pravé straně v části **rychlých krocích**, zvolte **povolit** nebo **zakázat**.
   ![Povolit vybraného uživatele – snímek obrazovky](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Povolené* uživatelé jsou automaticky přepnout do *vynucené* při registraci pro Azure MFA. To není ručně změnit stav uživatele *vynucené*.

5. Potvrďte výběr v místním okně, které se otevře.

Po povolení uživatelů je upozorněte e-mailem. Sdělte jim, že budete vyzváni k registraci při příštím přihlášení. Navíc pokud vaše organizace používá neprohlížečové aplikace, které nepodporují moderní ověřování, potřebují vytvořit hesla aplikací. Můžete také zahrnout odkaz [Průvodce pro koncové uživatele Azure MFA](../user-help/multi-factor-authentication-end-user.md) mohli začít pracovat.

### <a name="use-powershell"></a>Použití prostředí PowerShell

Chcete-li změnit stav uživatele pomocí [Azure AD PowerShell](/powershell/azure/overview), změňte `$st.State`. Existují tři možné stavy:

* Povoleno
* Vynuceno
* Zakázáno  

Nepřesouvejte přímo na uživatele *vynucené* stavu. Pokud tak učiníte, aplikace nezaložené prohlížeči přestanou fungovat, protože nebyla uživatel neabsolvoval registraci Azure MFA a nezískal [heslo aplikace](howto-mfa-mfasettings.md#app-passwords).

Nejprve nainstalujte modul pomocí:

       Install-Module MSOnline
       
> [!TIP]
> Nezapomeňte a připojte se nejprve pomocí **Connect-MsolService**


 V tomto příkladu skript prostředí PowerShell umožňuje vícefaktorové ověřování pro konkrétního uživatele:

        Import-Module MSOnline
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = "Enabled"
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Použití prostředí PowerShell je dobrou volbou, když budete potřebovat k hromadné povolení uživatelů. Následující skript jako příklad, prochází seznam uživatelů a umožňuje vícefaktorové ověřování u svých účtů:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = "Enabled"
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }
    
Pokud chcete zakázat MFA, použijte tento skript:

    Get-MsolUser -UserPrincipalName user@domain.com | Set-MsolUser -StrongAuthenticationRequirements @()
    
která může také zkrátila na:

    Set-MsolUser -UserPrincipalName user@domain.com -StrongAuthenticationRequirements @()

## <a name="next-steps"></a>Další postup

Proč byla uživateli zobrazí výzva nebo nechcete zobrazit výzvu k provedení vícefaktorové ověřování? V části [sestavy přihlášení Azure AD v sestavách v dokumentu ověřování Azure Multi-Factor Authentication](howto-mfa-reporting.md#azure-ad-sign-ins-report).

Chcete-li nakonfigurovat další nastavení, jako jsou důvěryhodné IP adresy, vlastní hlasové zprávy a upozornění na podvod, najdete v článku [nastavení konfigurace Azure Multi-Factor Authentication](howto-mfa-mfasettings.md)

Informace o správě nastavení pro ověřování Azure Multi-Factor Authentication najdete v článku [spravovat uživatelská nastavení pomocí ověřování Azure Multi-Factor Authentication v cloudu](howto-mfa-userdevicesettings.md)
