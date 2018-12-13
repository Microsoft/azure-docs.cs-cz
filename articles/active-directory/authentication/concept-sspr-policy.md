---
title: Zásady pro resetování hesel Azure AD samoobslužný
description: Konfigurace možností zásady resetování hesla pomocí samoobslužné služby Azure AD
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 3a16977f2d3bc7321de2d2a1538276f182e7fa10
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53190982"
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Zásady hesel a omezení v Azure Active Directory

Tento článek popisuje zásady pro hesla a požadavky na složitost spojené s uživatelskými účty, které jsou uložené ve vašem tenantovi Azure Active Directory (Azure AD).

## <a name="administrator-reset-policy-differences"></a>Správce resetovat zásady rozdíly

**Microsoft vynucuje silné výchozí *dvě brány* zásady pro libovolnou roli Správce služby Azure pro resetování hesla** tato zásada může být jiné než ta, kterou jste definovali pro vaše uživatele a nedá se změnit. Vždy byste měli otestovat funkci resetování hesla jako uživatel bez jakékoli role Správce služby Azure, které jsou přiřazeny.

Pomocí zásad brány dvě **správci nemají možnost použít bezpečnostní otázky**.

Zásada dvě brány vyžaduje dva druhy ověřovacích dat, například **e-mailová adresa**, **aplikace authenticator**, nebo **telefonní číslo**. Dvě brány zásady platí za následujících okolností:

* Všechny následující role Správce služby Azure jsou ovlivněny:
  * Správce technické podpory
  * Správce podpory služeb
  * Správce fakturace
  * Podpora partnerů úrovně 1
  * Podpora partnerů úrovně 2
  * Správce služby Exchange
  * Správce služby Lync
  * Správce uživatelských účtů
  * Uživatelé zapisující do adresáře
  * Globální správce ani správce společnosti
  * Správce služeb Sharepointu
  * Správce dodržování předpisů
  * Správce aplikace
  * Správce zabezpečení
  * Správce privilegovaných rolí
  * Správce služby Microsoft Intune
  * Správce služby proxy aplikace
  * Správce služby CRM
  * Správce služby Power BI

* Pokud ve zkušební verzi předplatného; po uplynutí 30 dnů nebo
* Individuální doména je k dispozici, třeba contoso.com; nebo
* Azure AD Connect je synchronizaci identit z místního adresáře

### <a name="exceptions"></a>Výjimky

Zásada jedna brána vyžaduje jednu část ověření dat, jako jsou e-mailovou adresu *nebo* telefonní číslo. Jedna brána zásady platí za následujících okolností:

* Je během prvních 30 dní zkušební předplatné; nebo
* Není přítomna individuální doména (*. onmicrosoft.com); a
* Azure AD Connect nesynchronizuje identity

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>UserPrincipalName zásady, které platí pro všechny uživatelské účty

Každý uživatelský účet, který je potřeba se přihlásit ke službě Azure AD musí mít hodnotu atributu jedinečného uživatelského hlavní název (UPN) přidružené k účtu. Následující tabulka popisuje zásady, které platí i v místním Active Directory uživatelské účty, které jsou synchronizovány do cloudu a uživatelské účty jenom cloudu:

| Vlastnost | Požadavky na atribut UserPrincipalName |
| --- | --- |
| Povolený počet znaků |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Znaky nejsou povoleny |<ul> <li>Žádné "\@ \" znak, který není oddělení uživatelského jména z domény.</li> <li>Nesmí obsahovat znak tečky "." bezprostředně předcházející "\@ \" symbol</li></ul> |
| Omezení délky |<ul> <li>Celková délka nesmí přesáhnout 113 znaků</li><li>Může obsahovat až 64 znaků před "\@ \" symbol</li><li>Může být až 48 znaků po "\@ \" symbol</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Zásady pro hesla, které se vztahují jenom na cloudové uživatelské účty

Následující tabulka popisuje nastavení zásad k dispozici heslo, které lze použít u uživatelských účtů, které jsou vytvořeny a spravovat v Azure AD:

| Vlastnost | Požadavky |
| --- | --- |
| Povolený počet znaků |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ " ( ) ;</li></ul> |
| Znaky nejsou povoleny |<ul><li>Znaky Unicode.</li><li>Mezery.</li><li> Pouze silná hesla</li></ul> |
| Omezení pro heslo |<ul><li>Minimálně 8 znaků a maximálně 16 znaků.</li><li>Pouze silná hesla: Vyžaduje tři ze čtyř z následujících akcí:<ul><li>Malá písmena.</li><li>Velká písmena.</li><li>Číslice (0 – 9).</li><li>Symboly (viz předchozí omezení pro heslo).</li></ul></li></ul> |
| Doba vypršení platnosti hesla |<ul><li>Výchozí hodnota: **90** dnů.</li><li>Hodnota je konfigurovatelná pomocí `Set-MsolPasswordPolicy` rutiny z Active Directory modulu Azure pro Windows PowerShell.</li></ul> |
| Oznámení o vypršení platnosti hesla |<ul><li>Výchozí hodnota: **14** (před vypršením platnosti hesla).</li><li>Hodnota je konfigurovatelná pomocí `Set-MsolPasswordPolicy` rutiny.</li></ul> |
| Vypršení platnosti hesla |<ul><li>Výchozí hodnota: **false** dnů (to znamená, že vypršení platnosti hesla je povoleno).</li><li>Hodnotu lze nastavit pro jednotlivé uživatelské účty pomocí `Set-MsolUser` rutiny.</li></ul> |
| Historie změn hesel |Poslední heslo *nelze* znovu použít, když uživatel změní heslo. |
| Historie pro resetování hesla | Poslední heslo *můžete* znovu použít, když uživatel může resetovat zapomenuté heslo. |
| Uzamčení účtu |Po 10 neúspěšných pokusů o přihlášení pomocí nesprávné heslo uživatel je uzamčen pro jednominutovým. Další nesprávné pokusy o přihlášení uzamčení uživatele pro zvýšení doby trvání čas. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Nastavení zásad vypršení platnosti hesel ve službě Azure AD

