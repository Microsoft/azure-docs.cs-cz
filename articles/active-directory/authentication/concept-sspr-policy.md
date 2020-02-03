---
title: Zásady samoobslužného resetování hesla – Azure Active Directory
description: Konfigurace možností zásad samoobslužného resetování hesel služby Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd6cacae9c7af705b0de7b59e0f25f25637a5a89
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2020
ms.locfileid: "76962488"
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Zásady a omezení hesel v Azure Active Directory

Tento článek popisuje zásady hesel a požadavky na složitost spojené s uživatelskými účty v tenantovi Azure Active Directory (Azure AD).

## <a name="administrator-reset-policy-differences"></a>Správce vynulovat rozdíly zásad

**Microsoft vynutil silné výchozí zásady resetování hesel pro všechny role správce Azure** . Tato zásada se může lišit od toho, kterou jste definovali pro vaše uživatele, a nejde je změnit. Funkce resetování hesla byste měli vždycky testovat jako uživatel bez přiřazených rolí správce Azure.

Díky zásadám se dvěma branami nemají **správci možnost používat bezpečnostní otázky**.

Zásady dvou bran vyžadují dvě části ověřovacích dat, jako je **e-mailová adresa**, **ověřovací aplikace**nebo **telefonní číslo**. Zásady dvou bran se uplatní v následujících případech:

* Ovlivněny jsou všechny následující role správce Azure:
  * Správce helpdesku
  * Správce služby Service support
  * Správce fakturace
  * Podpora partnerů úrovně 1
  * Podpora partnerů úrovně 2
  * Správce Exchange
  * Správce Skypu pro firmy
  * Správce uživatele
  * Zapisovače adresářů
  * Globální správce nebo správce společnosti
  * Správce SharePointu
  * Správce dodržování předpisů
  * Správce aplikace
  * Správce zabezpečení
  * Správce privilegovaných rolí
  * Správce Intune
  * Správce služby proxy aplikací
  * Správce Dynamics 365
  * Správce služba Power BI
  * Správce ověřování
  * Správce privilegovaného ověřování

* Pokud uplyne 30 dnů ve zkušebním předplatném, ani
* Pro vašeho tenanta Azure AD je nakonfigurovaná vlastní doména, třeba *contoso.com*; ani
* Azure AD Connect synchronizuje identity z místního adresáře.

### <a name="exceptions"></a>Výjimky

Zásada s jedním bránou vyžaduje jedno z ověřovacích dat, jako je e-mailová adresa *nebo* telefonní číslo. Zásady s jednou bránou se uplatní v následujících případech:

* Je v průběhu prvních 30 dnů zkušebního předplatného; ani
* Vlastní doména není nakonfigurovaná pro vašeho tenanta Azure AD, takže používá výchozí * *. onmicrosoft.com*. Všimněte si, že výchozí doména * *. onmicrosoft.com* se nedoporučuje pro použití v produkčním prostředí; ani
* Azure AD Connect nesynchronizují identity

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>UserPrincipalName – zásady, které se vztahují na všechny uživatelské účty

Každý uživatelský účet, který musí být přihlášený k Azure AD, musí mít přidruženou hodnotu atributu hlavní název uživatele (UPN), která je přidružená ke svému účtu. Následující tabulka popisuje zásady, které platí pro místní uživatelské účty služby Active Directory, které jsou synchronizované s cloudem a jenom pro cloudové uživatelské účty:

| Vlastnost | Požadavky UserPrincipalName |
| --- | --- |
| Povolené znaky |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Nepovolené znaky |<ul> <li>Libovolný "\@\" znak, který odděluje uživatelské jméno z domény.</li> <li>Nemůže obsahovat znak tečky "." hned před symbolem "\@\"</li></ul> |
| Omezení délky |<ul> <li>Celková délka nesmí překročit 113 znaků.</li><li>Před symbolem\@\" může být až 64 znaků.</li><li>Za symbolem\@\" může být až 48 znaků.</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Zásady hesel, které platí jenom pro cloudové uživatelské účty

Následující tabulka popisuje nastavení zásad hesel, která se používají u uživatelských účtů vytvořených a spravovaných v Azure AD:

