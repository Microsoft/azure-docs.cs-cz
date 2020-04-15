---
title: Povolení vícefaktorového ověřování na uživatele – služba Azure Active Directory
description: Zjistěte, jak povolit vícefaktorové ověřování Azure pro jednotlivé uživatele změnou stavu uživatele.
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
ms.openlocfilehash: 3e8ceaf13324864c7ec3df731c3e710815b0eba9
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309786"
---
# <a name="enable-per-user-azure-multi-factor-authentication-to-secure-sign-in-events"></a>Povolení vícefaktorového ověřování Azure pro jednotlivé uživatele pro zabezpečení událostí přihlášení

Existují dva způsoby, jak zabezpečit události přihlášení uživatele vyžadováním vícefaktorového ověřování ve službě Azure AD. První a upřednostňovanou možností je nastavení zásady podmíněného přístupu, která za určitých podmínek vyžaduje vícefaktorové ověřování. Druhou možností je povolit každému uživateli azure multifaktorové ověřování. Pokud jsou uživatelé povoleni jednotlivě, provádějí vícefaktorové ověřování při každém přihlášení (s některými výjimkami, například při přihlášení z důvěryhodných IP adres nebo když je zapnutá funkce _zapamatovaných zařízení)._

> [!NOTE]
> Povolení azure multifaktorové ověřování pomocí zásad podmíněného přístupu je doporučený přístup. Změna stavu uživatelů se již nedoporučuje, pokud vaše licence nezahrnují podmíněný přístup, protože vyžaduje, aby uživatelé prováděli vícefaktorové ověřování při každém přihlášení.
>
> Pokud chcete začít používat podmíněný přístup, [přečtěte si informace o kurzu: Zabezpečené události přihlášení uživatelů pomocí azure multifaktorového ověřování](tutorial-enable-azure-mfa.md).

## <a name="azure-multi-factor-authentication-user-states"></a>Stavy uživatelů azure s vícefaktorovým ověřováním

Uživatelské účty v Azure Multi-Factor Authentication mají následující tři odlišné stavy:

> [!IMPORTANT]
> Povolení azure vícefaktorového ověřování prostřednictvím zásad podmíněného přístupu nezmění stav uživatele. Nebuďte znepokojeni, pokud se uživatelé zobrazí jako zakázaní. Podmíněný přístup nezmění stav.
>
> **Pokud používáte zásady podmíněného přístupu, neměli byste povolovat ani vynucovat uživatele.**

| Status | Popis | Ovlivněné aplikace, které nejsou ovlivněny prohlížečem | Ovlivněné aplikace prohlížeče | Ovlivněno moderní ověřování |
|:---:| --- |:---:|:--:|:--:|
| Zakázáno | Výchozí stav pro nového uživatele, který není zaregistrovaný v Azure Multi-Factor Authentication. | Ne | Ne | Ne |
| Povoleno | Uživatel byl zaregistrován v Azure Multi-Factor Authentication, ale nezaregistroval. Při příštím přihlášení obdrží výzvu k registraci. | Ne.  Pokračují v práci, dokud není dokončen proces registrace. | Ano. Po vypršení platnosti relace je vyžadována registrace Azure Multi-Factor Authentication.| Ano. Po vypršení platnosti přístupového tokenu je vyžadována registrace Azure Multi-Factor Authentication. |
| Vynuceno | Uživatel byl zaregistrován a dokončil proces registrace pro azure multi-factor authentication. | Ano. Aplikace vyžadují hesla aplikací. | Ano. Azure Multi-Factor Authentication je vyžadováno při přihlášení. | Ano. Azure Multi-Factor Authentication je vyžadováno při přihlášení. |

Stav uživatele odráží, zda je správce zaregistroval do Azure Multi-Factor Authentication a zda dokončil proces registrace.

Všichni uživatelé spustit *zakázáno*. Když zaregistrujete uživatele do Azure Multi-Factor Authentication, jejich stav se změní na *Povoleno*. Pokud je povoleno, uživatelé se přihlašují a dokončí proces registrace, jejich stav se změní na *Vynuceno*.

> [!NOTE]
> Pokud je vícefaktorové ověřování znovu povoleno u objektu uživatele, který už má podrobnosti o registraci, jako je například telefon nebo e-mail, pak správci musí mít tento uživatel znovu zaregistrovat Vícefaktorové ověřování prostřednictvím portálu Azure nebo PowerShellu. Pokud se uživatel znovu nezaregistruje, jeho stav MFA nepřechází z *Povoleno* na *Vynuceno* v uživatelském rozhraní správy mfa.

## <a name="view-the-status-for-a-user"></a>Zobrazení stavu uživatele

Pomocí následujících kroků přejdete na stránku portálu Azure, kde můžete zobrazit a spravovat stavy uživatelů:

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako správce.
1. Vyhledejte a vyberte *službu Azure Active Directory*a vyberte **možnost Uživatelé** > **Všichni uživatelé**.
1. Vyberte **vícefaktorové ověřování**. Chcete-li zobrazit tuto možnost nabídky, může být nutné posunout doprava. Výběrem ukázkového snímku obrazovky níže zobrazíte celé okno portálu Azure a umístění nabídky:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Vyberte vícefaktorové ověřování z okna Uživatelé ve službě Azure AD.")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Otevře se nová stránka, která zobrazuje stav uživatele, jak je znázorněno v následujícím příkladu.
   ![Snímek obrazovky s ukázkovými informacemi o stavu uživatele pro azure multifaktorové ověřování](./media/howto-mfa-userstates/userstate1.png)

## <a name="change-the-status-for-a-user"></a>Změna stavu uživatele

Chcete-li změnit stav azure multi-factor authentication pro uživatele, proveďte následující kroky:

