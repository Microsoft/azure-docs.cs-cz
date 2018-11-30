---
title: Přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace ve službě Azure AD | Dokumentace Microsoftu
description: Informace o přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace ve službě Azure AD.
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
ms.date: 10/20/2018
ms.author: celested
ms.reviewer: luleon, jeedes
ms.custom: aaddev
ms.openlocfilehash: afcdb7c64f4431e920f1f1fbce1e1e6d3e4db79c
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52424948"
---
# <a name="how-to-customize-claims-issued-in-the-saml-token-for-enterprise-applications"></a>Postupy: přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace

Azure Active Directory (Azure AD) ještě dnes podporuje jednotné přihlašování na se většina podnikových aplikací, včetně obě aplikace předem integrované v galerii aplikací Azure AD, jakož i vlastních aplikací. Při ověření uživatele k aplikaci prostřednictvím služby Azure AD pomocí protokolu SAML 2.0, Azure AD odešle token do aplikace (přes HTTP POST). A potom aplikaci ověří a použije token k přihlášení uživatele místo vás vyzve k zadání uživatelského jména a hesla. Tyto tokeny SAML obsahují informace o uživateli, známé jako "deklarace".

A *deklarace identity* informace, které stavy zprostředkovatelů identity o uživateli uvnitř tokenu vydaného pro tohoto uživatele. V [tokenu SAML](https://en.wikipedia.org/wiki/SAML_2.0), tato data je obvykle součástí příkaz Attribute SAML. Jedinečné ID uživatele je obvykle reprezentována zkratka jako identifikátor názvu předmětu SAML.

Ve výchozím nastavení Azure AD vydá SAML token pro vaše aplikace, která obsahuje deklarace identity NameIdentifier s hodnotou uživatelské jméno (hlavní název uživatele také označované LLAP) ve službě Azure AD. Tuto hodnotu můžete jednoznačné identifikaci uživatele. SAML token obsahuje také další deklarace identity, který obsahuje uživatele e-mailová adresa, jméno a příjmení.

K zobrazení nebo úpravám deklarací identity vystavených v tokenu SAML pro aplikaci, otevřete aplikaci na webu Azure portal. Vyberte **zobrazit a upravit všechny ostatní atributy uživatele** zaškrtávací políčko ve **atributy uživatele** části aplikace.

![Části atributy uživatele][1]

Existují dva možné důvody, proč může být potřeba upravit deklarací identity vystavených v tokenu SAML:
* Aplikace se zapsala do vyžadují jinou sadu deklarací identity identifikátory URI nebo hodnoty deklarací identity.
* Aplikace byla nasazena způsobem, který vyžaduje deklarace identity NameIdentifier na něco jiného než uživatelské jméno (také označované LLAP hlavní uživatelské jméno) uložené ve službě Azure AD.

Můžete upravit některou výchozí hodnoty deklarací identity. Vyberte řádek pro deklarace identity v tabulce atributy tokenu SAML. Tím se otevře **Upravit atribut** část a pak můžete upravit název deklarace identity, hodnotu a přidružený k deklaraci oboru názvů.

![Upravit atribut uživatele][2]

Můžete také odebrat deklarace (jiné než NameIdentifier) pomocí místní nabídky, které se otevře po kliknutí na **...**  ikonu. Můžete také přidat nové deklarace pomocí **přidat atribut** tlačítko.

![Upravit atribut uživatele][3]

## <a name="editing-the-nameidentifier-claim"></a>Úprava deklarace identity NameIdentifier

K vyřešení problému, kde byla aplikace nasazené pomocí jiné uživatelské jméno, vyberte na **identifikátor uživatele** rozevírací seznam **atributy uživatele** části. Tato akce obsahuje dialogové okno s několik možností:

![Upravit atribut uživatele][4]

### <a name="attributes"></a>Atributy

Vyberte požadovaný zdroj `NameIdentifier` (nebo NameID) deklarace identity. Můžete vybrat jednu z následujících možností.

| Název | Popis |
|------|-------------|
| Email | E-mailovou adresu uživatele |
| userprincipalName | Hlavní název uživatele (UPN) uživatele |
| onpremisessamaccount | Název účtu SAM, která je synchronizovaná z místní služby Azure AD |
| ID objektu | ID objektu uživatele ve službě Azure AD |
| EmployeeID | EmployeeID uživatele |
| Rozšíření adresáře | Rozšíření adresáře [synchronizované z místní služby Active Directory pomocí Azure AD Connect Sync](../hybrid/how-to-connect-sync-feature-directory-extensions.md) |
| Atributů rozšíření 1 – 15 | Místní atributy rozšíření používané k rozšíření schématu služby Azure AD |

### <a name="transformations"></a>Transformace

Můžete také použít funkce speciální deklarace identity transformace.

| Funkce | Popis |
|----------|-------------|
| **ExtractMailPrefix()** | Odebere příponu domény od e-mailovou adresu, název účtu SAM nebo hlavní název uživatele. To vyextrahuje jenom první část uživatelské jméno se předává (například "joe_smith" namísto joe_smith@contoso.com). |
| **Join()** | Připojí se atribut s ověřenou doménu. Pokud je hodnota identifikátoru vybraného uživatele domény, bude extrahovat uživatelské jméno pro připojení vybrané ověřenou doménu. Například, pokud vyberete e-mailu (joe_smith@contoso.com) jako hodnotu identifikátoru uživatele a vyberte contoso.onmicrosoft.com jako ověřenou doménu, výsledkem bude joe_smith@contoso.onmicrosoft.com. |
| **ToLower() byly** | Znaky v datech vybraného atributu převede na malá písmena. |
| **ToUpper()** | Znaky v datech vybraného atributu převede na velká písmena. |

## <a name="adding-claims"></a>Přidání deklarace identity

Při přidání deklarace identity, můžete zadat název (který se nemusí nezbytně se řídí vzorem identifikátoru URI podle specifikace SAML). Nastavte hodnotu na jakýkoli atribut uživatele, který je uložen v adresáři.

![Přidat atribut uživatele][7]

Například budete muset odeslat oddělení, které uživatel patří do jejich organizaci jako deklarace identity (například prodej). Zadejte název deklarace podle očekávání tím, aplikace a pak vyberte **user.department** jako hodnotu.

> [!NOTE]
> Pokud pro daného uživatele neexistuje převáděná hodnota uložena pro vybraný atribut, není právě tento deklarací identity vystavených v tokenu.

> [!TIP]
> **User.onpremisesecurityidentifier** a **user.onpremisesamaccountname** jsou podporovány pouze při synchronizaci dat uživatele z místní služby Active Directory pomocí [Azure AD Připojení nástroje](../hybrid/whatis-hybrid-identity.md).

## <a name="restricted-claims"></a>Deklarace identity s omezeným přístupem

Existují některé deklarace identity s omezeným přístupem v SAML. Pokud chcete přidat tyto deklarace, Azure AD nebude odesílat tyto deklarace identity. Sada deklarací identity SAML s omezením pomocí specifikátoru jsou následující:

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

* [Správa aplikací v Azure AD](../manage-apps/what-is-application-management.md)
* [Konfigurace jednotného přihlašování pro aplikace, které nejsou v galerii aplikací Azure AD](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Řešení potíží s založené na SAML jednotného přihlašování](howto-v1-debug-saml-sso-issues.md)

<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/user-attribute-section.png
[2]: ./media/active-directory-saml-claims-customization/edit-claim-name-value.png
[3]: ./media/active-directory-saml-claims-customization/delete-claim.png
[4]: ./media/active-directory-saml-claims-customization/user-identifier.png
[5]: ./media/active-directory-saml-claims-customization/extractemailprefix-function.png
[6]: ./media/active-directory-saml-claims-customization/join-function.png
[7]: ./media/active-directory-saml-claims-customization/add-attribute.png
