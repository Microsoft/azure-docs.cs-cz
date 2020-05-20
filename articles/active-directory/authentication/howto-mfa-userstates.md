---
title: Povolit Multi-Factor Authentication pro jednotlivé uživatele – Azure Active Directory
description: Přečtěte si, jak povolit Multi-Factor Authentication Azure pro jednotlivé uživatele změnou stavu uživatele.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0db72e30fbced17665c112ad56510d7c2ca23d12
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83639626"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>Povolení služby Azure Multi-Factor Authentication pro jednotlivé uživatele k zabezpečení přihlašovacích událostí

Existují dva způsoby, jak zabezpečit události přihlašování uživatelů pomocí služby Multi-Factor Authentication ve službě Azure AD. První a upřednostňovaná možnost je nastavit zásadu podmíněného přístupu, která za určitých podmínek vyžaduje vícefaktorové ověřování. Druhou možností je povolit každému uživateli Azure Multi-Factor Authentication. Když jsou uživatelé povoleni samostatně, provádějí službu Multi-Factor Authentication pokaždé, když se přihlásí (s některými výjimkami, například když se přihlásí z důvěryhodných IP adres nebo když je zapnutá funkce _zapamatovaných zařízení_ ).

> [!NOTE]
> Doporučený postup je povolení Azure Multi-Factor Authentication pomocí zásad podmíněného přístupu. Změna stavů uživatele se už nedoporučuje, pokud vaše licence nezahrnují podmíněný přístup, protože vyžadují, aby uživatelé prováděli MFA při každém přihlášení.
>
> Pokud chcete začít používat podmíněný přístup, přečtěte si téma [kurz: zabezpečení událostí přihlašování uživatelů pomocí Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md).

## <a name="azure-multi-factor-authentication-user-states"></a>Stavy uživatelů v Azure Multi-Factor Authentication

Uživatelské účty v Azure Multi-Factor Authentication mají následující tři různé stavy:

> [!IMPORTANT]
> Povolení služby Azure Multi-Factor Authentication prostřednictvím zásad podmíněného přístupu nezmění stav uživatele. Nepoužívejte alarm, pokud se uživatelé jeví jako zakázané. Podmíněný přístup nezmění stav.
>
> **Pokud používáte zásady podmíněného přístupu, neměli byste povolit ani vyhovět uživatelům.**

| Status | Popis | Neprohlížečové aplikace ovlivněny | Ovlivněné aplikace v prohlížeči | Moderní ověřování ovlivněno |
|:---:| --- |:---:|:--:|:--:|
| Zakázáno | Výchozí stav nového uživatele, který není zaregistrovaný v Azure Multi-Factor Authentication. | Ne | Ne | Ne |
| Povoleno | Uživatel je zaregistrovaný v Azure Multi-Factor Authentication, ale nezaregistroval. Obdrží výzvu k registraci při příštím přihlášení. | Ne.  Budou dál fungovat, dokud se proces registrace nedokončí. | Ano. Po vypršení platnosti relace se vyžaduje registrace služby Azure Multi-Factor Authentication.| Ano. Po vypršení platnosti přístupového tokenu se vyžaduje registrace Azure Multi-Factor Authentication. |
| Vynuceno | Uživatel je zaregistrovaný a dokončil proces registrace pro Azure Multi-Factor Authentication. | Ano. Aplikace vyžadují hesla aplikací. | Ano. Při přihlášení se vyžaduje Azure Multi-Factor Authentication. | Ano. Při přihlášení se vyžaduje Azure Multi-Factor Authentication. |

Stav uživatele odráží, jestli ho správce zaregistroval v Azure Multi-Factor Authentication a jestli dokončil proces registrace.

Všichni uživatelé začínají *zakázáni*. Když zaregistrujete uživatele v Azure Multi-Factor Authentication, jejich stav se změní na *povoleno*. Když se uživatelé s povoleným přihlášením a dokončí proces registrace, jejich stav se změní na *vynutilo*.

> [!NOTE]
> Pokud je MFA znovu zapnuté u objektu uživatele, který už obsahuje podrobnosti o registraci, jako je telefon nebo e-mail, musí správci tento uživatel znovu zaregistrovat MFA prostřednictvím Azure Portal nebo PowerShellu. Pokud se uživatel znovu neregistruje, jejich stav MFA nepřejde z *Enabled* na *vynutilo* v UŽIVATELSKÉM rozhraní pro správu MFA.

## <a name="view-the-status-for-a-user"></a>Zobrazit stav uživatele