1. Pomocí předchozích kroků se dostanete na stránku **uživatelů** azure vícefaktorového ověřování.
1. Najděte uživatele, kterého chcete povolit pro azure multifaktorové ověřování. Je možné, že bude nutné změnit zobrazení v horní části na **uživatele**.
   ![Vyberte uživatele, pro který chcete změnit stav na kartě Uživatelé.](./media/howto-mfa-userstates/enable1.png)
1. Zaškrtněte políčko vedle jména uživatele, pro které chcete změnit stav.
1. Na pravé straně v **rychlých krocích**zvolte **Povolit** nebo **Zakázat**. V následujícím příkladu má uživatel *Jan Novák* kontrolu vedle svého jména ![a je povolen pro použití: Povolit vybraného uživatele kliknutím na povolit v nabídce rychlých kroků](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Oprávnění* uživatelé se při registraci pro vícefaktorové ověřování Azure automaticky přepnou na *Vynuceno.* Neměňte ručně stav uživatele na *Vynuceno*.

1. Potvrďte výběr v rozbalovacím okně, které se otevře.

Po povolení uživatelů, upozorněte je e-mailem. Sdělte uživatelům, že se zobrazí výzva, která je požádá o registraci při příštím přihlášení. Pokud vaše organizace používá aplikace, které nepodporují moderní ověřování, musí také vytvářet hesla aplikací. Další informace najdete v [průvodci koncovým uživatelem Azure Multi-Factor Authentication,](../user-help/multi-factor-authentication-end-user.md) která jim pomůže začít.

## <a name="change-state-using-powershell"></a>Změna stavu pomocí PowerShellu

Chcete-li změnit stav uživatele pomocí prostředí Azure `$st.State` [AD PowerShell](/powershell/azure/overview), změňte parametr pro uživatelský účet. Existují tři možné stavy pro uživatelský účet:

* *Enabled* (Povoleno)
* *Vynuceno*
* *Disabled* (Zakázáno)  

Nepřesouvejte uživatele přímo do stavu *Vynuceno.* Pokud tak učiníte, přestanou fungovat aplikace, které nejsou založené na prohlížeči, protože uživatel neprošel registrací Azure Multi-Factor Authentication a nezískal [heslo aplikace](howto-mfa-mfasettings.md#app-passwords).

Chcete-li začít, nainstalujte modul *MSOnline* pomocí [instalačního modulu](/powershell/module/powershellget/install-module) následujícím způsobem:

```PowerShell
Install-Module MSOnline
```

Dále připojte pomocí [služby Connect-MsolService](/powershell/module/msonline/connect-msolservice):

```PowerShell
Connect-MsolService
```

Následující příklad skriptu prostředí PowerShell umožňuje *bsimon@contoso.com*vícefaktorové ověřování pro jednotlivéuživatele s názvem :

```PowerShell
$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
$st.RelyingParty = "*"
$st.State = "Enabled"
$sta = @($st)

# Change the following UserPrincipalName to the user you wish to change state
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
```

Použití prostředí PowerShell je dobrá volba, když potřebujete hromadně povolit uživatele. Následující skript prochází seznam uživatelů a umožňuje vícefaktorové informace o jejich účtech. V prvním řádku `$users` nastavte uživatelské účty takto:

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

Chcete-li zakázat vícefaktorové ověřování, následující příklad získá uživatele s [Get-MsolUser](/powershell/module/msonline/get-msoluser), pak odebere všechny *StrongAuthenticationRequirements* nastavit pro definovaného uživatele pomocí [Set-MsolUser](/powershell/module/msonline/set-msoluser):

```PowerShell
Get-MsolUser -UserPrincipalName bsimon@contoso.com | Set-MsolUser -StrongAuthenticationRequirements @()
```

Můžete také přímo zakázat vícefaktorové ověřování pro uživatele pomocí [Set-MsolUser](/powershell/module/msonline/set-msoluser) takto:

```PowerShell
Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements @()
```

## <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Převod uživatelů z vícefaktorové ověřování pro jednotlivé uživatele na vícefaktorové ověřování založené na podmíněném přístupu

Následující Prostředí PowerShell vám může pomoci při převodu na azure multifaktorové ověřování založené na podmíněném přístupu.

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
> Nedávno jsme změnili chování a tento skript prostředí PowerShell. Dříve skript uložen mimo metody MFA, zakázal vícefaktorové financování a obnovil metody. To již není nutné nyní, že výchozí chování pro zakázat nevymaže metody.
>
> Pokud je vícefaktorové ověřování znovu povoleno u objektu uživatele, který už má podrobnosti o registraci, jako je například telefon nebo e-mail, pak správci musí mít tento uživatel znovu zaregistrovat Vícefaktorové ověřování prostřednictvím portálu Azure nebo PowerShellu. Pokud se uživatel znovu nezaregistruje, jeho stav MFA nepřechází z *Povoleno* na *Vynuceno* v uživatelském rozhraní správy mfa.

## <a name="next-steps"></a>Další kroky

Pokud chcete nakonfigurovat nastavení Azure pro vícefaktorové ověřování, jako jsou důvěryhodné IP adresy, vlastní hlasové zprávy a upozornění na podvody, přečtěte si informace [o konfiguraci nastavení azure multifaktorového ověřování](howto-mfa-mfasettings.md). Pokud chcete spravovat uživatelská nastavení pro vícefaktorové ověřování Azure, přečtěte si informace [o správě uživatelských nastavení pomocí azure multifaktorového ověřování](howto-mfa-userdevicesettings.md).

Informace o tom, proč byl uživatel vyzván nebo nebyl vyzván k provedení vícefaktorového ověřování, najdete [v tématu Sestavy azure multifaktorové ověřování](howto-mfa-reporting.md#azure-ad-sign-ins-report).
