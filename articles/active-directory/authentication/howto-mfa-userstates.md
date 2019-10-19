---
title: Uživatelské stavy pro Azure Multi-Factor Authentication – Azure Active Directory
description: Přečtěte si informace o stavech uživatelů v Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ee1d282506b537ed29592ca9008c88a53220d7d
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554836"
---
# <a name="how-to-require-two-step-verification-for-a-user"></a>Jak pro uživatele vyžadovat dvoustupňové ověřování

Pro Vyžadování dvoustupňového ověřování můžete použít jeden ze dvou kroků. oba vyžadují použití účtu globálního správce. První možností je povolit každému uživateli Azure Multi-Factor Authentication (MFA). Když jsou uživatelé povoleni samostatně, provádějí dvoustupňové ověřování pokaždé, když se přihlásí (s některými výjimkami, například když se přihlásí z důvěryhodných IP adres nebo když je zapnutá funkce _zapamatovaných zařízení_ ). Druhou možností je nastavit zásadu podmíněného přístupu, která za určitých podmínek vyžaduje dvoustupňové ověřování.

> [!TIP]
> Doporučený postup je povolení Azure Multi-Factor Authentication pomocí zásad podmíněného přístupu. Změna stavů uživatele se už nedoporučuje, pokud vaše licence neobsahují podmíněný přístup, protože budou vyžadovat, aby uživatelé při každém přihlášení provedli vícefaktorové ověřování.

## <a name="choose-how-to-enable"></a>Volba způsobu povolení

**Povoleno změnou stavu uživatele** – Toto je tradiční metoda pro Vyžadování dvoustupňového ověřování a je popsána v tomto článku. Funguje s Azure MFA v cloudu i v Azure MFA serveru. Použití této metody vyžaduje, aby uživatelé prováděli dvoustupňové ověřování **pokaždé, když** se přihlásí, a přepíše zásady podmíněného přístupu.

Povoleno zásadami podmíněného přístupu – toto je nejpružnější způsob, jak pro uživatele povolit dvoustupňové ověřování. Povolení použití zásad podmíněného přístupu funguje jenom pro Azure MFA v cloudu a nabízí prémiovou funkci Azure AD. Další informace o této metodě najdete v [nasazení cloudové Multi-Factor Authentication Azure](howto-mfa-getstarted.md).

Povoleno pomocí Azure AD Identity Protection – tato metoda používá zásady rizik Azure AD Identity Protection k Vyžadování dvoustupňového ověřování založeného pouze na riziku přihlášení pro všechny cloudové aplikace. Tato metoda vyžaduje licencování Azure Active Directory P2. Další informace o této metodě najdete v [Azure Active Directory Identity Protection](../identity-protection/howto-sign-in-risk-policy.md)

> [!Note]
> Další informace o licencích a cenách najdete na stránce s cenami [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) a [Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) .

## <a name="enable-azure-mfa-by-changing-user-state"></a>Povolení Azure MFA změnou stavu uživatele

Uživatelské účty v Azure Multi-Factor Authentication mají následující tři různé stavy:

| Stav | Popis | Neprohlížečové aplikace ovlivněny | Ovlivněné aplikace v prohlížeči | Moderní ověřování ovlivněno |
|:---:|:---:|:---:|:--:|:--:|
| Zakázáno |Výchozí stav nového uživatele, který není zaregistrovaný v Azure MFA. |Ne |Ne |Ne |
| Povoleno |Uživatel je zaregistrovaný v Azure MFA, ale není zaregistrovaný. Obdrží výzvu k registraci při příštím přihlášení. |Ne.  Budou dál fungovat, dokud se proces registrace nedokončí. | Ano. Po vypršení platnosti relace se vyžaduje registrace Azure MFA.| Ano. Po vypršení platnosti přístupového tokenu se vyžaduje registrace Azure MFA. |
| Vynuceno |Uživatel je zaregistrovaný a dokončil proces registrace pro Azure MFA. |Ano. Aplikace vyžadují hesla aplikací. |Ano. Při přihlášení se vyžaduje Azure MFA. | Ano. Při přihlášení se vyžaduje Azure MFA. |

Stav uživatele odráží, jestli ho správce zaregistroval v Azure MFA, a jestli dokončil proces registrace.

Všichni uživatelé začínají *zakázáni*. Když zaregistrujete uživatele v Azure MFA, jejich stav se změní na *povoleno*. Když se uživatelé s povoleným přihlášením a dokončí proces registrace, jejich stav se změní na *vynutilo*.  

### <a name="view-the-status-for-a-user"></a>Zobrazit stav uživatele

