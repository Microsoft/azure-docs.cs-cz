---
title: Vícefaktorové ověřování pro jednotlivé uživatele – služba Azure Active Directory
description: Povolte vícefaktorové ověřování změnou stavu uživatele v Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61d7227c57422cfe2228002750ec29bffa385d44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756758"
---
# <a name="how-to-require-two-step-verification-for-a-user"></a>Jak vyžadovat dvoustupňové ověření pro uživatele

Můžete použít jeden ze dvou přístupů pro vyžadování dvoustupňového ověření, které vyžadují použití účtu globálního správce. První možností je povolit každému uživateli azure vícefaktorové ověřování (MFA). Pokud jsou uživatelé povoleni jednotlivě, provádějí při každém přihlášení dvoustupňové ověření (s některými výjimkami, například při přihlášení z důvěryhodných IP adres nebo když je zapnutá funkce _zapamatovaných zařízení)._ Druhou možností je nastavení zásady podmíněného přístupu, která za určitých podmínek vyžaduje dvoustupňové ověření.

> [!TIP]
> Povolení azure multifaktorové ověřování pomocí zásad podmíněného přístupu je doporučený přístup. Změna stavu uživatelů se již nedoporučuje, pokud vaše licence nezahrnují podmíněný přístup, protože bude vyžadovat, aby uživatelé prováděli vícefaktorové ověřování při každém přihlášení.

## <a name="choose-how-to-enable"></a>Zvolte, jak povolit

**Povoleno změnou stavu uživatele** - Toto je tradiční metoda pro vyžadování dvoustupňového ověření a je popsána v tomto článku. Funguje s Azure MFA v cloudu i na Serveru Azure MFA. Pomocí této metody vyžaduje, aby uživatelé provádět dvoustupňové ověření **při každém** přihlášení a přepíše zásady podmíněného přístupu.

**Povoleno zásadami podmíněného přístupu** – jedná se o nejflexibilnější způsob, jak uživatelům povolit dvoustupňové ověření. Povolení použití zásad podmíněného přístupu funguje jenom pro Azure MFA v cloudu a je prémiovou funkcí Azure AD. Další informace o této metodě najdete v [nasadit cloudové Azure Multi-Factor Authentication](howto-mfa-getstarted.md).

**Povolená ochranou identity Azure AD** – tato metoda používá zásady rizik a ochrany identity Azure AD k vyžadování dvoustupňového ověření založeného pouze na riziku přihlášení pro všechny cloudové aplikace. Tato metoda vyžaduje licencování Služby Azure Active Directory P2. Další informace o této metodě najdete v [Azure Active Directory Identity Protection](../identity-protection/howto-sign-in-risk-policy.md)

