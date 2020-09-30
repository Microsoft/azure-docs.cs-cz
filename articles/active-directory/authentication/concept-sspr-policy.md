---
title: Zásady samoobslužného resetování hesla – Azure Active Directory
description: Další informace o různých možnostech zásad samoobslužného resetování hesla Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/27/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 990d8ef275982b6d70c51819e47b33f543345023
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91531271"
---
# <a name="password-policies-and-account-restrictions-in-azure-active-directory"></a>Zásady hesel a omezení účtů v Azure Active Directory

V Azure Active Directory (Azure AD) existuje zásada hesla definující nastavení, jako je složitost hesla, délka nebo stáří. K dispozici jsou také zásady, které definují přijatelné znaky a délku pro uživatelská jména.

Pokud se pro změnu nebo resetování hesla ve službě Azure AD použije Samoobslužné resetování hesla (SSPR), zásady hesel se zkontrolují. Pokud heslo nesplňuje požadavky zásad, bude uživatel vyzván k jeho pokusu. Správci Azure mají určitá omezení používání SSPR, která se liší od běžných uživatelských účtů.

Tento článek popisuje nastavení zásad hesel a požadavky na složitost spojené s uživatelskými účty ve vašem tenantovi Azure AD a jak můžete pomocí PowerShellu kontrolovat nebo nastavovat nastavení vypršení platnosti hesla.

## <a name="username-policies"></a><a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Zásady uživatelského jména

Každý účet, který se přihlásí do služby Azure AD, musí mít přidruženou hodnotu atributu hlavní název uživatele (UPN), která je přidružená ke svému účtu. V hybridních prostředích s místním prostředím Active Directory Domain Services (služba AD DS) synchronizovaném s Azure AD pomocí Azure AD Connect ve výchozím nastavení je hlavní název uživatele (UPN) Azure AD nastavený na Prem UPN.

Následující tabulka popisuje zásady uživatelského jména, které se vztahují na místní služba AD DS účty, které jsou synchronizované s Azure AD, a jenom pro cloudové uživatelské účty vytvořené přímo ve službě Azure AD:

| Vlastnost | Požadavky UserPrincipalName |
| --- | --- |
| Povolené znaky |<ul> <li>A – Z</li> <li>a – z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Nepovolené znaky |<ul> <li>Libovolný \@ \" znak, který odděluje uživatelské jméno z domény.</li> <li>Nemůže obsahovat znak tečky "." hned před \@ \" symbolem "."</li></ul> |
| Omezení délky |<ul> <li>Celková délka nesmí překročit 113 znaků.</li><li>Před \@ symbolem může být až 64 znaků. \"</li><li>Za \@ symbolem může být až 48 znaků. \"</li></ul> |

## <a name="azure-ad-password-policies"></a><a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Zásady hesel Azure AD

Zásady hesel se aplikují na všechny uživatelské účty, které se vytváří a spravují přímo ve službě Azure AD. Tato zásada hesla se nedá upravit, i když můžete [nakonfigurovat vlastní zakázaná hesla pro ochranu heslem Azure AD](tutorial-configure-custom-password-protection.md).

Zásady hesla se nevztahují na uživatelské účty synchronizované z místního služba AD DS prostředí pomocí Azure AD Connect, pokud EnforceCloudPasswordPolicyForPasswordSyncedUsers nepovolíte.

Jsou definovány následující možnosti zásad hesla:

| Vlastnost | Požadavky |
| --- | --- |
| Povolené znaky |<ul><li>A – Z</li><li>a – z</li><li>0 – 9</li> <li>@ # $% ^ & *-_! + = [] {} &#124; \:,. ? / \` ~ " ( ) ;</li> <li>prázdné místo</li></ul> |
| Nepovolené znaky | Znaky Unicode. |
| Omezení hesla |<ul><li>Minimálně 8 znaků a maximálně 256 znaků.</li><li>Vyžaduje tři ze čtyř z následujících možností:<ul><li>Malými písmeny.</li><li>Velká písmena.</li><li>Čísla (0-9).</li><li>Symboly (viz předchozí omezení hesla).</li></ul></li></ul> |
| Doba platnosti hesla vypršela (maximální stáří hesla) |<ul><li>Výchozí hodnota: **90** dní.</li><li>Hodnota se dá nakonfigurovat pomocí `Set-MsolPasswordPolicy` rutiny z modulu Azure Active Directory pro Windows PowerShell.</li></ul> |
| Oznámení o vypršení platnosti hesla (když se uživatelům oznámí vypršení platnosti hesla) |<ul><li>Výchozí hodnota: **14** dní (před vypršením platnosti hesla).</li><li>Hodnotu lze konfigurovat pomocí `Set-MsolPasswordPolicy` rutiny.</li></ul> |
| Vypršení platnosti hesla (nechat hesla nikdy nevyprší) |<ul><li>Výchozí hodnota: **false** (označuje, že heslo má datum vypršení platnosti).</li><li>Hodnotu lze nakonfigurovat pro jednotlivé uživatelské účty pomocí `Set-MsolUser` rutiny.</li></ul> |
| Historie změn hesel | Poslední heslo *nelze* znovu použít, když uživatel změní heslo. |
| Historie resetování hesla | Poslední heslo *lze* znovu použít, když uživatel resetuje zapomenuté heslo. |
| Uzamčení účtu | Po 10 neúspěšných pokusech o přihlášení k nesprávnému heslu je uživatel zablokován na jednu minutu. Další nesprávné pokusy o přihlášení zablokují uživateli zvýšení doby trvání. [Inteligentní uzamykání](howto-password-smart-lockout.md) sleduje poslední tři chybné hodnoty hash hesla, aby nedošlo k vyššímu zvýšení čítače uzamčení pro stejné heslo. Pokud někdo několikrát zadá stejné chybné heslo, toto chování nezpůsobí, že účet nebude uzamčen. |

## <a name="administrator-reset-policy-differences"></a>Rozdílné zásady resetování hesel pro správce

Ve výchozím nastavení se pro Samoobslužné resetování hesla povolují účty správců a vynutila se silná výchozí zásada pro resetování hesel se *dvěma branami* . Tato zásada může být jiná než ta, kterou jste definovali pro uživatele, a tato zásada se nedá změnit. Funkce resetování hesla byste měli vždycky testovat jako uživatel bez přiřazených rolí správce Azure.

Díky zásadám se dvěma branami nemají správci možnost používat bezpečnostní otázky.

Zásady dvou bran vyžadují dvě části ověřovacích dat, jako je e-mailová adresa, ověřovací aplikace nebo telefonní číslo. Zásady dvou bran se uplatní v následujících případech:

* Ovlivněny jsou všechny následující role správce Azure:
  * Správce helpdesku
  * Správce služeb
  * Správce fakturace
  * Podpora partnerů Tier1
  * Podpora partnerů 2
  * Správce Exchange
  * Správce Skypu pro firmy
  * Správce uživatelů
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
  * Správce služby Power BI
  * Správce ověřování
  * Správce privilegovaného ověřování

* Pokud uplyne 30 dnů ve zkušebním předplatném, ani
* Pro vašeho tenanta Azure AD je nakonfigurovaná vlastní doména, třeba *contoso.com*; ani
* Azure AD Connect synchronizuje identity z místního adresáře.

Pomocí rutiny [set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) prostředí PowerShell můžete zakázat použití SSPR pro účty správců. `-SelfServePasswordResetEnabled $False`Parametr ZAKÁŽE SSPR správcům.

### <a name="exceptions"></a>Výjimky

Zásada s jedním bránou vyžaduje jedno z ověřovacích dat, jako je e-mailová adresa nebo telefonní číslo. Zásady s jednou bránou se uplatní v následujících případech:

* Je v průběhu prvních 30 dnů zkušebního předplatného; ani
* Vlastní doména není nakonfigurovaná pro vašeho tenanta Azure AD, takže používá výchozí **. onmicrosoft.com*. Výchozí doména **. onmicrosoft.com* se nedoporučuje pro použití v produkčním prostředí; ani
* Azure AD Connect nesynchronizují identity

## <a name="password-expiration-policies"></a><a name="set-password-expiration-policies-in-azure-ad"></a>Zásady vypršení platnosti hesla

*Globální správce* nebo *Správce uživatelů* může použít [modul Microsoft Azure AD pro Windows PowerShell](/powershell/module/Azuread/?view=azureadps-2.0) k nastavení hesla uživatele, která nebrzy vyprší.

Pomocí rutin prostředí PowerShell můžete také odebrat konfiguraci pro nikdy nevypršení platnosti nebo zjistit, která uživatelská hesla jsou nastavena na hodnotu nikdy nevyprší.

