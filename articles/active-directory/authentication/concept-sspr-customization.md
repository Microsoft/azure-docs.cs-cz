---
title: Přizpůsobení samoobslužného resetování hesla – Azure Active Directory
description: Možnosti vlastního nastavení pro samoobslužné resetování hesla služby Azure AD
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979463"
---
# <a name="customize-the-azure-ad-functionality-for-self-service-password-reset"></a>Přizpůsobení funkce Azure AD pro samoobslužné resetování hesla

Odborníci v oblasti IT, kteří chtějí nasadit samoobslužné resetování hesla (SSPR) ve službě Azure Active directory (Azure AD), můžou přizpůsobit prostředí tak, aby odpovídalo potřebám jejich uživatelů.

## <a name="customize-the-contact-your-administrator-link"></a>Přizpůsobení odkazu Kontaktování správce

Uživatelé samoobslužného resetování hesla mají na portálu pro resetování hesla k dispozici odkaz "Kontaktujte správce". Pokud uživatel vybere tento odkaz, provede jednu ze dvou věcí:

* Pokud je ponecháno ve výchozím stavu:
   * Správcům je odeslán e-mail s žádostí o pomoc při změně hesla uživatele. Podívejte se na [ukázkový e-mail](#sample-email) níže.
* Pokud je přizpůsoben:
   * Odešle uživateli webovou stránku nebo e-mailovou adresu určenou správcem.

> [!TIP]
> Pokud tuto možnost přizpůsobíte, doporučujeme nastavit tuto možnost na něco, co uživatelé již znají pro podporu

> [!WARNING]
> Pokud toto nastavení upravíte pomocí e-mailové adresy a účtu, který vyžaduje resetování heslem, uživatel pravděpodobně nebude moci požádat o pomoc.

### <a name="sample-email"></a>Ukázkový e-mail

![Ukázkový požadavek na obnovení e-mailu odeslaný správci][Contact]

Kontaktní e-mail je odeslán následujícím příjemcům v následujícím pořadí:

1. Pokud je přiřazena role **správce technické podpory** nebo role správce **hesla,** budou upozorněni správci s těmito rolemi.
1. Pokud nejsou přiřazeni žádní správci technické podpory nebo správci hesel, budou upozorněni správci s rolí **správce uživatele.**
1. Pokud není přiřazena žádná z předchozích rolí, budou upozorněni **globální správci.**

Ve všech případech je oznámeno maximálně 100 příjemců.

Další informace o různých rolích správce a jejich přiřazení najdete [v tématu Přiřazení rolí správce ve službě Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

### <a name="disable-contact-your-administrator-emails"></a>Zakázání e-mailů "Kontaktovat správce"

Pokud vaše organizace nechce upozorňovat správce na požadavky na obnovení hesla, můžete povolit následující konfiguraci:

* Povolte samoobslužné resetování hesla pro všechny koncové uživatele. Tato možnost je v části**Vlastnosti** **obnovení** > hesla . Pokud nechcete, aby uživatelé resetovali svá vlastní hesla, můžete změnit přístup k prázdné skupině. *Tuto možnost nedoporučujeme.*
* Přizpůsobte odkaz helpdesk a zadejte webovou adresu URL nebo adresu mailto: adresu, kterou mohou uživatelé použít k získání pomoci. Tato možnost je v části Vlastní**nastavení vlastního technického oddělení** > **nebo adresa URL**pro obnovení >  **hesla**.

## <a name="customize-the-ad-fs-sign-in-page-for-sspr"></a>Přizpůsobení přihlašovací stránky služby AD FS pro samosprávu

Správci služby AD FS (AD FS) služby Active Directory mohou přidat odkaz na svou přihlašovací stránku pomocí pokynů uvedených v článku [Popis přihlašovací stránky přidat.](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/add-sign-in-page-description)

Chcete-li přidat odkaz na přihlašovací stránku služby AD FS, použijte na serveru služby AD FS následující příkaz. Uživatelé mohou tuto stránku použít k zadání pracovního postupu pro sazby 1SPR.

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><A href='https://passwordreset.microsoftonline.com' target='_blank'>Can’t access your account?</A></p>"
```

## <a name="customize-the-sign-in-page-and-access-panel-look-and-feel"></a>Přizpůsobení přihlašovací stránky a vzhledu a chování přístupového panelu

Přihlašovací stránku můžete přizpůsobit. Můžete přidat logo, které se zobrazí spolu s obrázkem, který odpovídá vaší společnosti značky.

Vybraná grafika se zobrazí v následujících případech:

* Poté, co uživatel zadá své uživatelské jméno
* Pokud uživatel přistupuje k přizpůsobené adrese URL:
   * Předáním `whr` parametru na stránku pro resetování hesla, jako je`https://login.microsoftonline.com/?whr=contoso.com`
   * Předáním `username` parametru na stránku pro resetování hesla, jako je`https://login.microsoftonline.com/?username=admin@contoso.com`

Podrobnosti o konfiguraci firemní značky najdete v článku [Přidání firemní značky na přihlašovací stránku ve službě Azure AD](../fundamentals/customize-branding.md).

### <a name="directory-name"></a>Název adresáře

Atribut názvu adresáře můžete změnit v části**Vlastnosti služby** **Azure Active Directory** > . Můžete zobrazit popisný název organizace, který je vidět na portálu a v automatizované komunikaci. Tato možnost je nejviditelnější v automatizovaných e-mailech ve formulářích, které následují:

* Popisný název v e-mailu, například "Microsoft jménem contoso demo"
* Předmět v e-mailu, například "CONTOSO demo účet e-mail ověřovací kód"

## <a name="next-steps"></a>Další kroky

* [Jak dokončit úspěšné zavedení SSPR?](howto-sspr-deployment.md)
* [Resetování nebo změna hesla](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrace pro samoobslužné resetování hesla](../user-help/active-directory-passwords-reset-register.md)
* [Máte dotaz k licencování?](concept-sspr-licensing.md)
* [Jaká data používá SSPR a jaká data byste měli naplnit pro vaše uživatele?](howto-sspr-authenticationdata.md)
* [Které metody ověřování jsou dostupné pro uživatele?](concept-sspr-howitworks.md#authentication-methods)
* [Jaké jsou možnosti zásad se SSPR?](concept-sspr-policy.md)
* [Co je zpětný zápis hesla a proč byste se o něj měli starat?](howto-sspr-writeback.md)
* [Jak hlásit aktivitu v SSPR?](howto-sspr-reporting.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Myslím, že je něco rozbité. Jak lze vyřešit probléms sspr?](active-directory-passwords-troubleshoot.md)
* [Mám otázku, která není zodpovězená jinde](active-directory-passwords-faq.md)

[Contact]: ./media/concept-sspr-customization/sspr-contact-admin.png "Požádejte správce o pomoc s obnovením e-mailu s heslem příklad"
