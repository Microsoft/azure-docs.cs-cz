---
title: Stavů uživatele Microsoft Azure Multi-Factor Authentication
description: Další informace o stavu uživatele v Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/26/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: richagi
ms.openlocfilehash: 6945966d4a701ea6e2684b7da766c8b6c9f9a283
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39049044"
---
# <a name="how-to-require-two-step-verification-for-a-user-or-group"></a>Jak vyžadovat dvoustupňové ověřování pro uživatele nebo skupiny

Můžete využít jeden ze dvou následujících metod pro vyžadování dvoustupňového ověření. První možností je povolit jednotlivé uživatele pro Azure Multi-Factor Authentication (MFA). Pokud uživatelé jsou povolené jednotlivě, jejich provedení dvoustupňového ověřování pokaždé, když se přihlásí (s několika výjimkami, jako je například, když se přihlásí z důvěryhodných IP adres nebo když _zapamatovaných zařízeních_ funkce je zapnutá). Druhou možností je nastavit zásady podmíněného přístupu, která vyžaduje dvoustupňové ověření za určitých podmínek.

>[!TIP] 
>Vyberte jednu z těchto metod a vyžadovat dvoustupňové ověřování, ne obojí. Povolení uživatele pro ověřování Azure Multi-Factor Authentication potlačí všechny zásady podmíněného přístupu.

## <a name="which-option-is-right-for-you"></a>Která možnost je pro vás nejvhodnější?

**Povolení ověřování Azure Multi-Factor Authentication tak, že změníte stavů uživatele** je tradiční přístup pro vyžadování dvoustupňového ověření. Funguje to pro oba Azure MFA v cloudu a Azure MFA serveru. Všichni uživatelé, kteří povolíte provedení dvoustupňového ověřování při každém přihlášení. Povolení uživatele potlačí všechny zásady podmíněného přístupu, které může mít vliv na tohoto uživatele. 

**Povolení ověřování Azure Multi-Factor Authentication pomocí zásad podmíněného přístupu** je flexibilnější postup pro vyžadování dvoustupňového ověření. Pracuje pouze pro Azure MFA v cloudu, ale a _podmíněného přístupu_ je [placené funkce služby Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features). Můžete vytvořit zásady podmíněného přístupu, které platí pro skupiny, stejně jako jednotlivé uživatele. S vysokým rizikem skupiny je možné přidělit další omezení než skupiny s nízkým rizikem, nebo dvoustupňové ověřování je možné vyžadováno pouze u vysoce rizikové cloudové aplikace a pro ty s nízkým rizikem přeskočeno. 

Obě možnosti vyzvat uživatele k registraci pro ověřování Azure Multi-Factor Authentication při prvním přihlášení po zapnutí požadavky. Obě možnosti jsou také pracovat dají konfigurovat [nastavení ověřování Azure Multi-Factor Authentication](howto-mfa-mfasettings.md).

## <a name="enable-azure-mfa-by-changing-user-status"></a>Povolit Azure MFA tak, že změníte stav uživatele.

Uživatelské účty v Azure Multi-Factor Authentication mají následující tři různé stavy:

| Status | Popis | Neprohlížečové aplikace, které jsou ovlivněny | Vliv prohlížečových aplikací | Vliv na moderní ověřování |
|:---:|:---:|:---:|:--:|:--:|
| Zakázáno |Výchozí stav nového uživatele není zaregistrované v Azure MFA. |Ne |Ne |Ne |
| Povoleno |Uživatel byl zaregistrován v Azure MFA, ale nebyla zaregistrována. Zobrazí se výzva k registraci při příštím přihlášení. |Ne.  Nadále fungovat až do dokončení procesu registrace. | Ano. Po vypršení platnosti relace, je požadována registrace Azure MFA.| Ano. Po vypršení platnosti přístupového tokenu, je požadována registrace Azure MFA. |
| Vynuceno |Uživatel byl zaregistrován a dokončil proces registrace pro Azure MFA. |Ano.  Aplikace potřebujete hesla aplikace. |Ano. Azure MFA je povinný při přihlášení. | Ano. Azure MFA je povinný při přihlášení. |

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

   >[!TIP]
   >*Povolené* uživatelé jsou automaticky přepnout do *vynucené* při registraci pro Azure MFA. To není ručně změnit stav uživatele *vynucené*. 

5. Potvrďte výběr v místním okně, které se otevře. 

Po povolení uživatelů je upozorněte e-mailem. Sdělte jim, že budete vyzváni k registraci při příštím přihlášení. Navíc pokud vaše organizace používá neprohlížečové aplikace, které nepodporují moderní ověřování, potřebují vytvořit hesla aplikací. Můžete také zahrnout odkaz [Průvodce pro koncové uživatele Azure MFA](../user-help/multi-factor-authentication-end-user.md) mohli začít pracovat.

### <a name="use-powershell"></a>Použití prostředí PowerShell
Chcete-li změnit stav uživatele pomocí [Azure AD PowerShell](/powershell/azure/overview), změňte `$st.State`. Existují tři možné stavy:

* Povoleno
* Vynuceno
* Zakázáno  

Nepřesouvejte přímo na uživatele *vynucené* stavu. Pokud tak učiníte, aplikace nezaložené prohlížeči přestanou fungovat, protože nebyla uživatel neabsolvoval registraci Azure MFA a nezískal [heslo aplikace](howto-mfa-mfasettings.md#app-passwords). 

Pomocí prostředí PowerShell je dobrou volbou, když budete chtít hromadné povolení uživatelů. Vytvořte Powershellový skript, který prochází seznam uživatelů a umožňuje jim:

        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

Následující skript představuje příklad:

    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
        $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
        $st.RelyingParty = "*"
        $st.State = “Enabled”
        $sta = @($st)
        Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }

## <a name="enable-azure-mfa-with-a-conditional-access-policy"></a>Povolit Azure MFA pomocí zásad podmíněného přístupu

_Podmíněný přístup_ je placené funkce služby Azure Active Directory s mnoha možnostmi konfigurace. Tyto kroky se provedou v rámci jeden způsob, jak vytvořit zásady. Další informace, přečtěte si informace o [podmíněného přístupu v Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Přejděte na **Azure Active Directory** > **podmíněného přístupu**.
3. Vyberte **nové zásady**.
4. V části **přiřazení**vyberte **uživatelů a skupin**. Použití **zahrnout** a **vyloučit** karty k určení, které uživatelé a skupiny zásad spravuje.
5. V části **přiřazení**vyberte **cloudové aplikace**. Vybrat možnost zahrnutí **všechny cloudové aplikace**.
6. V části **ovládací prvky přístupu**vyberte **udělení**. Zvolte **vyžadovat vícefaktorové ověřování**.
7. Zapnout **povolit zásady** k **na**a pak vyberte **Uložit**.

Další možnosti v zásadách podmíněného přístupu vám tak umožnili zadat přesně, pokud je nutné použít dvoustupňové ověřování. Například můžete provést zásad, jako jsou například tato: Při pokusu o přístup k naší platformě pro zajištění aplikace z nedůvěryhodným sítím na zařízení, která nejsou připojená k doméně dodavatelé, vyžadovat dvoustupňové ověřování. 

## <a name="next-steps"></a>Další postup

- Získejte tipy [osvědčené postupy pro podmíněný přístup](../active-directory-conditional-access-best-practices.md).

- Umožňuje spravovat nastavení ověřování Azure Multi-Factor Authentication [vaši uživatelé a jejich zařízení](howto-mfa-userdevicesettings.md).