Tento návod se vztahuje na jiné poskytovatele, jako je Intune a Microsoft 365, které se také spoléhají na Azure AD pro identity a adresářové služby. Vypršení platnosti hesla je jediná součást zásad, kterou je možné změnit.

> [!NOTE]
> Jenom hesla pro uživatelské účty, které nejsou synchronizované prostřednictvím Azure AD Connect, se dají nakonfigurovat tak, aby nevypršela platnost. Další informace o synchronizaci adresářů najdete v tématu [připojení AD pomocí Azure AD](../hybrid/whatis-hybrid-identity.md).

### <a name="set-or-check-the-password-policies-by-using-powershell"></a>Nastavení nebo kontrola zásad hesla pomocí PowerShellu

Začněte tím, [že si stáhnete a nainstalujete modul Azure AD PowerShell](/powershell/module/Azuread/?view=azureadps-2.0) a [připojíte ho k vašemu tenantovi Azure AD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples).

Po instalaci modulu použijte následující postup k dokončení jednotlivých úkolů podle potřeby.

### <a name="check-the-expiration-policy-for-a-password"></a>Podívejte se na zásady vypršení platnosti hesla.

1. Otevřete příkazový řádek PowerShellu a [Připojte se k Tenantovi Azure AD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples) pomocí účtu *globálního správce* nebo *Správce uživatelů* .
1. Pro jednotlivé uživatele nebo všechny uživatele spusťte jeden z následujících příkazů:

   * Pokud chcete zjistit, jestli je u jednoho uživatele nastavené na nikdy vypršení platnosti hesla, spusťte následující rutinu. Nahraďte `<user ID>` ID uživatele, které chcete kontrolovat, například *driley \@ contoso.onmicrosoft.com*:

       ```powershell
       Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

   * Pokud chcete zobrazit nastavení heslo pro všechny uživatele bez **vypršení platnosti** , spusťte následující rutinu:

       ```powershell
       Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
       ```

### <a name="set-a-password-to-expire"></a>Nastavit vypršení platnosti hesla

1. Otevřete příkazový řádek PowerShellu a [Připojte se k Tenantovi Azure AD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples) pomocí účtu *globálního správce* nebo *Správce uživatelů* .
1. Pro jednotlivé uživatele nebo všechny uživatele spusťte jeden z následujících příkazů:

   * Pokud chcete nastavit heslo jednoho uživatele, aby platnost hesla vyprší, spusťte následující rutinu. Nahraďte `<user ID>` ID uživatele, které chcete kontrolovat, například *driley \@ contoso.onmicrosoft.com* .

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
       ```

   * Pokud chcete nastavit hesla všech uživatelů v organizaci, aby vyprší platnost, použijte tuto rutinu:

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
       ```

### <a name="set-a-password-to-never-expire"></a>Nastavit heslo na nikdy neplatnost

1. Otevřete příkazový řádek PowerShellu a [Připojte se k Tenantovi Azure AD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0#examples) pomocí účtu *globálního správce* nebo *Správce uživatelů* .
1. Pro jednotlivé uživatele nebo všechny uživatele spusťte jeden z následujících příkazů:

   * Pokud chcete nastavit heslo pro jednoho uživatele, který nikdy nevyprší, spusťte následující rutinu. Nahraďte `<user ID>` ID uživatele, které chcete kontrolovat, například *driley \@ contoso.onmicrosoft.com* .

       ```powershell
       Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
       ```

   * Pokud chcete nastavit heslo pro všechny uživatele v organizaci, na který nikdy nevypršela platnost, spusťte následující rutinu:

       ```powershell
       Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
       ```

   > [!WARNING]
   > Hesla jsou nastavena na `-PasswordPolicies DisablePasswordExpiration` stáří na základě `pwdLastSet` atributu. Pokud v závislosti na `pwdLastSet` atributu změníte vypršení platnosti `-PasswordPolicies None` , všechna hesla, která mají `pwdLastSet` starší než 90 dní, vyžadují, aby je uživatel změnil při příštím přihlášení. Tato změna může ovlivnit velký počet uživatelů.

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s SSPR, přečtěte si téma [kurz: povolení uživatelům odemknout svůj účet nebo resetovat hesla pomocí samoobslužného resetování hesla Azure Active Directory](tutorial-enable-sspr.md).

Pokud máte nebo uživatelé s SSPR problémy, přečtěte si téma [řešení potíží s samoobslužným resetováním hesla](active-directory-passwords-troubleshoot.md) .
