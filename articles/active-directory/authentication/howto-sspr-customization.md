---
title: Přizpůsobení samoobslužného resetování hesla – Azure Active Directory
description: Naučte se přizpůsobit možnosti zobrazení a zkušeností uživatelů pro Samoobslužné resetování hesla služby Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: e121a6a45ab20055591a3670152114b8d03529cf
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173990"
---
# <a name="customize-the-user-experience-for-azure-active-directory-self-service-password-reset"></a>Přizpůsobení uživatelského prostředí pro Azure Active Directory Samoobslužné resetování hesla

Samoobslužné resetování hesla (SSPR) poskytuje uživatelům ve službě Azure Active Directory (Azure AD) možnost změnit nebo resetovat svoje heslo bez zásahu správce nebo helpdesku. Pokud je účet uživatele uzamčený nebo zapomněl heslo, může postupovat podle výzev ke zrušení odblokování a opětovnému získání práce. Tato možnost omezuje volání helpdesku a ztrátu produktivity, když se uživatel nemůže přihlásit ke svému zařízení nebo aplikaci.

Pro zlepšení prostředí SSPR pro uživatele můžete přizpůsobit vzhled a chování stránky pro resetování hesla, e-mailových oznámení nebo přihlašovací stránky. Tyto možnosti přizpůsobení umožňují uživatelům, kteří jsou na správném místě, dát jasný přístup k prostředkům společnosti.
    
V tomto článku se dozvíte, jak přizpůsobit SSPR e-mailové odkazy pro uživatele, branding společnosti a AD FS odkaz na přihlašovací stránku.

## <a name="customize-the-contact-your-administrator-link"></a>Přizpůsobení odkazu "kontaktujte správce"

Aby uživatelé mohli získat pomoc s samoobslužným resetováním hesla, zobrazí se odkaz "kontaktujte správce" na portálu pro resetování hesla. Pokud uživatel vybere tento odkaz, provede jednu z následujících akcí:

* Pokud tento odkaz na kontakt zůstane ve výchozím stavu, pošle se vašim správcům e-mail a požádá ho, aby vám poskytl pomoc se změnou hesla uživatele. Následující ukázková e-mailová zpráva se zobrazuje jako výchozí:

    ![Ukázková žádost o resetování e-mailu odeslaného správci](./media/howto-sspr-customization/sspr-contact-admin.png)

* Pokud je uživatel přizpůsobený, pošle uživateli na webovou stránku nebo e-mailovou adresu určenou správcem pro pomoc.
    * Pokud tuto akci přizpůsobíte, doporučujeme nastavit tuto možnost na uživatele, kteří už pro podporu znají.

    > [!WARNING]
    > Pokud toto nastavení přizpůsobíte pomocí e-mailové adresy a účtu, který vyžaduje resetování hesla, uživatel nemusí být schopen požádat o pomoc.

### <a name="default-email-behavior"></a>Výchozí chování e-mailu

Výchozí kontaktní e-mail se pošle příjemcům v tomto pořadí:

1. Pokud je přiřazena role *správce helpdesku* nebo *správce hesel* , budou správci s těmito rolemi upozorněni.
1. Pokud není přiřazený žádný správce helpdesku ani správce hesel, budou upozorněni správci s rolí *Správce uživatelů* .
1. Pokud není přiřazená žádná z předchozích rolí, oznámí se *globální správci* .

Ve všech případech se oznamuje maximálně 100 příjemců.

Další informace o různých rolích správce a jejich přiřazení najdete v tématu [přiřazení rolí správce v Azure Active Directory](../roles/permissions-reference.md).

### <a name="disable-contact-your-administrator-emails"></a>Zakázání e-mailů s názvem "kontaktujte správce"

Pokud vaše organizace nechce upozornit správce na žádosti o resetování hesla, můžete použít následující možnosti konfigurace:

* Přizpůsobte odkaz na helpdesk, který poskytuje adresu URL webu nebo adresu mailto: adresy, které mohou uživatelé použít k získání pomoci. Tato možnost je v části **resetování hesla**  >  **Customization**  >  **vlastní e-mail helpdesku nebo adresa URL**.
* Povolit Samoobslužné resetování hesla pro všechny uživatele. Tato možnost je v části vlastnosti **resetování hesla**  >  **Properties**. Pokud nechcete, aby uživatelé obnovili vlastní hesla, můžete nastavit rozsah přístupu do prázdné skupiny. *Tuto možnost nedoporučujeme.*

## <a name="customize-the-sign-in-page-and-access-panel"></a>Přizpůsobení přihlašovací stránky a přístupového panelu

Přihlašovací stránku můžete přizpůsobit, například přidat logo, které se zobrazí spolu s obrázkem, který odpovídá brandingu vaší společnosti. Další informace o tom, jak nakonfigurovat Branding společnosti, najdete v tématu [Přidání firemního brandingu na přihlašovací stránku ve službě Azure AD](../fundamentals/customize-branding.md).

Obrázek, který zvolíte, se zobrazí v následujících situacích:

* Poté, co uživatel zadá své uživatelské jméno
* Pokud uživatel přistupuje k přizpůsobené adrese URL:
   * Předáním `whr` parametru na stránku pro resetování hesla, například `https://login.microsoftonline.com/?whr=contoso.com`
   * Předáním `username` parametru na stránku pro resetování hesla, například `https://login.microsoftonline.com/?username=admin@contoso.com`

### <a name="directory-name"></a>Název adresáře

Pokud chcete, aby vypadaly lépe uživatelsky přívětivé, můžete změnit název organizace na portálu a v automatizované komunikaci. Chcete-li změnit atribut názvu adresáře v Azure Portal, přejděte do části **Azure Active Directory**  >  **Properties (vlastnosti**). Tato srozumitelná možnost názvu organizace je nejčastěji viditelná v automatizovaných e-mailech, jako v následujících příkladech:

* Popisný název v e-mailu, například *Microsoft jménem ukázky společnosti Contoso*
* Řádek předmětu v e-mailu, například *kód pro ověření e-mailu s ukázkovým účtem contoso*

## <a name="customize-the-ad-fs-sign-in-page"></a>Přizpůsobení přihlašovací stránky AD FS

Pokud pro události přihlašování uživatelů používáte Active Directory Federation Services (AD FS) (AD FS), můžete na přihlašovací stránku přidat odkaz pomocí pokynů v článku [Přidání přihlašovací stránky](/windows-server/identity/ad-fs/operations/add-sign-in-page-description).

Poskytněte uživatelům odkaz na stránku, aby mohli zadat pracovní postup SSPR, například *https://passwordreset.microsoftonline.com* . Pokud chcete přidat odkaz na přihlašovací stránku AD FS, použijte na serveru AD FS tento příkaz:

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><a href='https://passwordreset.microsoftonline.com' target='_blank'>Can't access your account?</a></p>"
```

## <a name="next-steps"></a>Další kroky

Informace o používání SSPR ve vašem prostředí najdete v tématu [Možnosti vytváření sestav pro správu hesel služby Azure AD](howto-sspr-reporting.md).

Pokud máte nebo uživatelé s SSPR problémy, přečtěte si téma [řešení potíží s samoobslužným resetováním hesla](./troubleshoot-sspr.md) .