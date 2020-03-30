---
title: Samoobslužné zásady pro resetování hesla – Azure Active Directory
description: Informace o různých možnostech samoobslužných zásad obnovení hesla služby Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: fba4dae66b5adcea6cc33e61d8cf88946e29546e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80051174"
---
# <a name="self-service-password-reset-policies-and-restrictions-in-azure-active-directory"></a>Samoobslužné zásady a omezení pro resetování hesla ve službě Azure Active Directory

Tento článek popisuje zásady hesel a požadavky na složitost přidružené k uživatelským účtům v tenantovi služby Azure Active Directory (Azure AD).

## <a name="administrator-reset-policy-differences"></a>Rozdílné zásady resetování hesel pro správce

**Microsoft vynucuje silné výchozí zásady pro resetování hesla *se dvěma branami* pro jakoukoli roli správce Azure**. Tato zásada se může lišit od zásady, kterou jste definovali pro uživatele, a tuto zásadu nelze změnit. Vždy byste měli otestovat funkci resetování hesla jako uživatel bez přiřazených rolí správce Azure.

Díky dvou-brány **zásady správci nemají možnost používat bezpečnostní otázky**.

Zásady dvou bran vyžadují dva části ověřovacích dat, například *e-mailovou adresu*, *ověřovací aplikaci*nebo *telefonní číslo*. Zásady dvou bran platí v následujících případech:

* Ovlivněny jsou všechny následující role správce Azure:
  * Správce technické podpory
  * Správce služeb
  * Správce fakturace
  * Podpora partnerské úrovně1
  * Podpora partnerské úrovně2
  * Správce Exchange
  * Správce Skypu pro firmy
  * Správce uživatele
  * Adresář spisovatelů
  * Globální správce nebo správce společnosti
  * Správce SharePointu
  * Správce dodržování předpisů
  * Správce aplikace
  * Správce zabezpečení
  * Správce privilegovaných rolí
  * Správce Intune
  * Správce služby proxy aplikace
  * Správce dynamics 365
  * Správce služby Power BI
  * Správce ověřování
  * Správce privilegovaného ověřování

* Pokud ve zkušebním předplatném uplynulo 30 dní; Nebo
* Vlastní doména byla nakonfigurovaná pro klienta Azure AD, například *contoso.com*; Nebo
* Azure AD Connect synchronizuje identity z místního adresáře

### <a name="exceptions"></a>Výjimky

Zásady jedné brány vyžadují jednu část ověřovacích dat, například e-mailovou adresu nebo telefonní číslo. Zásady jedné brány platí za následujících okolností:

* Je to během prvních 30 dnů zkušebního předplatného; Nebo
* Vlastní doména nebyla nakonfigurovaná pro vašeho klienta Azure AD, takže používá výchozí **.onmicrosoft.com*. Výchozí **.onmicrosoft.com* doména se nedoporučuje pro produkční použití; A
* Azure AD Connect nesynchronizuje identity

## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>Zásady UserPrincipalName, které platí pro všechny uživatelské účty

Každý uživatelský účet, který se musí přihlásit ke službě Azure AD, musí mít k účtu přidruženou hodnotu atributu jedinečného hlavního uživatelského jména (UPN). V následující tabulce jsou uvedeny zásady, které platí pro místní uživatelské účty služby Active Directory Domain Services, které jsou synchronizovány s cloudem, i pro uživatelské účty pouze v cloudu:

| Vlastnost | Požadavky UserPrincipalName |
| --- | --- |
| Povolené znaky |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> ' \. - \_ ! \# ^ \~</li></ul> |
| Znaky nejsou povoleny. |<ul> <li>Libovolný\@ \" znak ", který neodděluje uživatelské jméno od domény.</li> <li>Nesmí obsahovat znak tečky "." bezprostředně\@ \" předcházející symbolu "</li></ul> |
| Omezení délky |<ul> <li>Celková délka nesmí přesáhnout 113 znaků.</li><li>Před symbolem "\@ \" může být až 64 znaků</li><li>Za symbolem "\@ \" může být až 48 znaků</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Zásady hesla, které platí jenom pro uživatelské účty v cloudu

