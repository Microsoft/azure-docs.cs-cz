---
title: Povolit Multi-Factor Authentication pro jednotlivé uživatele – Azure Active Directory
description: Přečtěte si, jak povolit Multi-Factor Authentication Azure AD pro jednotlivé uživatele změnou stavu uživatele.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 08/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 749829f641119273813d3c8ca826daf8b4dc4d11
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742659"
---
# <a name="enable-per-user-azure-ad-multi-factor-authentication-to-secure-sign-in-events"></a>Povolení Multi-Factor Authentication Azure AD pro jednotlivé uživatele k zabezpečení přihlašovacích událostí

K zabezpečení událostí přihlašování uživatelů ve službě Azure AD můžete vyžadovat vícefaktorové ověřování (MFA). Doporučeným přístupem k ochraně uživatelů je povolení Multi-Factor Authentication služby Azure AD pomocí zásad podmíněného přístupu. Podmíněný přístup je funkce Azure AD Premium P1 nebo P2, která umožňuje použít pravidla, podle potřeby v určitých scénářích vyžadovat MFA. Pokud chcete začít používat podmíněný přístup, přečtěte si [kurz: události přihlašování uživatelů zabezpečených uživateli pomocí Multi-Factor Authentication Azure AD](tutorial-enable-azure-mfa.md).

Pro klienty bezplatné služby Azure AD bez podmíněného přístupu můžete [použít výchozí nastavení zabezpečení k ochraně uživatelů](../fundamentals/concept-fundamentals-security-defaults.md). V případě potřeby se uživatelům zobrazí výzva k zadání vícefaktorového ověřování, ale nemůžete definovat vlastní pravidla pro řízení chování.

V případě potřeby můžete místo toho povolit každý účet Multi-Factor Authentication Azure AD pro jednotlivé uživatele. Když jsou uživatelé povoleni samostatně, provádějí ověřování službou Multi-Factor Authentication pokaždé, když se přihlásí (s některými výjimkami, například když se přihlásí z důvěryhodných IP adres nebo když je zapnutá funkce _zapamatování MFA u důvěryhodných zařízení_ ).

Změna stavů uživatele se nedoporučuje, pokud licence Azure AD neobsahují podmíněný přístup a nechcete používat výchozí hodnoty zabezpečení. Další informace o různých způsobech povolení vícefaktorového ověřování najdete v tématu [funkce a licence pro Azure AD Multi-Factor Authentication](concept-mfa-licensing.md).

> [!IMPORTANT]
>
> Tento článek podrobně popisuje, jak zobrazit a změnit stav pro Multi-Factor Authentication Azure AD pro jednotlivé uživatele. Pokud používáte výchozí nastavení podmíněného přístupu nebo zabezpečení, nebudete tyto kroky kontrolovat ani Nepovolovat uživatelské účty.
>
> Povolení Multi-Factor Authentication služby Azure AD prostřednictvím zásad podmíněného přístupu nezmění stav uživatele. Nepoužívejte alarm, pokud se uživatelé jeví jako zakázané. Podmíněný přístup nezmění stav.
>
> **Pokud používáte zásady podmíněného přístupu, nepovolujte ani neuplatněte Multi-Factor Authentication Azure AD pro jednotlivé uživatele.**

## <a name="azure-ad-multi-factor-authentication-user-states"></a>Uživatelské stavy Multi-Factor Authentication služby Azure AD

Stav uživatele odráží, jestli ho správce zaregistroval v Multi-Factor Authentication Azure AD pro jednotlivé uživatele. Uživatelské účty ve službě Azure AD Multi-Factor Authentication mají následující tři různé stavy:

