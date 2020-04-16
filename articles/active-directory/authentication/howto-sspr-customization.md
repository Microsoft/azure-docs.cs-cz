---
title: Přizpůsobení samoobslužného resetování hesla – Azure Active Directory
description: Přečtěte si, jak přizpůsobit možnosti zobrazení uživatelů a možností prostředí pro samoobslužné resetování hesla služby Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54a45602b80db965e3cc79d188dd40034a320b79
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394251"
---
# <a name="customize-the-user-experience-for-azure-active-directory-self-service-password-reset"></a>Přizpůsobení uživatelského prostředí pro samoobslužné resetování hesla služby Azure Active Directory

Samoobslužné resetování hesla (SSPR) umožňuje uživatelům ve službě Azure Active Directory (Azure AD) změnit nebo resetovat své heslo bez zapojení správce nebo technické podpory. Pokud je uživatelský účet uzamčen nebo zapomene heslo, může sledovat výzvy k odblokování a vrácení se do práce. Tato možnost snižuje volání technické podpory a ztrátu produktivity, když se uživatel nemůže přihlásit ke svému zařízení nebo aplikaci.

Chcete-li zlepšit možnosti samoresetování služby ASPR pro uživatele, můžete přizpůsobit vzhled stránky pro resetování hesla, e-mailových oznámení nebo přihlašovacích stránek. Tyto možnosti vlastního nastavení umožňují uživateli objasnit, že jsou na správném místě, a dát mu jistotu, že přistupují k firemním prostředkům.
    
Tento článek ukazuje, jak přizpůsobit odkaz na e-mail ovou adresu Samota pro uživatele, značku společnosti a odkaz na přihlašovací stránku služby AD FS.

## <a name="customize-the-contact-your-administrator-link"></a>Přizpůsobení odkazu Kontaktování správce

Chcete-li uživatelům pomoci s samoobslužným resetováním hesla, zobrazí se na portálu pro resetování hesla odkaz "Kontaktujte správce". Pokud uživatel vybere tento odkaz, udělá jednu ze dvou věcí:

* Pokud je tento odkaz kontaktů ponechán ve výchozím stavu, je správcům odeslán e-mail s žádostí o pomoc při změně hesla uživatele. Následující ukázkový e-mail zobrazuje tuto výchozí e-mailovou zprávu:

    ![Ukázkový požadavek na obnovení e-mailu odeslaný správci](./media/howto-sspr-customization/sspr-contact-admin.png)

* Pokud je přizpůsoben, odešle uživateli webovou stránku nebo e-mailovou adresu určenou správcem o pomoc.
    * Pokud tuto možnost přizpůsobíte, doporučujeme nastavit tuto možnost na něco, co uživatelé již znají pro podporu.

    > [!WARNING]
    > Pokud toto nastavení upravíte pomocí e-mailové adresy a účtu, který vyžaduje resetování heslem, uživatel pravděpodobně nebude moci požádat o pomoc.

### <a name="default-email-behavior"></a>Výchozí chování e-mailu

Výchozí kontaktní e-mail je příjemcům odeslán v následujícím pořadí:

1. Pokud je přiřazena role *správce technické podpory* nebo role správce *hesla,* budou upozorněni správci s těmito rolemi.
1. Pokud není přiřazen žádný správce technické podpory nebo správce hesel, budou upozorněni správci s rolí *správce uživatele.*
1. Pokud není přiřazena žádná z předchozích rolí, budou upozorněni *globální správci.*

Ve všech případech je oznámeno maximálně 100 příjemců.

Další informace o různých rolích správce a jejich přiřazení najdete [v tématu Přiřazení rolí správce ve službě Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

### <a name="disable-contact-your-administrator-emails"></a>Zakázání e-mailů "Kontaktovat správce"

Pokud vaše organizace nechce upozorňovat správce na požadavky na obnovení hesla, lze použít následující možnosti konfigurace:

* Přizpůsobte odkaz helpdesk a zadejte webovou adresu URL nebo adresu mailto: adresu, kterou mohou uživatelé použít k získání pomoci. Tato možnost je v části Vlastní**nastavení vlastního technického oddělení** > **nebo adresa URL**pro obnovení >  **hesla**.
* Povolte samoobslužné resetování hesla pro všechny uživatele. Tato možnost je v části**Vlastnosti** **obnovení** > hesla . Pokud nechcete, aby uživatelé resetovali svá vlastní hesla, můžete změnit přístup k prázdné skupině. *Tuto možnost nedoporučujeme.*

## <a name="customize-the-sign-in-page-and-access-panel"></a>Přizpůsobení přihlašovací stránky a přístupového panelu

Přihlašovací stránku můžete přizpůsobit, například přidat logo, které se zobrazí spolu s obrázkem, který odpovídá vaší firemní značce. Další informace o konfiguraci firemní značky najdete v [tématu Přidání firemní značky na přihlašovací stránku ve službě Azure AD](../fundamentals/customize-branding.md).

Vybraná grafika se zobrazí v následujících případech:

* Poté, co uživatel zadá své uživatelské jméno
* Pokud uživatel přistupuje k přizpůsobené adrese URL:
   * Předáním `whr` parametru na stránku pro resetování hesla, jako je`https://login.microsoftonline.com/?whr=contoso.com`
   * Předáním `username` parametru na stránku pro resetování hesla, jako je`https://login.microsoftonline.com/?username=admin@contoso.com`

### <a name="directory-name"></a>Název adresáře

Chcete-li, aby věci vypadaly uživatelsky přívětivěji, můžete změnit název organizace na portálu a v automatizované komunikaci. Pokud chcete změnit atribut názvu adresáře na webu Azure Portal, přejděte na **Azure Active Directory** > **Properties**. Tato možnost popisné organizace název je nejviditelnější v automatizovaných e-mailů, jako v následujících příkladech:

* Popisný název v e-mailu, například "*Microsoft jménem contoso demo*"
* Předmět v e-mailu, například "*CONTOSO demo účet e-mail ověřovací kód*"

## <a name="customize-the-ad-fs-sign-in-page"></a>Přizpůsobení přihlašovací stránky služby AD FS

Pokud pro události přihlášení uživatele používáte službu AD Federation Services (AD FS), můžete přidat odkaz na přihlašovací stránku pomocí pokynů v článku [k přidání popisu přihlašovací stránky](/windows-server/identity/ad-fs/operations/add-sign-in-page-description).

Poskytněte uživatelům odkaz na stránku, aby zadali *https://passwordreset.microsoftonline.com*pracovní postup sspr, například . Chcete-li přidat odkaz na přihlašovací stránku služby AD FS, použijte na serveru služby AD FS následující příkaz:

``` powershell
Set-ADFSGlobalWebContent -SigninPageDescriptionText "<p><a href='https://passwordreset.microsoftonline.com' target='_blank'>Can't access your account?</a></p>"
```

## <a name="next-steps"></a>Další kroky

Informace o využití sspr ve vašem prostředí najdete v [tématu Možnosti vytváření sestav pro správu hesel Azure AD](howto-sspr-reporting.md).

Pokud máte vy nebo uživatelé problémy se službou SSPR, [přečtěte si článek Poradce při řešení samoobslužného resetování hesla](active-directory-passwords-troubleshoot.md)
