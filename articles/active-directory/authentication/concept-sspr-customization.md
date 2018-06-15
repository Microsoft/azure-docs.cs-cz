---
title: Resetování hesla pomocí samoobslužné služby přizpůsobení – Azure Active Directory
description: Resetování hesla pomocí samoobslužné služby Azure AD možnosti přizpůsobení
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: ffd12d03dffb5deafc8605cc7352bd71d588d235
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33866732"
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Přizpůsobení funkce služby Azure AD pro samoobslužné resetování hesla

Odborníci v oblasti IT, kteří chtějí nasadit samoobslužné resetování hesla (SSPR) v Azure Active directory (Azure AD) můžete přizpůsobit prostředí tak, aby odpovídaly potřebám své uživatele.

## <a name="customize-the-contact-your-administrator-link"></a>Přizpůsobení na odkaz "Obraťte se na správce"

I v případě, že SSPR není povolena, uživatelé stále mít "Získáte od správce" odkaz na heslo resetovat portálu. Pokud uživatel vybere tento odkaz se buď:
   * E-maily vašimi správci a je požádá o pomoc při změně hesla uživatele. 
   * Odešle uživatelům na adresu URL, kterou zadáte pro pomoc. 

Doporučujeme nastavit tento kontakt na něco jako e-mailová adresa nebo web, který uživatelé již používat pro dotazy týkající se podpory.

![Obraťte se na][Contact]

Kontaktní e-mail je odeslán následujícím příjemcům v následujícím pořadí:

1. Pokud **heslo správce** role je přiřazená, správci k této roli jsou upozorněni.
2. Pokud žádní správci heslo přiřazené, pak správcům, kteří mají **správce uživatele** role jsou upozorněni.
3. Pokud žádná z předchozích role přiřazené, pak se **globální správci** jsou upozorněni.

Ve všech případech jsou maximálně 100 příjemce oznámení.

Další informace o rolích jiný správce a jejich přiřazení, najdete v tématu [přiřazení rolí správce v Azure Active Directory](../active-directory-assign-admin-roles-azure-portal.md).

### <a name="disable-contact-your-administrator-emails"></a>Zakázat "Obraťte se na správce" e-mailů

Pokud vaše organizace nechce oznámí, že správci o heslo resetovat požadavky, můžete povolit následující konfiguraci:

* Povolte samoobslužné resetování hesla pro všechny uživatele. Tato možnost je v části **resetování hesla** > **vlastnosti**.
  
  Pokud nechcete, aby uživatelům resetovat vlastní hesla, můžete určit obor přístupu k prázdnou skupinou. *Tato možnost nedoporučujeme.*
* Přizpůsobení odkaz helpdesk poskytnout adresu URL webového nebo mailto: adresa, kterou mohou uživatelé získat pomoc. Tato možnost je v části **resetování hesla** > **přizpůsobení** > **vlastní technické podpory e-mailu nebo adresa URL**.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>Přizpůsobit přihlašovací stránku služby AD FS pro SSPR

Správci Active Directory Federation Services (AD FS) můžete přidat odkaz na jejich přihlašovací stránku pomocí pokyny v nalezen [přidat přihlašovací stránku popis](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description) článku.

Chcete-li přidat odkaz na přihlašovací stránku služby AD FS, použijte následující příkaz na serveru služby AD FS. Uživatelé mohou pomocí této stránky k zadání pracovního postupu SSPR.

``` Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href=’https://passwordreset.microsoftonline.com’>Can’t access your account?</A></p>" ```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>Přizpůsobit přihlašovací stránku a přístup k panelu vzhledu a chování

Můžete přizpůsobit přihlašovací stránku. Můžete přidat logo, které se zobrazí spolu s bitovou kopií, které vyhovuje značky společnosti.

Grafické objekty, které zvolíte jsou uvedeny v následujících případech:

* Poté, co uživatel zadá své uživatelské jméno
* Pokud uživatel přistoupí k vlastní adresu URL:
    * Předáním *Wh* parametru heslo resetovat stránky, jako je třeba "https://login.microsoftonline.com/?whr=contoso.com"
    * Předáním *uživatelské jméno* parametru heslo resetovat stránky, jako je třeba "https://login.microsoftonline.com/?username=admin@contoso.com"

Najít podrobnosti o tom, jak nakonfigurovat firemního brandingu v článku [přidání firemního brandingu na přihlašovací stránku ve službě Azure AD](../customize-branding.md).

### <a name="directory-name"></a>Název adresáře

Můžete změnit atribut názvu adresáře v rámci **Azure Active Directory** > **vlastnosti**. Organizace popisný název, který se může zobrazit v portálu a automatizované komunikace. Tato možnost je nejvíce viditelné v automatizovaných e-mailů ve formulářích, které následují:

* Popisný název e-mailu, například "Microsoft jménem ukázkové společnosti CONTOSO"
* Řádek předmětu e-mailu, například "CONTOSO ukázkový e-mailu ověřovací kód účtu"

## <a name="next-steps"></a>Další postup

* [Jak dokončit úspěšné zavedení SSPR?](howto-sspr-deployment.md)
* [Resetování nebo změna hesla](../active-directory-passwords-update-your-own-password.md)
* [Registrace samoobslužného resetování hesla](../active-directory-passwords-reset-register.md)
* [Máte dotaz k licencování?](concept-sspr-licensing.md)
* [Jaká data používá SSPR a která data byste měli naplnit pro vaše uživatele?](howto-sspr-authenticationdata.md)
* [Které metody ověřování jsou dostupné pro uživatele?](concept-sspr-howitworks.md#authentication-methods)
* [Jaké jsou možnosti zásad se SSPR?](concept-sspr-policy.md)
* [Co je zpětný zápis hesla a proč byste se o něj měli starat?](howto-sspr-writeback.md)
* [Jak hlásit aktivitu v SSPR?](howto-sspr-reporting.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Myslím, že je něco poškozené. Jak řešit problémy SSPR?](active-directory-passwords-troubleshoot.md)
* [Mám otázku, která není zodpovězená jinde](active-directory-passwords-faq.md)

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "Požádejte správce o pomoc resetovat vaše heslo e-mailu příklad"