| State | Popis | Ovlivněné starší verze ověřování | Ovlivněné aplikace v prohlížeči | Moderní ověřování ovlivněno |
|:---:| --- |:---:|:--:|:--:|
| Zakázáno | Výchozí stav pro uživatele, který není zaregistrovaný v Multi-Factor Authentication Azure AD pro jednotlivé uživatele. | No | No | No |
| Povoleno | Uživatel je zaregistrovaný v Multi-Factor Authentication Azure AD pro jednotlivé uživatele, ale může používat i heslo pro starší verze ověřování. Pokud uživatel ještě nezaregistroval metody ověřování MFA, obdrží výzvu k registraci při příštím přihlášení pomocí moderního ověřování (například přes webový prohlížeč). | No. Starší verze ověřování pokračuje v práci až do dokončení procesu registrace. | Yes. Po vypršení platnosti relace se vyžaduje registrace Azure AD Multi-Factor Authentication.| Yes. Po vypršení platnosti přístupového tokenu se vyžaduje registrace Azure AD Multi-Factor Authentication. |
| Vynuceno | Uživatel je zaregistrovaný pro každého uživatele ve službě Azure AD Multi-Factor Authentication. Pokud uživatel ještě nezaregistroval metody ověřování, obdrží výzvu k registraci při příštím přihlášení pomocí moderního ověřování (například přes webový prohlížeč). Uživatelé, kteří dokončí registraci v *povoleném* stavu, se automaticky přesunou do stavu *vynutilo* . | Yes. Aplikace vyžadují hesla aplikací. | Yes. Při přihlášení se vyžaduje Multi-Factor Authentication Azure AD. | Yes. Při přihlášení se vyžaduje Multi-Factor Authentication Azure AD. |

Všichni uživatelé začínají *zakázáni*. Když zaregistrujete uživatele v Multi-Factor Authentication Azure AD pro jednotlivé uživatele, jejich stav se změní na *povoleno*. Když se uživatelé s povoleným přihlášením a dokončí proces registrace, jejich stav se změní na *vynutilo*. Správci mohou přesouvat uživatele mezi stavy, včetně *Vynutilosti* *povoleno* nebo *zakázáno*.

> [!NOTE]
> Pokud je pro uživatele znovu povoleno ověřování MFA pro uživatele a uživatel se znovu neregistruje, jejich stav MFA nepřejde z *Enabled* na *vynutilo* v UŽIVATELSKÉM rozhraní pro správu MFA. Správce musí uživatele přesunout přímo do *vymáhání*.

## <a name="view-the-status-for-a-user"></a>Zobrazit stav uživatele