Globální správce pro cloudové služby Microsoftu můžete použít modul Microsoft Azure AD pro prostředí Windows PowerShell k nastavení hesla uživatelů se vypršení platnosti. Rutiny prostředí Windows PowerShell můžete použít taky k odebrání platné stále konfigurace nebo chcete-li zjistit, které uživatel hesla se nastavilo na nekonečnou platnost. 

Tento návod se vztahuje na jiných poskytovatelů, jako je Intune a Office 365, které také závisí na službě Azure AD pro identitu a adresářové služby. Vypršení platnosti hesla je jediná součást zásadami, které je možné změnit.

> [!NOTE]
> Jenom hesla pro uživatelské účty, které nejsou synchronizovány prostřednictvím synchronizace adresářů je možné nakonfigurovat na nevyprší platnost. Další informace o synchronizaci adresářů, naleznete v tématu [AD se službou Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).
>

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Nastavte nebo zkontrolujte zásady pro hesla pomocí prostředí PowerShell

Chcete-li začít, je potřeba [stáhněte a nainstalujte modul Azure AD PowerShell](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). Po jeho instalaci, můžete použít následující postup ke konfiguraci jednotlivých polí.

### <a name="check-the-expiration-policy-for-a-password"></a>Zkontrolujte, že zásada vypršení platnosti hesla

1. Připojení k prostředí Windows PowerShell s použitím svých přihlašovacích údajů správce společnosti.
1. Spustí jednu z následujících příkazů:

   * Pokud chcete zobrazit, pokud jeden uživatel heslo se nastavilo na nekonečnou platnost, spusťte následující rutinu pomocí hlavního názvu uživatele (například *aprilr@contoso.onmicrosoft.com*) nebo ID uživatele, kterého chcete zkontrolovat: `Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}`
   * Pokud chcete zobrazit **platnost hesla nikdy nevyprší** nastavení pro všechny uživatele, spusťte následující rutinu: `Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}`

### <a name="set-a-password-to-expire"></a>Nastavení vypršení platnosti hesla

1. Připojení k prostředí Windows PowerShell s použitím svých přihlašovacích údajů správce společnosti.
1. Spustí jednu z následujících příkazů:

   * Chcete-li nastavit heslo s jedním uživatelem, tak, aby platnost hesla vyprší, spusťte následující rutinu s použitím hlavní název uživatele nebo ID uživatele uživatele: `Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None`
   * Pokud chcete nastavit hesel všech uživatelů v organizaci tak, aby platnost souhlasu vyprší, použijte následující rutinu: `Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None`

### <a name="set-a-password-to-never-expire"></a>Nastavení hesla nikdy nevyprší

1. Připojení k prostředí Windows PowerShell s použitím svých přihlašovacích údajů správce společnosti.
1. Spustí jednu z následujících příkazů:

   * Pokud chcete nastavit heslo jednoho uživatele bez vypršení platnosti, spusťte následující rutinu s použitím hlavní název uživatele nebo ID uživatele uživatele: `Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration`
   * Chcete-li nastavit hesel všech uživatelů v organizaci bez vypršení platnosti, spusťte následující rutinu: `Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration`

   > [!WARNING]
   > Hesla nastavit na `-PasswordPolicies DisablePasswordExpiration` stále stáří na základě `pwdLastSet` atribut. Pokud nastavujete uživatelských hesel bez vypršení platnosti a pak přejít 90 dnů, platnost hesla. Na základě `pwdLastSet` atribut, pokud změníte vypršení platnosti na `-PasswordPolicies None`, všechna hesla, které mají `pwdLastSet` starší než 90 dny vyžadovat, aby je změnit při dalším přihlášení. Tato změna může ovlivnit velký počet uživatelů. 

## <a name="next-steps"></a>Další postup

Následující články poskytují další informace o resetování hesla přes Azure AD:

* [Jak dokončit úspěšné zavedení SSPR?](howto-sspr-deployment.md)
* [Resetování nebo změna hesla](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrace samoobslužného resetování hesla](../user-help/active-directory-passwords-reset-register.md)
* [Máte dotaz k licencování?](concept-sspr-licensing.md)
* [Jaká data používá SSPR a která data byste měli naplnit pro vaše uživatele?](howto-sspr-authenticationdata.md)
* [Které metody ověřování jsou dostupné pro uživatele?](concept-sspr-howitworks.md#authentication-methods)
* [Co je zpětný zápis hesla a proč byste se o něj měli starat?](howto-sspr-writeback.md)
* [Jak hlásit aktivitu v SSPR?](howto-sspr-reporting.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Myslím, že je něco poškozené. Jak řešit problémy SSPR?](active-directory-passwords-troubleshoot.md)
* [Mám otázku, která není zodpovězená jinde](active-directory-passwords-faq.md)