Pro přístup ke stránce, kde můžete zobrazit a spravovat stavy uživatelů, použijte následující postup:

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Přejít na **Azure Active Directory**  > **uživatelé a skupiny**  > **všech uživatelích**.
3. Vyberte **Multi-Factor Authentication**.
   ![Select Multi-Factor Authentication ](./media/howto-mfa-userstates/selectmfa.png)
4. Otevře se nová stránka, která zobrazuje stav uživatele.
   stav uživatele ![multi-Factor Authentication – snímek obrazovky ](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Změna stavu uživatele

1. Pomocí předchozích kroků se dostanete na stránku Azure Multi-Factor Authentication **Users** .
2. Vyhledejte uživatele, kterého chcete povolit pro Azure MFA. Možná budete muset změnit zobrazení v horní části.
   ![Select uživateli změnit stav na kartě Uživatelé ](./media/howto-mfa-userstates/enable1.png)
3. Zaškrtněte políčko vedle jeho názvu.
4. Na pravé straně v části **rychlé kroky**vyberte **Povolit** nebo **Zakázat**.
   ![Enable vybraného uživatele kliknutím na Povolit v nabídce rychlé kroky ](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Povolení* uživatelé se automaticky přepínají, aby se *vynutili* při registraci pro Azure MFA. Neměňte ručně stav uživatele na *vynutilo*.

5. Potvrďte výběr v automaticky otevíraném okně, které se otevře.

Jakmile povolíte uživatele, upozorněte je e-mailem. Sdělte jim, že se budou požádáni, aby se zaregistrovali při příštím přihlášení. Pokud vaše organizace používá neprohlížečové aplikace, které nepodporují moderní ověřování, musí také vytvářet hesla aplikací. Můžete také přidat odkaz na [Průvodce koncovými uživateli Azure MFA](../user-help/multi-factor-authentication-end-user.md) , který jim pomůže začít.

### <a name="use-powershell"></a>Použití PowerShellu

Pokud chcete změnit stav uživatele pomocí [Azure AD PowerShellu](/powershell/azure/overview), změňte `$st.State`. Existují tři možné stavy:

* Povoleno
* Vynuceno
* Zakázáno  

Nepřesouvat uživatele přímo do *Vynutilého* stavu. Pokud to uděláte, aplikace nezaložené na prohlížeči přestanou fungovat, protože uživatel neprošl registrací Azure MFA a nezískal [heslo aplikace](howto-mfa-mfasettings.md#app-passwords).

Nejdřív nainstalujte modul pomocí:

   ```PowerShell
   Install-Module MSOnline
   ```

> [!TIP]
> Nezapomeňte se připojit jako první pomocí **Connect-MsolService** .

Tento ukázkový skript PowerShellu umožňuje MFA pro jednotlivé uživatele:

   ```PowerShell
   Import-Module MSOnline
   $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
   $st.RelyingParty = "*"
   $st.State = "Enabled"
   $sta = @($st)
   Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
   ```

Použití prostředí PowerShell je vhodné, pokud potřebujete hromadně povolit uživatele. Například následující skript projde seznamem uživatelů a povolí MFA na svých účtech:

   ```PowerShell
   $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
   foreach ($user in $users)
   {
       $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
       $st.RelyingParty = "*"
       $st.State = "Enabled"
       $sta = @($st)
       Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
   }
   ```

Pokud chcete zakázat MFA, použijte tento skript:

   ```PowerShell
   Get-MsolUser -UserPrincipalName user@domain.com | Set-MsolUser -StrongAuthenticationMethods @()
   ```

To může být také zkráceno na:

   ```PowerShell
   Set-MsolUser -UserPrincipalName user@domain.com -StrongAuthenticationRequirements @()
   ```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Převod uživatelů z ověřování MFA na uživatele na vícefaktorové ověřování na základě podmíněného přístupu

Následující PowerShell vám může pomoci při převodu na Multi-Factor Authentication Azure založené na podmíněném přístupu.

Spusťte tento PowerShell v okně ISE nebo ho uložte jako. Soubor PS1, který se má spustit místně

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

## <a name="next-steps"></a>Další kroky

* Proč se uživateli zobrazila výzva nebo nezobrazila výzva k provedení MFA? Viz část [Sestava přihlášení k Azure AD v sestavách v dokumentu azure Multi-Factor Authentication](howto-mfa-reporting.md#azure-ad-sign-ins-report).
* Informace o konfiguraci dalších nastavení, jako jsou důvěryhodné IP adresy, vlastní hlasové zprávy a výstrahy na podvod, najdete v článku [Konfigurace nastavení služby Azure Multi-Factor Authentication](howto-mfa-mfasettings.md) .
* Informace o správě uživatelských nastavení pro Azure Multi-Factor Authentication najdete v článku [Správa uživatelských nastavení pomocí Azure Multi-Factor Authentication v cloudu](howto-mfa-userdevicesettings.md) .