Následující tabulka popisuje nastavení zásad hesel u uživatelských účtů, které jsou vytvořené a spravované ve službě Azure AD:

| Vlastnost | Požadavky |
| --- | --- |
| Povolené znaky |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { { &#124; \ : ' , . ? / \`~ " ( ) ;</li> <li>prázdné místo</li></ul> |
| Znaky nejsou povoleny. | Znaky Unicode. |
| Omezení hesla |<ul><li>Minimálně 8 znaků a maximálně 256 znaků.</li><li>Vyžaduje tři ze čtyř z následujících:<ul><li>Malá písmena.</li><li>Velká písmena.</li><li>(0-9).</li><li>Symboly (viz předchozí omezení hesla).</li></ul></li></ul> |
| Doba platnosti hesla (maximální stáří hesla) |<ul><li>Výchozí hodnota: **90** dní.</li><li>Hodnotu lze konfigurovat pomocí `Set-MsolPasswordPolicy` rutiny z modulu Azure Active Directory Module for Windows PowerShell.</li></ul> |
| Oznámení o vypršení platnosti hesla (Pokud jsou uživatelé upozorněni na vypršení platnosti hesla) |<ul><li>Výchozí hodnota: **14** dní (před vypršením platnosti hesla).</li><li>Hodnotu lze konfigurovat pomocí `Set-MsolPasswordPolicy` rutiny.</li></ul> |
| Vypršení platnosti hesla (Nechte hesla nikdy nevyprší) |<ul><li>Výchozí hodnota: **false** (označuje, že heslo má datum vypršení platnosti).</li><li>Hodnotu lze nakonfigurovat pro jednotlivé `Set-MsolUser` uživatelské účty pomocí rutiny.</li></ul> |
| Historie změn hesla | Poslední heslo *nelze* znovu použít, když uživatel změní heslo. |
| Historie resetování hesla | Poslední heslo *lze* znovu použít, když uživatel resetuje zapomenuté heslo. |
| Uzamčení účtu | Po 10 neúspěšných pokusech o přihlášení se špatným heslem je uživatel uzamčen po dobu jedné minuty. Další nesprávné pokusy o přihlášení uzamčení uživatele pro prodloužení doby trvání. [Inteligentní uzamčení](howto-password-smart-lockout.md) sleduje poslední tři chybné zapisování hesel, aby se zabránilo zvýšení čítače uzamčení pro stejné heslo. Pokud někdo zadá stejné chybné heslo vícekrát, toto chování nezpůsobí uzamčení účtu. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Nastavení zásad vypršení platnosti hesla ve službě Azure AD

*Globální správce* nebo *správce uživatelů* cloudové služby Microsoftu může pomocí *modulu Microsoft Azure AD module pro prostředí Windows PowerShell* nastavit, aby vypršela platnost uživatelských hesel. Pomocí rutin prostředí Windows PowerShell můžete také odebrat konfiguraci, která nikdy nevyprší, nebo zjistit, která uživatelská hesla jsou nastavena tak, aby nikdy nevypršela.

Tyto pokyny platí pro jiné poskytovatele, jako je Intune a Office 365, které také spoléhají na Azure AD pro identity a adresářové služby. Vypršení platnosti hesla je jedinou částí zásad, kterou lze změnit.

> [!NOTE]
> Pouze hesla pro uživatelské účty, které nejsou synchronizovány prostřednictvím synchronizace adresářů, mohou být nakonfigurována tak, aby nevypršela. Další informace o synchronizaci adresářů najdete [v tématu Připojení služby AD pomocí služby Azure AD](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Nastavení nebo kontrola zásad hesla pomocí PowerShellu

Chcete-li začít, [stáhněte a nainstalujte modul Azure AD PowerShell](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). Po instalaci modulu nakonfigurujte každé pole pomocí následujících kroků.

### <a name="check-the-expiration-policy-for-a-password"></a>Kontrola zásad vypršení platnosti pro heslo

1. Připojte se k prostředí Windows PowerShell pomocí pověření správce uživatele nebo správce společnosti.
1. Spusťte jeden z následujících příkazů:

   * Chcete-li zjistit, zda je heslo jednoho uživatele nastaveno na nikdy nevyprší, spusťte následující rutinu pomocí hlavního jména uživatele (například *\@aprilr contoso.onmicrosoft.com)* nebo ID uživatele, kterého chcete zkontrolovat:

   ```powershell
   Get-AzureADUser -ObjectId <user ID> | Select-Object @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

   * Chcete-li zobrazit nastavení **Heslo nikdy vyprší** pro všechny uživatele, spusťte následující rutinu:

   ```powershell
   Get-AzureADUser -All $true | Select-Object UserPrincipalName, @{N="PasswordNeverExpires";E={$_.PasswordPolicies -contains "DisablePasswordExpiration"}}
   ```

### <a name="set-a-password-to-expire"></a>Nastavení vypršení platnosti hesla

1. Připojte se k prostředí Windows PowerShell pomocí pověření správce uživatele nebo správce společnosti.
1. Proveďte jeden z následujících příkazů:

   * Chcete-li nastavit heslo jednoho uživatele tak, aby platnost hesla vypršela, spusťte pomocí hlavního jména uživatele nebo ID uživatele následující rutinu:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies None
   ```

   * Chcete-li nastavit hesla všech uživatelů v organizaci tak, aby jejich platnost vypršela, použijte následující rutinu:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies None
   ```

### <a name="set-a-password-to-never-expire"></a>Nastavení hesla, které nikdy nevyprší

1. Připojte se k prostředí Windows PowerShell pomocí pověření správce uživatele nebo správce společnosti.
1. Proveďte jeden z následujících příkazů:

   * Chcete-li nastavit heslo jednoho uživatele tak, aby nikdy nevypršelo, spusťte pomocí hlavního jména UŽIVATELE nebo ID uživatele následující rutinu:

   ```powershell
   Set-AzureADUser -ObjectId <user ID> -PasswordPolicies DisablePasswordExpiration
   ```

   * Chcete-li nastavit hesla všech uživatelů v organizaci tak, aby nikdy nevypršela, spusťte následující rutinu:

   ```powershell
   Get-AzureADUser -All $true | Set-AzureADUser -PasswordPolicies DisablePasswordExpiration
   ```

   > [!WARNING]
   > Hesla nastavená `-PasswordPolicies DisablePasswordExpiration` na stále `pwdLastSet` věk na základě atributu. Na základě `pwdLastSet` atributu, pokud změníte vypršení platnosti `-PasswordPolicies None` `pwdLastSet` na , všechna hesla, která mají starší než 90 dní, vyžadují, aby je uživatel při příštím přihlášení změnil. Tato změna může ovlivnit velký počet uživatelů.

## <a name="next-steps"></a>Další kroky

Následující články poskytují další informace o resetování hesla prostřednictvím služby Azure AD:

* [Jak dokončit úspěšné zavedení SSPR?](howto-sspr-deployment.md)
* [Resetování nebo změna hesla](../user-help/active-directory-passwords-update-your-own-password.md)
* [Zaregistrujte se pro samoobslužné resetování hesla](../user-help/active-directory-passwords-reset-register.md).
* [Máte dotaz k licencování?](concept-sspr-licensing.md)
* [Jaká data používá SSPR a jaká data byste měli naplnit pro vaše uživatele?](howto-sspr-authenticationdata.md)
* [Které metody ověřování jsou dostupné pro uživatele?](concept-sspr-howitworks.md#authentication-methods)
* [Co je zpětný zápis hesla a proč byste se o něj měli starat?](howto-sspr-writeback.md)
* [Jak hlásit aktivitu v SSPR?](howto-sspr-reporting.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Myslím, že je něco rozbité. Jak lze vyřešit probléms sspr?](active-directory-passwords-troubleshoot.md)
* [Mám otázku, která není zodpovězená jinde](active-directory-passwords-faq.md)
