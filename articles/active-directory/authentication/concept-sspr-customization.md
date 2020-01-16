---
title: Přizpůsobení samoobslužného resetování hesla – Azure Active Directory
description: Možnosti vlastního nastavení pro Samoobslužné resetování hesla služby Azure AD
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
ms.openlocfilehash: c6f7f59f7bcc93edafa3cbb47bd432b52bde985c
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979463"
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Přizpůsobení funkce Azure AD pro Samoobslužné resetování hesla

IT profesionálové, kteří chtějí nasadit Samoobslužné resetování hesla (SSPR) ve službě Azure Active Directory (Azure AD), můžou přizpůsobit prostředí tak, aby odpovídalo potřebám svých uživatelů.

## <a name="customize-the-contact-your-administrator-link"></a>Přizpůsobení odkazu "kontaktujte správce"

Uživatelé samoobslužného resetování hesla mají v portálu pro resetování hesel k dispozici odkaz "kontaktujte správce". Pokud uživatel vybere tento odkaz, provede jednu z následujících akcí:

* Pokud necháte výchozí stav:
   * E-mail se pošle vašim správcům a požádá ho, aby vám poskytl pomoc se změnou hesla uživatele. Podívejte se na [ukázkový e-mail](#sample-email) níže.
* Pokud je přizpůsobený:
   * Pošle uživateli na webovou stránku nebo e-mailovou adresu určenou správcem pro pomoc.

> [!TIP]
> Pokud tuto akci přizpůsobíte, doporučujeme nastavit tuto možnost na uživatele, kteří už pro podporu znají.

> [!WARNING]
> Pokud toto nastavení přizpůsobíte pomocí e-mailové adresy a účtu, který vyžaduje resetování hesla, uživatel nemusí být schopen požádat o pomoc.

### <a name="sample-email"></a>Ukázkový e-mail

![Ukázková žádost o resetování e-mailu odeslaného správci][Contact]

Kontaktní e-mail se pošle následujícím příjemcům v tomto pořadí:

1. Pokud je přiřazena role **správce helpdesku** nebo **správce hesel** , budou správci s těmito rolemi upozorněni.
1. Pokud nejsou přiřazeni žádní správci helpdesku ani správce hesel, budou upozorněni na správce s rolí **Správce uživatelů** .
1. Pokud není přiřazená žádná z předchozích rolí, oznámí se **globální správci** .

Ve všech případech se oznamuje maximálně 100 příjemců.

Další informace o různých rolích správce a jejich přiřazení najdete v tématu [přiřazení rolí správce v Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

### <a name="disable-contact-your-administrator-emails"></a>Zakázání e-mailů s názvem "kontaktujte správce"

Pokud vaše organizace nechce informovat správce o požadavcích na resetování hesla, můžete povolit následující konfiguraci:

* Povolit Samoobslužné resetování hesla pro všechny koncové uživatele. Tato možnost je v části **resetování hesla** > **vlastnosti**. Pokud nechcete, aby uživatelé obnovili vlastní hesla, můžete nastavit rozsah přístupu do prázdné skupiny. *Tuto možnost nedoporučujeme.*
* Přizpůsobte odkaz na helpdesk, který poskytuje adresu URL webu nebo adresu mailto: adresy, které mohou uživatelé použít k získání pomoci. Tato možnost je v části **resetování hesla** > **přizpůsobení** > **e-mail nebo adresa URL vlastního helpdesku**.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>Přizpůsobení přihlašovací stránky AD FS pro SSPR

Správci Active Directory Federation Services (AD FS) (AD FS) můžou přidat odkaz na přihlašovací stránku pomocí pokynů, které najdete v článku [Přidání přihlašovací stránky s popisem](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description) .

Chcete-li přidat odkaz na přihlašovací stránku AD FS, použijte následující příkaz na serveru AD FS. Uživatelé můžou pomocí této stránky zadat pracovní postup SSPR.

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href='https://passwordreset.microsoftonline.com' target='_blank'>Can’t access your account?</A></p>"
```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>Přizpůsobení stránky pro přihlášení a vzhledu přístupového panelu

Přihlašovací stránku můžete přizpůsobit. Můžete přidat logo, které se zobrazí spolu s obrázkem, který odpovídá branding vaší společnosti.

Obrázek, který zvolíte, se zobrazí v následujících situacích:

* Poté, co uživatel zadá své uživatelské jméno
* Pokud uživatel přistupuje k přizpůsobené adrese URL:
   * Předáním parametru `whr` na stránku pro resetování hesla, například `https://login.microsoftonline.com/?whr=contoso.com`
   * Předáním parametru `username` na stránku pro resetování hesla, například `https://login.microsoftonline.com/?username=admin@contoso.com`

Podrobné informace o tom, jak nakonfigurovat Branding společnosti, najdete v článku [Přidání firemního brandingu na přihlašovací stránku ve službě Azure AD](../fundamentals/customize-branding.md).

### <a name="directory-name"></a>Název adresáře

V části **Azure Active Directory** > **vlastnosti**můžete změnit atribut název adresáře. Můžete zobrazit popisný název organizace, který se zobrazuje na portálu a v automatizované komunikaci. Tato možnost je nejvíce viditelná v automatizovaných e-mailech ve formulářích, které následují:

* Popisný název v e-mailu, například Microsoft jménem ukázky společnosti CONTOSO
* Řádek předmětu v e-mailu, například kód pro ověření e-mailu s ukázkovým účtem CONTOSO

## <a name="next-steps"></a>Další kroky

* [Jak dokončit úspěšné zavedení SSPR?](howto-sspr-deployment.md)
* [Resetování nebo změna hesla](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrace samoobslužného resetování hesla](../user-help/active-directory-passwords-reset-register.md)
* [Máte dotaz k licencování?](concept-sspr-licensing.md)
* [Jaká data používá SSPR a jaká data byste měli naplnit pro vaše uživatele?](howto-sspr-authenticationdata.md)
* [Které metody ověřování jsou dostupné pro uživatele?](concept-sspr-howitworks.md#authentication-methods)
* [Jaké jsou možnosti zásad se SSPR?](concept-sspr-policy.md)
* [Co je zpětný zápis hesla a proč byste se o něj měli starat?](howto-sspr-writeback.md)
* [Jak hlásit aktivitu v SSPR?](howto-sspr-reporting.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Myslím, že je něco přerušeno. Návody řešit potíže s SSPR?](active-directory-passwords-troubleshoot.md)
* [Mám otázku, která není zodpovězená jinde](active-directory-passwords-faq.md)

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "Řekněte správci, aby vám pomohli resetovat e-maily s heslem. příklad"
