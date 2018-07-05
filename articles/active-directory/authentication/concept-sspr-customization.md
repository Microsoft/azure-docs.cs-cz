---
title: Samoobslužné resetování hesla přizpůsobení – Azure Active Directory
description: Resetovat možnosti vlastního nastavení pro hesla pomocí samoobslužné služby Azure AD
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: afcd39e6512ac7bcb46f56df157822efa463113e
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449794"
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Přizpůsobení funkce služby Azure AD pro samoobslužné resetování hesla

Odborníci v oblasti IT, kteří chtějí nasadit samoobslužné resetování hesla (SSPR) ve službě Azure Active directory (Azure AD) můžete přizpůsobit prostředí tak, aby odpovídaly potřebám svých uživatelů.

## <a name="customize-the-contact-your-administrator-link"></a>Přizpůsobit odkaz "Kontaktujte správce"

I v případě, že není povolené samoobslužné resetování HESLA, mít uživatelé dál na portál pro resetování odkaz "Kontaktujte správce" na heslo. Pokud uživatel vybere tento odkaz je buď:
   * E-mailem správci a požádá o pomoc při změně uživatelského hesla. 
   * Odešle uživatelům na adresu URL, které určíte pro pomoc. 

Doporučujeme nastavit tento kontakt na e-mailovou adresu nebo web, který vaši uživatelé již používají pro dotazy na podporu.

![Obraťte se na][Contact]

Kontaktní e-mail je odeslán následujícím příjemcům v následujícím pořadí:

1. Pokud **správce hesel** role je přiřazená, správci s touto rolí se zobrazí oznámení.
2. Pokud žádné heslo správcům se přiřazuje, pak správcům **Správce uživatelů** role se zobrazí oznámení.
3. Pokud ani jeden z předchozích role přiřadí, pak bude **globální správci** se zobrazí oznámení.

Ve všech případech se zobrazí maximálně 100 příjemců oznámení.

Další informace o správce jiné role a postupy je přiřadit, naleznete v tématu [přiřazení rolí správce v Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

### <a name="disable-contact-your-administrator-emails"></a>Zakázat "Kontaktujte správce" e-mailů

Pokud vaše organizace nechce upozornit, že žádosti o resetování hesla správce, můžete povolit následující konfiguraci:

* Povolte samoobslužné resetování hesla pro všechny koncové uživatele. Tato možnost je v části **resetování hesla** > **vlastnosti**.
  
  Pokud nechcete, aby uživatelům resetování vlastních hesel, můžete omezit rozsah přístupu k prázdnou skupinou. *Nedoporučujeme tuto možnost.*
* Přizpůsobení helpdesku odkaz umožňující zadat adresu URL webové nebo mailto: adresu, kterou mohou uživatelé získat pomoc. Tato možnost je v části **resetování hesla** > **přizpůsobení** > **vlastní technické podpory e-mailu nebo adresa URL**.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>Přizpůsobení přihlašovací stránky služby AD FS pro samoobslužné resetování HESLA

Správce Active Directory Federation Services (AD FS) můžete přidat odkaz na přihlašovací stránku pomocí pokynů v [přidat přihlašovací stránku popis](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description) článku.

Můžete přidat odkaz na přihlašovací stránku služby AD FS, použijte následující příkaz na serveru služby AD FS. Uživatelé můžou na této stránce můžete zadat pracovního postupu samoobslužné resetování HESLA.

``` Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href=’https://passwordreset.microsoftonline.com’>Can’t access your account?</A></p>" ```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>Přizpůsobit přihlašovací stránku a přístup k panelu vzhledu a chování

Můžete přizpůsobit přihlašovací stránku. Můžete přidat logo, které se zobrazí spolu s bitovou kopii, která odpovídá značku své firmy.

Grafiky, kterou zvolíte, jsou uvedeny v následujících případech:

* Poté, co uživatel zadá své uživatelské jméno
* Když chce uživatel na přizpůsobenou adresu URL:
    * Předáním *Wh* parametr heslo resetovat stránky, jako je třeba "https://login.microsoftonline.com/?whr=contoso.com"
    * Předáním *uživatelské jméno* parametr heslo resetovat stránky, jako je třeba "https://login.microsoftonline.com/?username=admin@contoso.com"

Najít podrobnosti o tom, jak nakonfigurovat vlastní firemní branding v článku [přidání firemního brandingu na přihlašovací stránku ve službě Azure AD](../fundamentals/customize-branding.md).

### <a name="directory-name"></a>Název adresáře

Můžete změnit atribut názvu adresáře v rámci **Azure Active Directory** > **vlastnosti**. Organizace popisný název, který zobrazuje můžete zobrazit na portálu a v rámci automatizovaného komunikace. Tato možnost je nejviditelnější automatizovaných e-mailů ve formulářích, které následují:

* Popisný název e-mailu, například "Microsoft jménem společnosti CONTOSO ukázku".
* Řádek předmětu e-mailu, například "CONTOSO ukázka e-mailu ověřovací kód účtu"

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

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "Požádejte správce o pomoc, resetuje se heslo e-mailu příkladu"
