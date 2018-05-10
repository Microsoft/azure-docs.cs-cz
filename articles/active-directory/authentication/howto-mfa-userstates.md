---
title: Microsoft Azure Multi-Factor Authentication uživatele stavy
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
ms.openlocfilehash: b809097e50a17178da12fdb424eba08dc8e0c4cb
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="how-to-require-two-step-verification-for-a-user-or-group"></a>Jak vyžadovat dvoustupňové ověřování pro uživatele nebo skupinu

Můžete provést jednu ze dvou přístupů pro vyžádání dvoustupňové ověřování. První možností je umožnit každému uživateli pro Azure Multi-Factor Authentication (MFA). Pokud uživatelé jsou povolené jednotlivě, fungují dvoustupňové ověřování při každém přihlášení (na několik výjimek, například při přihlášení z důvěryhodných adres IP adresy nebo když _zapamatovaných zařízení_ je zapnuta funkce). Druhou možností je nastavit zásady podmíněného přístupu, která vyžaduje dvoustupňové ověřování za určitých podmínek.

>[!TIP] 
>Vyberte jednu z těchto metod tak, aby vyžadovala dvoustupňové ověření, nikoli oba dva. Povolení uživatele pro ověřování Azure Multi-Factor Authentication potlačí všechny zásady podmíněného přístupu.

## <a name="which-option-is-right-for-you"></a>Možnosti, kterou je pro vás nejvhodnější?

**Povolení ověřování Azure Multi-Factor Authentication změnou stavů uživatele** je tradiční přístup pro vyžádání dvoustupňové ověřování. Funguje pro oba Azure MFA v cloudu a Azure MFA serveru. Všichni uživatelé, které vám umožňují provádět dvoustupňové ověřování při každém přihlášení. Povolení uživatele potlačí všechny zásady podmíněného přístupu, které může mít vliv na tohoto uživatele. 

**Povolení ověřování Azure Multi-Factor Authentication se zásadami podmíněného přístupu** je flexibilnější přístup pro vyžádání dvoustupňové ověřování. Pracuje pouze pro Azure MFA v cloudu, ale a _podmíněného přístupu_ je [placené funkce služby Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features). Můžete vytvořit zásady podmíněného přístupu, které platí pro skupiny, jakož i jednotlivé uživatele. S vysokým rizikem skupiny je možné přidělit další omezení než nízkým rizikem skupiny nebo dvoustupňové ověřování můžete vyžaduje se jenom pro vysoce rizikové cloudové aplikace a pro ty, které jsou nízkým rizikem přeskočeno. 

Obě možnosti vyzvat uživatele k registraci pro Azure Multi-Factor Authentication při prvním přihlášení po zapnutí požadavky. Obě možnosti také pracovat konfigurovat [nastavení ověřování Azure Multi-Factor Authentication](howto-mfa-mfasettings.md).

## <a name="enable-azure-mfa-by-changing-user-status"></a>Povolit Azure MFA změnou stavu uživatele

Uživatelské účty v Azure Multi-Factor Authentication mají následující tři jedinečné stavy:

| Status | Popis | Neprohlížečové aplikace vliv | Aplikace prohlížeče vliv | Moderní ověřování vliv |
|:---:|:---:|:---:|:--:|:--:|
| Zakázáno |Výchozího stavu pro nového uživatele nejsou registrovaná v Azure MFA. |Ne |Ne |Ne |
| Povoleno |Uživatel byl zaregistrován ke službě Azure MFA, ale není registrován. Obdrží výzvu k registraci při příštím přihlášení. |Ne.  Budou nadále fungovat až do dokončení procesu registrace. | Ano. Po vypršení platnosti relace, je požadována registrace Azure MFA.| Ano. Po vypršení platnosti přístupového tokenu, je požadována registrace Azure MFA. |
| Vynuceno |Uživatel byl zaregistrován a dokončil proces registrace pro Azure MFA. |Ano.  Aplikace potřebujete hesla aplikace. |Ano. Azure MFA se při přihlášení vyžaduje. | Ano. Azure MFA se při přihlášení vyžaduje. |

Stav uživatele, odráží jestli správce zapsal je v Azure MFA, a zda jejich dokončit proces registrace.

Všichni uživatelé spustí *zakázané*. Při registraci uživatele v Azure MFA, jeho stav se změní na *povoleno*. Při povolení uživatelé přihlásit a dokončit proces registrace, jejich stav se změní na *vynucené*.  

