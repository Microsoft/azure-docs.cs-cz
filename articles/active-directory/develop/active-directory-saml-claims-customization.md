---
title: Přizpůsobení deklarace identity vystavené v tokenu SAML pro podnikové aplikace v Azure Active Directory | Microsoft Docs
description: Naučte se přizpůsobovat deklarace identity vystavené v tokenu SAML pro podnikové aplikace v Azure Active Directory
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: 485e4c52751b390ebd440718e7a55de7b5b5ffd7
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="customizing-claims-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>Přizpůsobení deklarace identity vystavené v tokenu SAML pro podnikové aplikace v Azure Active Directory
Dnes Azure Active Directory podporuje jednotného přihlašování na s většinou podnikových aplikací, včetně obě aplikace předem integrovaných v galerii aplikací Azure AD a také vlastních aplikací. Když se uživatel přihlásí k aplikaci prostřednictvím služby Azure AD pomocí protokolu SAML 2.0, Azure AD odešle token do aplikace (přes HTTP POST). A pak aplikaci ověří a použije token k přihlásit uživatele místo výzvy k zadání uživatelského jména a hesla. Tyto tokeny SAML obsahují informace o uživateli známé jako "deklarace identity".

Ve vztahu identity Předčítání, "deklarace identity" je informace, které stavy zprostředkovatele identity o uživateli uvnitř tokenu vydaného pro tohoto uživatele. V [tokenu SAML](http://en.wikipedia.org/wiki/SAML_2.0), tato data je obvykle součástí příkaz atribut SAML. Jedinečné ID uživatele je obvykle reprezentována v předmětu SAML také nazývané jako název identifikátor.

Ve výchozím nastavení Azure Active Directory vystaví SAML token do vaší aplikace, která obsahuje deklaraci NameIdentifier s hodnotou uživatelské jméno (hlavní název uživatele NEBOLI) ve službě Azure AD. Tato hodnota může jednoznačné identifikaci uživatele. SAML token také obsahuje další deklarace obsahující uživatele e-mailová adresa, jméno a příjmení.

Chcete-li zobrazit nebo upravit deklarace identity vystavené v tokenu SAML k aplikaci, otevřete aplikaci na portálu Azure. Vyberte **zobrazení a upravovat všechny ostatní atributy uživatele** zaškrtnout políčko **uživatelské atributy** část aplikace.

![Uživatelské atributy oddílu][1]

Existují dvě možné důvody, proč je potřeba upravit deklarace identity vystavené v tokenu SAML:
* Aplikace byla zapsána vyžadují jinou sadu deklarací identity identifikátory URI nebo hodnot deklarací identity.
* Aplikace byly nasazeny způsobem, který vyžaduje NameIdentifier deklarace identity na něco jiného než uživatelské jméno (hlavní název uživatele NEBOLI) uložené ve službě Azure Active Directory.

Můžete upravit všechny výchozí hodnoty deklarace identity. Vyberte řádek pro deklarace identity v tabulce atributy tokenu SAML. Tím se otevře **Upravit atribut** části a pak můžete upravit název deklarace identity, hodnotu a přidružený k deklaraci oboru názvů.

![Upravit atribut uživatele][2]

Rovněž můžete odebrat deklarace (jiné než NameIdentifier) pomocí místní nabídky, otevře se kliknutím na **...**  ikonu. Můžete také přidat nové deklarace, pomocí **přidat atribut** tlačítko.

![Upravit atribut uživatele][3]

## <a name="editing-the-nameidentifier-claim"></a>Úpravy NameIdentifier deklarace identity
K vyřešení problému, kde byla aplikace nasazená pomocí jiné uživatelské jméno, klikněte na **uživatelský identifikátor** rozevírací nabídky **uživatelské atributy** části. Tato akce poskytuje dialogové okno s několik možností:

![Upravit atribut uživatele][4]

V rozevíracím seznamu vyberte **user.mail** nastavit NameIdentifier deklarace identity jako e-mailovou adresu uživatele v adresáři. Nebo vyberte **user.onpremisessamaccountname** nastavení pro uživatele je název účtu SAM, které se synchronizovaly z místní služby Azure AD.

Můžete také použít speciální **ExtractMailPrefix()** funkce odebrat příponu domény z e-mailovou adresu, název účtu SAM nebo hlavní název uživatele. To vyextrahuje pouze první část předávány prostřednictvím uživatelské jméno (například "joe_smith" místo joe_smith@contoso.com).

![Upravit atribut uživatele][5]

Jsme nyní jste také přidali **join()** funkce pro připojení k doméně ověřené s hodnotou identifikátoru uživatele. Když vyberete funkci join() v **uživatelský identifikátor** nejprve vybrat identifikátor uživatele jako jako e-mailovou adresu nebo uživatele hlavní název a potom v druhé rozevíracího seznamu vyberte ověřené domény. Pokud vyberete e-mailovou adresu s ověřenou doménu, pak Azure AD extrahuje uživatelské jméno z první hodnota joe_smith z joe_smith@contoso.com a jeho připojení s contoso.onmicrosoft.com. Podívejte se na následující příklad:

![Upravit atribut uživatele][6]

## <a name="adding-claims"></a>Přidání deklarace identity
Při přidání deklarace identity, můžete zadat název atributu (která nepotřebuje striktně dodržovat vzor URI podle specifikace SAML). Nastavte hodnotu na jakýkoli atribut uživatele, který je uložen v adresáři.

![Přidat atribut uživatele][7]

Například budete muset poslat oddělení, které uživatel patří do jejich organizace jako deklarace identity (například prodej). Zadejte název deklarace podle očekávání aplikací a potom vyberte **user.department** jako hodnotu.

> [!NOTE]
> Pokud pro daného uživatele neexistuje žádná hodnota uložené pro vybraný atribut, není právě této deklarace identity vystavené v tokenu.

> [!TIP]
> **User.onpremisesecurityidentifier** a **user.onpremisesamaccountname** podporují jenom při synchronizaci dat uživatele z místní služby Active Directory pomocí [Azure AD Připojení nástroje](../active-directory-aadconnect.md).

## <a name="restricted-claims"></a>Deklarace identity s omezeným přístupem

Existují některé s omezeným přístupem deklarace identity v SAML. Pokud přidáte tyto deklarace, Azure AD, nebude odesílat tyto deklarace identity. Následují sadě deklarací identity SAML omezený:

    | Typ deklarace identity (URI) |
    | ------------------- |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expired |
    | http://schemas.microsoft.com/identity/claims/accesstoken |
    | http://schemas.microsoft.com/identity/claims/openid2_id |
    | http://schemas.microsoft.com/identity/claims/identityprovider |
    | http://schemas.microsoft.com/identity/claims/objectidentifier |
    | http://schemas.microsoft.com/identity/claims/puid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier[MR1] |
    | http://schemas.microsoft.com/identity/claims/tenantid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod |
    | http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groups |
    | http://schemas.microsoft.com/claims/groups.link |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/role |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/wids |
    | http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant |
    | http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown |
    | http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged |
    | http://schemas.microsoft.com/2014/03/psso |
    | http://schemas.microsoft.com/claims/authnmethodsreferences |
    | http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier |
    | http://schemas.microsoft.com/identity/claims/scope |

## <a name="next-steps"></a>Další postup
* [Rejstřík článků o správě aplikací ve službě Azure Active Directory](../active-directory-apps-index.md)
* [Konfigurace jednotného přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory](../application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [Řešení potíží s na základě SAML jednotné přihlašování](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/user-attribute-section.png
[2]: ./media/active-directory-saml-claims-customization/edit-claim-name-value.png
[3]: ./media/active-directory-saml-claims-customization/delete-claim.png
[4]: ./media/active-directory-saml-claims-customization/user-identifier.png
[5]: ./media/active-directory-saml-claims-customization/extractemailprefix-function.png
[6]: ./media/active-directory-saml-claims-customization/join-function.png
[7]: ./media/active-directory-saml-claims-customization/add-attribute.png