> [!Note]
> Další informace o licencích a cenách najdete na cenových stránkách [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/
) a [Multi-Factor Authentication.](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

## <a name="enable-azure-mfa-by-changing-user-state"></a>Povolení Azure MFA změnou stavu uživatele

Uživatelské účty v Azure Multi-Factor Authentication mají následující tři odlišné stavy:

> [!IMPORTANT]
> Povolení Azure MFA prostřednictvím zásady podmíněného přístupu nezmění stav uživatele. Nebuďte znepokojeni uživatelé se objeví zakázáno. Podmíněný přístup nezmění stav. **Organizace by neměly povolit nebo vynucovat uživatele, pokud používají zásady podmíněného přístupu.**

| Status | Popis | Ovlivněné aplikace, které nejsou ovlivněny prohlížečem | Ovlivněné aplikace prohlížeče | Ovlivněno moderní ověřování |
|:---:| --- |:---:|:--:|:--:|
| Zakázáno | Výchozí stav pro nového uživatele, který není zapsán v Azure MFA. | Ne | Ne | Ne |
| Povoleno | Uživatel byl zapsán do Azure MFA, ale nezaregistroval. Při příštím přihlášení obdrží výzvu k registraci. | Ne.  Pokračují v práci, dokud není dokončen proces registrace. | Ano. Po vypršení platnosti relace je vyžadována registrace Azure MFA.| Ano. Po vypršení platnosti přístupového tokenu je vyžadována registrace Azure MFA. |
| Vynuceno | Uživatel byl zaregistrován a dokončil proces registrace pro Azure MFA. | Ano. Aplikace vyžadují hesla aplikací. | Ano. Azure MFA je vyžadováno při přihlášení. | Ano. Azure MFA je vyžadováno při přihlášení. |

Stav uživatele odráží, zda je správce zaregistroval do Azure MFA a zda dokončil proces registrace.

Všichni uživatelé spustit *zakázáno*. Když zaregistrujete uživatele do Azure MFA, jejich stav se změní na *Povoleno*. Pokud je povoleno, uživatelé se přihlašují a dokončí proces registrace, jejich stav se změní na *Vynuceno*.

> [!NOTE]
> Pokud je vícefaktorové ověřování znovu povoleno u objektu uživatele, který už má podrobnosti o registraci, jako je například telefon nebo e-mail, pak správci musí mít tento uživatel znovu zaregistrovat Vícefaktorové ověřování prostřednictvím portálu Azure nebo PowerShellu. Pokud se uživatel znovu nezaregistruje, jeho stav MFA nepřechází z *Povoleno* na *Vynuceno* v uživatelském rozhraní správy mfa.

### <a name="view-the-status-for-a-user"></a>Zobrazení stavu uživatele

Pomocí následujících kroků přejdete na stránku, kde můžete zobrazit a spravovat stavy uživatelů:

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako správce.
2. Vyhledejte a vyberte *Azure Active Directory*. Vyberte **možnost Uživatelé** > **Všichni uživatelé**.
3. Vyberte **vícefaktorové ověřování**. Chcete-li zobrazit tuto možnost nabídky, může být nutné posunout doprava. Výběrem ukázkového snímku obrazovky níže zobrazíte celé okno portálu Azure a umístění nabídky:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Vyberte vícefaktorové ověřování z okna Uživatelé ve službě Azure AD.")](media/howto-mfa-userstates/selectmfa.png#lightbox)
4. Otevře se nová stránka, která zobrazuje stavy uživatele.
   ![stav uživatele s vícefaktorovým ověřováním – snímek obrazovky](./media/howto-mfa-userstates/userstate1.png)

### <a name="change-the-status-for-a-user"></a>Změna stavu uživatele

1. Pomocí předchozích kroků se dostanete na stránku **uživatelů** azure vícefaktorového ověřování.
2. Najděte uživatele, kterého chcete povolit pro Azure MFA. Možná budete muset změnit zobrazení v horní části.
   ![Vyberte uživatele, pro který chcete změnit stav na kartě Uživatelé.](./media/howto-mfa-userstates/enable1.png)
3. Zaškrtněte políčko vedle jejich jména.
4. Vpravo v **rychlých krocích**zvolte **Povolit** nebo **Zakázat**.
   ![Povolení vybraného uživatele kliknutím na povolit v nabídce Rychlých kroků](./media/howto-mfa-userstates/user1.png)

   > [!TIP]
   > *Oprávnění* uživatelé se automaticky přepnou na *Vynuceno* při registraci pro Azure MFA. Neměňte ručně stav uživatele na *Vynuceno*.

5. Potvrďte výběr v rozbalovacím okně, které se otevře.

Po povolení uživatelů, upozorněte je e-mailem. Řekněte jim, že budou požádáni o registraci při příštím přihlášení. Pokud vaše organizace používá aplikace, které nepodporují moderní ověřování, musí také vytvářet hesla aplikací. Můžete také zahrnout odkaz na [příručku pro koncovéuživatele Azure MFA,](../user-help/multi-factor-authentication-end-user.md) která jim pomůže začít.

### <a name="use-powershell"></a>Použití prostředí PowerShell

Chcete-li změnit stav uživatele pomocí prostředí `$st.State`Azure [AD PowerShell](/powershell/azure/overview), změňte . Existují tři možné stavy:

* Povoleno
* Vynuceno
* Zakázáno  

Nepřesouvejte uživatele přímo do stavu *Vynuceno.* Pokud tak učiníte, aplikace, které nejsou založeny na prohlížeči, přestanou fungovat, protože uživatel neprošel registrací Azure MFA a nezískal [heslo aplikace](howto-mfa-mfasettings.md#app-passwords).

Nejprve nainstalujte modul pomocí:

   ```PowerShell
   Install-Module MSOnline
   ```

> [!TIP]
> Nezapomeňte se nejprve připojit pomocí **služby Connect-MsolService**

   ```PowerShell
   Connect-MsolService
   ```

Tento příklad skriptu prostředí PowerShell umožňuje vícefaktorové ověřování pro jednotlivé uživatele:

   ```PowerShell
   Import-Module MSOnline
   Connect-MsolService
   $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
   $st.RelyingParty = "*"
   $st.State = "Enabled"
   $sta = @($st)
   Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta
   ```

Použití prostředí PowerShell je dobrá volba, když potřebujete hromadně povolit uživatele. Jako příklad následující skript smyčky prostřednictvím seznamu uživatelů a umožňuje vícefaktorové informace o jejich účtech:

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

Chcete-li zakázat vícefaktorové finanční služby, použijte tento skript:

   ```PowerShell
   Get-MsolUser -UserPrincipalName user@domain.com | Set-MsolUser -StrongAuthenticationRequirements @()
   ```

které lze také zkrátit na:

   ```PowerShell
   Set-MsolUser -UserPrincipalName user@domain.com -StrongAuthenticationRequirements @()
   ```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Převod uživatelů z vícefaktorové ověřování pro jednotlivé uživatele na vícefaktorové ověřování založené na podmíněném přístupu

Následující Prostředí PowerShell vám může pomoci při převodu na azure multifaktorové ověřování založené na podmíněném přístupu.

Spusťte toto prostředí PowerShell v okně ise nebo jej uložte jako . PS1 pro spuštění místně.

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
> Nedávno jsme změnili chování a skript prostředí PowerShell výše odpovídajícím způsobem. Dříve skript uložen mimo metody MFA, zakázal vícefaktorové financování a obnovil metody. To již není nutné nyní, že výchozí chování pro zakázat nevymaže metody.
>
> Pokud je vícefaktorové ověřování znovu povoleno u objektu uživatele, který už má podrobnosti o registraci, jako je například telefon nebo e-mail, pak správci musí mít tento uživatel znovu zaregistrovat Vícefaktorové ověřování prostřednictvím portálu Azure nebo PowerShellu. Pokud se uživatel znovu nezaregistruje, jeho stav MFA nepřechází z *Povoleno* na *Vynuceno* v uživatelském rozhraní správy mfa.

## <a name="next-steps"></a>Další kroky

* Proč byl uživatel vyzván nebo nebyl vyzván k provedení vícefaktorového ověřování? Podívejte se na část [sestavy přihlášení Azure AD v dokumentu Sestavy v Azure vícefaktorové ověřování](howto-mfa-reporting.md#azure-ad-sign-ins-report).
* Pokud chcete nakonfigurovat další nastavení, jako jsou důvěryhodné IP adresy, vlastní hlasové zprávy a upozornění na podvody, přečtěte si článek [Konfigurace nastavení azure multifaktorového ověřování](howto-mfa-mfasettings.md)
* Informace o správě uživatelských nastavení pro vícefaktorové ověřování Azure najdete v článku [Správa uživatelských nastavení pomocí azure multifaktorového ověřování v cloudu](howto-mfa-userdevicesettings.md)