Chcete-li zobrazit a spravovat stavy uživatelů, proveďte následující kroky pro přístup na stránku Azure Portal:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako správce.
1. Vyhledejte a vyberte *Azure Active Directory* a pak vyberte **Uživatelé**  >  **Všichni uživatelé**.
1. Vyberte **Multi-Factor Authentication**. Pokud chcete zobrazit tuto možnost nabídky, možná se budete muset posunout doprava. Kliknutím na ukázkový snímek obrazovky zobrazíte celé Azure Portal okno a umístění nabídky: [ ![ Vyberte Multi-Factor Authentication z okna uživatelé ve službě Azure AD.](media/howto-mfa-userstates/selectmfa-cropped.png)](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Otevře se nová stránka, která zobrazí stav uživatele, jak je znázorněno v následujícím příkladu.
   ![Snímek obrazovky, který ukazuje ukázkové informace o stavu uživatele pro Azure AD Multi-Factor Authentication](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>Změna stavu uživatele

Pokud chcete pro uživatele změnit stav Multi-Factor Authentication Azure AD pro jednotlivé uživatele, proveďte následující kroky:

1. Pomocí předchozích kroků si můžete [Zobrazit stav uživatele](#view-the-status-for-a-user) , který se dostane na stránku Multi-Factor Authentication **uživatelů** Azure AD.
1. Vyhledejte uživatele, kterého chcete povolit pro Multi-Factor Authentication Azure AD pro jednotlivé uživatele. Možná budete muset změnit zobrazení na nejvyšší úrovni na **Uživatelé**.
   ![Na kartě Uživatelé vyberte uživatele, jehož stav chcete změnit.](./media/howto-mfa-userstates/enable1.png)
1. Zaškrtněte políčko vedle jména uživatelů, pro které chcete změnit stav.
1. Na pravé straně v části **rychlé kroky** vyberte **Povolit** nebo **Zakázat**. V následujícím příkladu má uživatel *Jan Novák* zaškrtnutí vedle jejich názvu a je povolený pro použití: ![ Povolit vybraného uživatele kliknutím na Povolit v nabídce rychlé kroky.](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Povolení* uživatelé se automaticky přepínají, aby se *vynutili* při registraci k Azure AD Multi-Factor Authentication. Neměňte ručně stav uživatele, který se má *vykonat* , pokud už není zaregistrovaný uživatel nebo pokud je přijatelné, aby uživatel mohl v připojení k starším ověřovacím protokolům dojít k přerušení.

1. Potvrďte výběr v automaticky otevíraném okně, které se otevře.

Jakmile povolíte uživatele, upozorněte je e-mailem. Sdělte uživatelům, že se zobrazí výzva, aby se zaregistrovali při příštím přihlášení. Pokud vaše organizace používá neprohlížečové aplikace, které nepodporují moderní ověřování, musí také vytvářet hesla aplikací. Další informace najdete v tématu [Azure AD Multi-Factor Authentication Průvodce koncovými uživateli](../user-help/multi-factor-authentication-end-user-first-time.md) , který jim pomůže začít.

## <a name="change-state-using-powershell"></a>Změna stavu pomocí PowerShellu

Pokud chcete změnit stav uživatele pomocí [Azure AD PowerShellu](/powershell/azure/), změňte `$st.State` parametr pro uživatelský účet. Existují tři možné stavy pro uživatelský účet:

* *Povoleno*
* *Vynuceno*
* *Zakázáno*  

Obecně platí, že nepřesouváte uživatele přímo do *vystavení* , pokud už nejsou zaregistrované pro MFA. Pokud tak učiníte, starší aplikace ověřování přestanou fungovat, protože uživatel neprošlý prostřednictvím služby Azure AD Multi-Factor Authentication registraci a nezískal [heslo aplikace](howto-mfa-app-passwords.md). V některých případech může být toto chování žádoucí, ale má vliv na činnost uživatele, dokud se uživatel neregistruje.

Začněte tím, že pomocí [instalačního modulu](/powershell/module/powershellget/install-module) nainstalujete modul *MSOnline* následujícím způsobem:

```PowerShell
Install-Module MSOnline
```

V dalším kroku se připojte pomocí [Connect-MsolService](/powershell/module/msonline/connect-msolservice):

```PowerShell
Connect-MsolService
```

Následující ukázkový skript PowerShellu umožňuje MFA pro jednotlivé uživatele s názvem *bsimon@contoso.com* :

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

Použití prostředí PowerShell je vhodné, pokud potřebujete hromadně povolit uživatele. Následující skript projde seznam uživatelů a povolí MFA na svých účtech. Zadejte uživatelské účty, které si v prvním řádku nastavili, a to následujícím `$users` způsobem:

   ```PowerShell
   # Define your list of users to update state in bulk
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

Pokud chcete zakázat MFA, následující příklad získá uživatele s rutinou [Get-MsolUser](/powershell/module/msonline/get-msoluser)a pak odebere všechny *StrongAuthenticationRequirements* sady pro definovaného uživatele pomocí rutiny [set-MsolUser](/powershell/module/msonline/set-msoluser):

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

MFA můžete také přímo zakázat pro uživatele pomocí [set-MsolUser](/powershell/module/msonline/set-msoluser) následujícím způsobem:

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access"></a>Převod uživatelů z MFA na uživatele na podmíněný přístup

Následující PowerShell vám může pomoci při převodu na Multi-Factor Authentication Azure AD založené na podmíněném přístupu.

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

> [!NOTE]
> Pokud je u uživatele znovu povoleno ověřování MFA a uživatel se znovu neregistruje, jejich stav MFA nepřejde z *povoleného* na *vynutilo* v UŽIVATELSKÉM rozhraní pro správu MFA. V takovém případě musí správce přesunout uživatele přímo do *vymáhání*.

## <a name="next-steps"></a>Další kroky

Informace o konfiguraci nastavení Multi-Factor Authentication služby Azure AD najdete v tématu  [Konfigurace nastavení Multi-Factor Authentication Azure AD](howto-mfa-mfasettings.md).

Pokud chcete spravovat nastavení uživatelů pro Multi-Factor Authentication Azure AD, přečtěte si téma [Správa uživatelských nastavení pomocí Azure ad Multi-Factor Authentication](howto-mfa-userdevicesettings.md).

Informace o tom, proč se uživateli zobrazila výzva k provedení MFA, najdete v tématu [sestavy Multi-Factor Authentication služby Azure AD](howto-mfa-reporting.md).
