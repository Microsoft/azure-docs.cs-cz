---
title: Resetování hesla pomocí samoobslužné služby zásady – Azure Active Directory
description: Možnosti zásad resetování hesel samoobslužné služby Azure AD
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 5952d0b1568e2554f53797515cde5cb23ad95a9b
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="password-policies-and-restrictions-in-azure-active-directory"></a>Zásady hesel a omezení v Azure Active Directory

Tento článek popisuje zásady pro hesla a požadavky na složitost spojené s uživatelskými účty, které jsou uložené v klientovi služby Azure Active Directory (Azure AD).

## <a name="administrator-password-policy-differences"></a>Rozdíly zásady hesla správce

Microsoft vynucuje silné výchozí *dvě brány* zásady pro žádnou roli Správce služby Azure pro resetování hesla. 

Pomocí zásad dvě brány nemají správci umožňuje použít bezpečnostní otázky.

 Zásady brány dva vyžaduje dva kusy ověřovací data, jako je například e-mailovou adresu *a* telefonní číslo. Dvě brány zásady platí v následujících případech:

* Všechny následující role Správce služby Azure se vztahuje:
  * Správce technické podpory
  * Správce služeb
  * Správce fakturace
  * Podpora partnerů úrovně 1
  * Podpora partnerů úrovně 2
  * Správce služby Exchange
  * Správce služby Lync
  * Správce účtu uživatele
  * Uživatelé zapisující do adresáře
  * Globální správce nebo správce společnosti
  * Správce služby SharePoint
  * Správce dodržování předpisů
  * Správce aplikace
  * Správce zabezpečení
  * Správce privilegovaných rolí
  * Správce služby Microsoft Intune
  * Správce služby proxy aplikace
  * Správce služeb CRM
  * Správce služby Power BI
  
* Pokud ve zkušební verzi předplatného po uplynutí 30 dnů

  nebo

* Jednoduché domény je k dispozici, například contoso.com

  nebo

* Azure AD Connect je synchronizaci identit z vašeho místního adresáře

### <a name="exceptions"></a>Výjimky
Zásadu jedna brána vyžaduje jeden prvek ověřování dat, jako je například e-mailovou adresu *nebo* telefonní číslo. Jedna brána zásady platí v následujících případech:

* Je do 30 dnů zkušebního předplatného

  nebo

* Jednoduché domény není přítomen (*. onmicrosoft.com) 

  a 

  Azure AD Connect není synchronizaci identit


## <a name="userprincipalname-policies-that-apply-to-all-user-accounts"></a>UserPrincipalName zásady, které platí pro všechny uživatelské účty

Každý uživatelský účet, který se musí přihlásit do služby Azure AD musí mít hodnotu atributu jedinečného uživatelského hlavní název (UPN) přidružené k účtu. V následující tabulce jsou podrobněji popsány dále zásady, které platí pro obě místní služby Active Directory uživatelské účty, které jsou synchronizovány do cloudu a jenom pro cloud uživatelské účty:

| Vlastnost | UserPrincipalName požadavky |
| --- | --- |
| Povolené znaky |<ul> <li>A – Z</li> <li>a - z</li><li>0 – 9</li> <li> . - \_ ! \# ^ \~</li></ul> |
| Znaky nejsou povoleny |<ul> <li>Všechny "\@ \" znak, který není oddělení uživatelského jména z domény.</li> <li>Nesmí obsahovat období znak "." bezprostředně předcházející "\@ \" – symbol</li></ul> |
| Omezení délky |<ul> <li>Celková délka nesmí přesáhnout délku 113 znaků</li><li>Může být až 64 znaků, než "\@ \" – symbol</li><li>Může být maximálně 48 znaků po "\@ \" – symbol</li></ul> |

## <a name="password-policies-that-only-apply-to-cloud-user-accounts"></a>Zásady pro hesla, které se vztahují pouze na uživatelské účty cloudu

Následující tabulka popisuje nastavení zásad k dispozici heslo, které mohou být použity pro uživatelské účty, které jsou vytvořeny a spravované ve službě Azure AD:

| Vlastnost | Požadavky |
| --- | --- |
| Povolené znaky |<ul><li>A – Z</li><li>a - z</li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
| Znaky nejsou povoleny |<ul><li>Znaky kódování Unicode.</li><li>Prostory.</li><li> Pouze silná hesla</li></ul> |
| Omezení hesla |<ul><li>Minimálně 8 znaků a maximálně 16 znaků.</li><li>Silná hesla pouze: vyžaduje tři ze čtyř z následujících akcí:<ul><li>Malá písmena.</li><li>Velká písmena.</li><li>Čísla (0-9).</li><li>Symboly (viz předchozí omezení hesla).</li></ul></li></ul> |
| Doba vypršení platnosti hesla |<ul><li>Výchozí hodnota: **90** dnů.</li><li>Hodnota je možné konfigurovat pomocí `Set-MsolPasswordPolicy` rutiny z Azure Active Directory modul pro prostředí Windows PowerShell.</li></ul> |
| Oznámení o vypršení hesla |<ul><li>Výchozí hodnota: **14** (před vypršením platnosti hesla).</li><li>Hodnota je možné konfigurovat pomocí `Set-MsolPasswordPolicy` rutiny.</li></ul> |
| Vypršení platnosti hesla |<ul><li>Výchozí hodnota: **false** dní (to znamená, že vypršení platnosti hesla je povoleno).</li><li>Hodnotu lze nastavit pro jednotlivé uživatelské účty pomocí `Set-MsolUser` rutiny.</li></ul> |
| Historie změn hesla |Poslední heslo *nelze* znovu použít, když uživatel změní heslo. |
| Historie pro resetování hesla | Poslední heslo *můžete* znovu použít, pokud uživatel resetuje zapomenuté heslo. |
| Uzamčení účtu |Po 10 neúspěšných pokusech o přihlášení pomocí nesprávného hesla je uživatel uzamčen pro jednu minutu. Další nesprávný pokusy o přihlášení uzamčení uživatele pro zvýšení doby trvání intervalu. |