### <a name="view-the-status-for-a-user"></a>Zobrazení stavu pro uživatele

Pomocí následujících kroků pro přístup ke stránce, kde můžete zobrazit a spravovat stavů uživatele:

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Přejděte na **Azure Active Directory** > **uživatelů a skupin** > **všichni uživatelé**.
3. Vyberte **služby Multi-Factor Authentication**.
   ![Vyberte služby Multi-Factor Authentication](./media/howto-mfa-userstates/selectmfa.png)
4. Otevře se nová stránka, která zobrazuje stavů uživatele.
   ![Stav uživatele služby Multi-Factor authentication – snímek obrazovky](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Změnit stav pro uživatele

1. Použít předchozí kroky pro zajištění ověřování Azure Multi-Factor Authentication **uživatelé** stránky.
2. Vyhledejte uživatele, které chcete povolit pro Azure MFA. Možná budete muset změnit zobrazení v horní části. 
   ![Najít uživatele – snímek obrazovky](./media/howto-mfa-userstates/enable1.png)
3. Zaškrtněte políčko vedle jména.
4. Na pravé straně v části **rychlé kroky**, zvolte **povolit** nebo **zakázat**.
   ![Povolit vybraného uživatele – snímek obrazovky](./media/howto-mfa-userstates/user1.png)

   >[!TIP]
   >*Povolit* uživatelé jsou automaticky přepnout do *vynucené* při registraci pro Azure MFA. Proveďte ručně změnit stav uživatele *vynucené*. 

5. Potvrďte výběr v místním okně, které se otevře. 

Jakmile uživatele povolíte, upozorněte je e-mailem. Sdělte jim, že budete vyzváni k registraci při příštím přihlášení. Navíc pokud vaše organizace používá neprohlížečové aplikace, které nepodporují moderní ověřování, budou muset vytvořit hesla aplikací. Můžete použít také odkaz [Průvodce pro koncové uživatele Azure MFA](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user.md) pomáhá jim začít pracovat.

### <a name="use-powershell"></a>Použití prostředí PowerShell
Chcete-li změnit stav uživatele pomocí [Azure AD PowerShell](/powershell/azure/overview), změňte `$st.State`. Existují tři možné stavy:

* Povoleno
* Vynuceno
* Zakázáno  

Nelze přesunout přímo na uživatele *vynucené* stavu. Pokud tak učiníte, aplikace nezaložené prohlížeče přestat fungovat, protože nebyl uživatel neprošel registrací Azure MFA a získat [heslo aplikace](howto-mfa-mfasettings.md#app-passwords). 

Pomocí prostředí PowerShell je vhodný, když potřebujete hromadné povolení uživatelů. Vytvořte Powershellový skript, který prochází seznam uživatelů a umožňuje jim:

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

## <a name="enable-azure-mfa-with-a-conditional-access-policy"></a>Povolit Azure MFA se zásadami podmíněného přístupu

_Podmíněný přístup_ je placené funkce služby Azure Active Directory, s mnoho možností konfigurace. Tyto kroky provede jeden způsob, jak vytvořit zásady. Další informace najdete v tématu o [podmíněného přístupu v Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Přejděte na **Azure Active Directory** > **podmíněného přístupu**.
3. Vyberte **nové zásady**.
4. V části **přiřazení**, vyberte **uživatelů a skupin**. Použití **zahrnout** a **vyloučit** karty se můžete určit, kteří uživatelé a skupiny spravuje zásady.
5. V části **přiřazení**, vyberte **cloudových aplikací**. Vybrat možnost zahrnutí **všech cloudových aplikací**.
6. V části **přístup k ovládacím prvkům**, vyberte **Grant**. Zvolte **vyžadovat vícefaktorové ověřování**.
7. Zapnout **povolit zásady** k **na**a potom vyberte **Uložit**.

Další možnosti v zásadách podmíněného přístupu získáte možnost zadat přesně, pokud je nutné použít dvoustupňové ověřování. Například můžete použít zásady, jako je tento: když dodavatelů došlo k pokusu o přístup k vaší aplikace Nákup z nedůvěryhodné sítě na zařízení, které nejsou připojené k doméně, vyžaduje dvoustupňové ověřování. 

## <a name="next-steps"></a>Další postup

- Získat tipy pro [osvědčené postupy pro podmíněný přístup](../active-directory-conditional-access-best-practices.md).

- Spravovat nastavení ověřování Azure Multi-Factor Authentication pro [uživatelům a jejich zařízení](howto-mfa-userdevicesettings.md).