Následující postup použijte pro přístup k Azure Portal stránce, kde můžete zobrazit a spravovat stavy uživatelů:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako správce.
1. Vyhledejte a vyberte *Azure Active Directory*a pak vyberte **Uživatelé**  >  **Všichni uživatelé**.
1. Vyberte **Multi-Factor Authentication**. Pokud chcete zobrazit tuto možnost nabídky, možná se budete muset posunout doprava. Kliknutím na ukázkový snímek obrazovky zobrazíte celé Azure Portal okno a umístění nabídky:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Výběr Multi-Factor Authentication v okně uživatelé ve službě Azure AD")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Otevře se nová stránka, která zobrazí stav uživatele, jak je znázorněno v následujícím příkladu.
   ![Snímek obrazovky, který ukazuje ukázkové informace o stavu uživatele pro Azure Multi-Factor Authentication](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>Změna stavu uživatele

Pokud chcete změnit stav služby Azure Multi-Factor Authentication pro uživatele, proveďte následující kroky:

1. Pomocí předchozích kroků se dostanete na stránku Azure Multi-Factor Authentication **Users** .
1. Vyhledejte uživatele, kterého chcete povolit pro Azure Multi-Factor Authentication. Možná budete muset změnit zobrazení na nejvyšší úrovni na **Uživatelé**.
   ![Na kartě Uživatelé vyberte uživatele, jehož stav chcete změnit.](./media/howto-mfa-userstates/enable1.png)
1. Zaškrtněte políčko vedle jména uživatelů, pro které chcete změnit stav.
1. Na pravé straně v části **rychlé kroky**vyberte **Povolit** nebo **Zakázat**. V následujícím příkladu má uživatel *Jan Novák* zaškrtnutí vedle jejich názvu a je povolený pro použití: ![ Povolit vybraného uživatele kliknutím na Povolit v nabídce rychlé kroky.](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Povolení* uživatelé se automaticky přepínají, aby se *vynutili* při registraci k Azure Multi-Factor Authentication. Neměňte ručně stav uživatele na *vynutilo*.

1. Potvrďte výběr v automaticky otevíraném okně, které se otevře.

Jakmile povolíte uživatele, upozorněte je e-mailem. Sdělte uživatelům, že se zobrazí výzva, aby se zaregistrovali při příštím přihlášení. Pokud vaše organizace používá neprohlížečové aplikace, které nepodporují moderní ověřování, musí také vytvářet hesla aplikací. Další informace najdete v [Průvodci koncovými uživateli Azure Multi-Factor Authentication](../user-help/multi-factor-authentication-end-user.md) , který jim pomůže začít.

## <a name="change-state-using-powershell"></a>Změna stavu pomocí PowerShellu

Pokud chcete změnit stav uživatele pomocí [Azure AD PowerShellu](/powershell/azure/overview), změňte `$st.State` parametr pro uživatelský účet. Existují tři možné stavy pro uživatelský účet:

* *Enabled* (Povoleno)
* *Vynuceno*
* *Disabled* (Zakázáno)  

Nepřesouvat uživatele přímo do *Vynutilého* stavu. Pokud to uděláte, aplikace nezaložené na prohlížeči přestanou fungovat, protože uživatel se nedostal přes Azure Multi-Factor Authentication registraci a nezískal [heslo aplikace](howto-mfa-mfasettings.md#app-passwords).

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

## <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Převod uživatelů z ověřování MFA na uživatele na vícefaktorové ověřování na základě podmíněného přístupu

Následující PowerShell vám může pomoci při převodu na Multi-Factor Authentication Azure založené na podmíněném přístupu.

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
> Nedávno jsme změnili chování a tento skript PowerShellu. Dříve byl skript uložen mimo metody MFA, zakázal MFA a obnovil metody. Už to není nutné, když výchozí chování pro Disable nevymaže tyto metody.
>
> Pokud je MFA znovu zapnuté u objektu uživatele, který už obsahuje podrobnosti o registraci, jako je telefon nebo e-mail, musí správci tento uživatel znovu zaregistrovat MFA prostřednictvím Azure Portal nebo PowerShellu. Pokud se uživatel znovu neregistruje, jejich stav MFA nepřejde z *Enabled* na *vynutilo* v UŽIVATELSKÉM rozhraní pro správu MFA.

## <a name="next-steps"></a>Další kroky

Informace o konfiguraci nastavení Azure Multi-Factor Authentication, jako jsou důvěryhodné IP adresy, vlastní hlasové zprávy a výstrahy na podvod, najdete v tématu [Konfigurace nastavení azure Multi-Factor Authentication](howto-mfa-mfasettings.md). Pokud chcete spravovat uživatelská nastavení pro Azure Multi-Factor Authentication, přečtěte si téma [Správa uživatelských nastavení pomocí Azure Multi-Factor Authentication](howto-mfa-userdevicesettings.md).

Informace o tom, proč se uživateli zobrazila výzva k provedení MFA, najdete v tématu [sestavy služby Azure Multi-Factor Authentication](howto-mfa-reporting.md).