| Vlastnost | Požadavky |
| --- | --- |
| Povolené znaky |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / \` ~ "();</li> <li>prázdné místo</li></ul> |
| Nepovolené znaky | Znaky Unicode. |
| Omezení hesla |<ul><li>Minimálně 8 znaků a maximálně 256 znaků.</li><li>Vyžaduje tři ze čtyř z následujících možností:<ul><li>Malými písmeny.</li><li>Velká písmena.</li><li>Čísla (0-9).</li><li>Symboly (viz předchozí omezení hesla).</li></ul></li></ul> |
| Doba platnosti hesla vypršela (maximální stáří hesla) |<ul><li>Výchozí hodnota: **90** dní.</li><li>Hodnotu lze konfigurovat pomocí rutiny `Set-MsolPasswordPolicy` z modulu Azure Active Directory pro prostředí Windows PowerShell.</li></ul> |
| Oznámení o vypršení platnosti hesla (když se uživatelům oznámí vypršení platnosti hesla) |<ul><li>Výchozí hodnota: **14** dní (před vypršením platnosti hesla).</li><li>Hodnotu lze konfigurovat pomocí rutiny `Set-MsolPasswordPolicy`.</li></ul> |
| Vypršení platnosti hesla (nechat vypršení platnosti hesla) |<ul><li>Výchozí hodnota: **false** (označuje, že heslo má datum vypršení platnosti).</li><li>Hodnotu lze nakonfigurovat pro jednotlivé uživatelské účty pomocí rutiny `Set-MsolUser`.</li></ul> |
| Historie změn hesel | Poslední heslo *nelze* znovu použít, když uživatel změní heslo. |
| Historie resetování hesla | Poslední heslo *lze* znovu použít, když uživatel resetuje zapomenuté heslo. |
| Uzamčení účtu | Po 10 neúspěšných pokusech o přihlášení k nesprávnému heslu je uživatel zablokován na jednu minutu. Další nesprávné pokusy o přihlášení zablokují uživateli zvýšení doby trvání. [Inteligentní uzamykání](howto-password-smart-lockout.md) sleduje poslední tři chybné hodnoty hash hesla, aby nedošlo k vyššímu zvýšení čítače uzamčení pro stejné heslo. Pokud někdo několikrát zadá stejné chybné heslo, toto chování nebude mít za následek uzamčení účtu. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Nastavení zásad vypršení platnosti hesla ve službě Azure AD

Globální správce nebo Správce uživatelů cloudové služby Microsoftu může použít Modul Microsoft Azure AD pro Windows PowerShell k nastavení hesla uživatele, která nebrzy vyprší. Pomocí rutin prostředí Windows PowerShell můžete také odebrat konfiguraci pro nikdy nevypršení platnosti nebo zjistit, která uživatelská hesla jsou nastavena na hodnotu nikdy nevyprší. 

Tento návod se vztahuje na jiné poskytovatele, jako je Intune a Office 365, které se taky spoléhají na Azure AD pro identity a adresářové služby. Vypršení platnosti hesla je jediná součást zásad, kterou je možné změnit.

> [!NOTE]
> Jenom hesla pro uživatelské účty, která nejsou synchronizovaná prostřednictvím synchronizace adresářů, se dají nakonfigurovat tak, aby nevypršela platnost. Další informace o synchronizaci adresářů najdete v tématu [připojení AD pomocí Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Nastavení nebo ověření zásad hesel pomocí prostředí PowerShell

Abyste mohli začít, musíte [si stáhnout a nainstalovat modul Azure AD PowerShell](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). Po instalaci nástroje můžete pomocí následujících kroků nakonfigurovat jednotlivá pole.

### <a name="check-the-expiration-policy-for-a-password"></a>Podívejte se na zásady vypršení platnosti hesla.

1. Připojte se k Windows PowerShellu pomocí přihlašovacích údajů správce uživatele nebo správce společnosti.
1. Spusťte jeden z následujících příkazů:

   * Chcete-li zjistit, zda je uživatelské heslo pro jednoho uživatele nastaveno na hodnotu nikdy nevyprší, spusťte následující rutinu pomocí hlavního názvu uživatele (například *\@' duben contoso.onmicrosoft.com*) nebo ID uživatele, kterého chcete ověřit:

   ```powershell
   Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

   * Pokud chcete zobrazit nastavení heslo pro všechny uživatele bez **vypršení platnosti** , spusťte následující rutinu:

   ```powershell
   Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

### <a name="set-a-password-to-expire"></a>Nastavit vypršení platnosti hesla

1. Připojte se k Windows PowerShellu pomocí přihlašovacích údajů správce uživatele nebo správce společnosti.
1. Spusťte jeden z následujících příkazů:

   * Pokud chcete nastavit heslo jednoho uživatele, aby platnost hesla vyprší, spusťte následující rutinu pomocí hlavního názvu uživatele (UPN) nebo ID uživatele:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
   ```

   * Pokud chcete nastavit hesla všech uživatelů v organizaci, aby vyprší platnost, použijte tuto rutinu:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
   ```

### <a name="set-a-password-to-never-expire"></a>Nastavit heslo na nikdy neplatnost

1. Připojte se k Windows PowerShellu pomocí přihlašovacích údajů správce uživatele nebo správce společnosti.
1. Spusťte jeden z následujících příkazů:

   * Chcete-li nastavit heslo pro jednoho uživatele na hodnotu nikdy nevyprší, spusťte následující rutinu pomocí hlavního názvu uživatele (UPN) nebo ID uživatele:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
   ```

   * Pokud chcete nastavit heslo pro všechny uživatele v organizaci, na který nikdy nevypršela platnost, spusťte následující rutinu:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
   ```

   > [!WARNING]
   > Hesla nastavená na `-PasswordPolicies DisablePasswordExpiration` mají stále stejný věk na základě `pwdLastSet` atributu. Pokud nastavíte heslo uživatele na hodnotu nikdy nevyprší a pak na 90 dní po, vyprší platnost hesla. Pokud v závislosti na atributu `pwdLastSet` změníte vypršení platnosti na `-PasswordPolicies None`, všechna hesla, která mají `pwdLastSet` starší než 90 dnů, vyžadují, aby ji uživatel změnil při příštím přihlášení. Tato změna může ovlivnit velký počet uživatelů.

## <a name="next-steps"></a>Další kroky

Následující články poskytují další informace o resetování hesla prostřednictvím služby Azure AD:

* [Jak dokončit úspěšné zavedení SSPR?](howto-sspr-deployment.md)
* [Resetování nebo změna hesla](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrace samoobslužného resetování hesla](../user-help/active-directory-passwords-reset-register.md)
* [Máte dotaz k licencování?](concept-sspr-licensing.md)
* [Jaká data používá SSPR a jaká data byste měli naplnit pro vaše uživatele?](howto-sspr-authenticationdata.md)
* [Které metody ověřování jsou dostupné pro uživatele?](concept-sspr-howitworks.md#authentication-methods)
* [Co je zpětný zápis hesla a proč byste se o něj měli starat?](howto-sspr-writeback.md)
* [Jak hlásit aktivitu v SSPR?](howto-sspr-reporting.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Myslím, že je něco přerušeno. Návody řešit potíže s SSPR?](active-directory-passwords-troubleshoot.md)
* [Mám otázku, která není zodpovězená jinde](active-directory-passwords-faq.md)