## <a name="set-password-expiration-policies-in-azure-ad"></a>Nastavení zásad vypršení platnosti hesla ve službě Azure AD

Globální správce pro cloudové služby Microsoftu můžete použít modul Microsoft Azure AD pro prostředí Windows PowerShell nastavit uživatelská hesla nechcete vyprší. Rutiny prostředí Windows PowerShell můžete použít také k odebrání platné stále konfigurace nebo zobrazíte uživatele, které jsou nastavené hesla nikdy nevyprší. 

Tyto pokyny platí pro jiných poskytovatelů, jako například Intune a Office 365, které také závisí na službě Azure AD pro identitu a adresářové služby. Vypršení platnosti hesla je pouze součástí zásad, která lze změnit.

> [!NOTE]
> Jenom hesla pro uživatelské účty, které nejsou synchronizovány pomocí synchronizace adresáře může být nakonfigurován pro nevyprší platnost. Další informace o synchronizaci adresářů, najdete v části [AD s Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect).
>
>

## <a name="set-or-check-the-password-policies-by-using-powershell"></a>Nastavte nebo zkontrolujte zásady pro hesla pomocí prostředí PowerShell

Chcete-li začít pracovat, je potřeba [stáhněte a nainstalujte modul Azure AD PowerShell](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0). Až budete mít nainstalováno, můžete použít následující postup ke konfiguraci každé pole.

### <a name="how-to-check-the-expiration-policy-for-a-password"></a>Postup kontroly zásad vypršení platnosti hesla
1. Připojení k prostředí Windows PowerShell pomocí svých přihlašovacích údajů správce společnosti.
2. Spusťte jeden z následujících příkazů:

   * Pokud chcete zobrazit, pokud heslo jednoho uživatele je nastaveno na jeho platnost nikdy nevypršela, spusťte následující rutinu pomocí hlavního názvu uživatele (například *aprilr@contoso.onmicrosoft.com*) nebo ID uživatele uživatele, které chcete zkontrolovat: `Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`
   * Pokud chcete zobrazit **heslo je platné stále** nastavení pro všechny uživatele, spusťte následující rutinu: `Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

### <a name="set-a-password-to-expire"></a>Nastavení hesla vyprší

1. Připojení k prostředí Windows PowerShell pomocí svých přihlašovacích údajů správce společnosti.
2. Spusťte jeden z následujících příkazů:

   * Nastavte heslo jednoho uživatele, aby vyprší platnost hesla, spusťte následující rutinu pomocí hlavního názvu uživatele nebo ID uživatele, uživatel: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $false`
   * Nastavte hesla všichni uživatelé v organizaci, aby vypršení jejich platnosti, použijte následující rutinu: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $false`

### <a name="set-a-password-to-never-expire"></a>Nastavení hesla bez vypršení platnosti

1. Připojení k prostředí Windows PowerShell pomocí svých přihlašovacích údajů správce společnosti.
2. Spusťte jeden z následujících příkazů:

   * Pokud chcete nastavit heslo jeden uživatel nikdy nevyprší, spusťte následující rutinu pomocí hlavního názvu uživatele nebo ID uživatele, uživatel: `Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires $true`
   * Pokud chcete nastavit hesel všech uživatelů v organizaci bez vypršení platnosti, spusťte následující rutinu: `Get-MSOLUser | Set-MsolUser -PasswordNeverExpires $true`

   > [!WARNING]
   > Nastavení hesla `-PasswordNeverExpires $true` stále stáří na základě `pwdLastSet` atribut. Pokud nastavíte uživatelská hesla bez vypršení platnosti a 90 dní přejít, vypršení platnosti hesla. Na základě `pwdLastSet` , pokud změníte vypršení platnosti do atribut `-PasswordNeverExpires $false`, všechna hesla, které mají `pwdLastSet` starší než 90 dní vyžadovat je změnit při příštím přihlášení uživatele. Tato změna může ovlivnit velký počet uživatelů. 

## <a name="next-steps"></a>Další postup

Následující články poskytují další informace o prostřednictvím služby Azure AD pro vytvoření nového hesla:

* [Jak dokončit úspěšné zavedení SSPR?](howto-sspr-deployment.md)
* [Resetování nebo změna hesla](../active-directory-passwords-update-your-own-password.md)
* [Registrace samoobslužného resetování hesla](../active-directory-passwords-reset-register.md)
* [Máte dotaz k licencování?](concept-sspr-licensing.md)
* [Jaká data používá SSPR a která data byste měli naplnit pro vaše uživatele?](howto-sspr-authenticationdata.md)
* [Které metody ověřování jsou dostupné pro uživatele?](concept-sspr-howitworks.md#authentication-methods)
* [Co je zpětný zápis hesla a proč byste se o něj měli starat?](howto-sspr-writeback.md)
* [Jak hlásit aktivitu v SSPR?](howto-sspr-reporting.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Myslím, že je něco poškozené. Jak řešit problémy SSPR?](active-directory-passwords-troubleshoot.md)
* [Mám otázku, která není zodpovězená jinde](active-directory-passwords-faq.md)
