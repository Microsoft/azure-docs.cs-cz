---
title: Jak určit, jaké jednotného přihlašování metodu | Dokumentace Microsoftu
description: Pochopit režimy jednotné přihlašování podporuje Azure AD a postupu při výběru, který se má vybrat pro aplikace, které vás zajímají.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: barbkess
ms.openlocfilehash: a9afb3f611a5762d329788b0aa7e1bc52e41fab3
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44356367"
---
# <a name="how-to-determine-what-single-sign-on-method-to-use"></a>Jak určit, jaké jednotného přihlašování metodu použít

V tomto článku vám pomůžou pochopit režimy jednotné přihlašování podporuje Azure AD a postupu při výběru, který se má vybrat pro aplikace, které vás zajímají.

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Jednotné přihlašování a zřizování režimy podporované podle specifických typů aplikací

Následující tabulka popisuje různé jednotné přihlašování a zřizování režimy podporované jednotlivými typy předchozí aplikace. Vám pomůže tato tabulka vám pomůže lépe porozumět aplikaci, pro kterou je třeba přidat pro podporu určitého cíle.

  ![Tabulka typů aplikací](./media/single-sign-on-modes/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Jak zvolit režim jednotného přihlašování

Toto jsou podporované **jednotného přihlašování** režimy pro aplikace Azure AD.

-   **Azure AD jednotné přihlašování zakázáno** – zvolte Azure AD jednotné přihlašování zakázáno **režim jednotného přihlašování** Pokud ještě nejsou připraveny na integraci této aplikace pomocí jednotného přihlašování s Azure AD, nebo ji jednoduše testování

-   **Propojené přihlašování** – zvolte [propojené přihlášení](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) **režim jednotného přihlašování** Pokud máte aplikaci, která je už propojená s existujícím jednotné přihlašování – řešení, nebo pokud chcete jen publikování jednoduchý odkaz na vaši uživatelé v jejich [přístupového panelu aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) nebo [Spouštěče aplikací Office 365](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)

-   **Přihlašování na základě heslo** – zvolte [přihlašování na základě heslo](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) **režim jednotného přihlašování** Pokud vaše aplikace zobrazí uživatelské jméno a heslo pole HTML a vy chcete ukládat tímto uživatelským jménem a heslo do přehrány aplikaci později

-   **Přihlašování na základě SAML** – zvolte [přihlašování na základě SAML](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#how-does-single-sign-on-with-azure-active-directory-work) jednotné přihlašování v režimu, pokud vaše aplikace podporuje protokol SAML nebo OpenID Connect, nebo chcete být schopni mapování uživatelů na konkrétní aplikační role na základě pravidel můžete definovat vaše SAML deklaracemi identity *(Poznámka:* \* tato možnost není k dispozici, když se proxy aplikací je nakonfigurován pro aplikaci)\*

-   **Přihlašování na základě záhlaví** – tuto možnost zvolte, [přihlašování na základě záhlaví](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#what-is-pingaccess-for-azure-ad) jednotné přihlašování režimu Pokud máte aplikaci pomocí PingAccess, který podporuje založený na HTTP hlavičce ověřování, kterou chcete provést jednotné přihlašování k *(Poznámka:* \* tato možnost je dostupná, jenom když proxy aplikací a Pingaccessem jsou nakonfigurovány pro aplikaci)\*

-   **Integrované ověřování Windows** – zvolte [integrované ověřování Windows](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) jednotné přihlašování v režimu při vystavení, kterou chcete provést jednotné přihlašování do aplikace v místním WIA *(Poznámka:* \* tato možnost je dostupná jenom při proxy aplikací je nakonfigurován pro aplikaci)\*

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Režimy jednotné přihlašování pro vlastní aplikace

Aplikace mají vlastní vznikaly [vlastní vyvinuté aplikaci](#_Custom-Developed_Applications) prostředí také podporují další jednotné přihlašování režimy uvedli, mezi které patří:

-   [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) přihlašování založené na

-   [OpenID Connect 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) přihlašování založené na

-   [WS-Federation 1.2](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) přihlašování založené na

-   [Protokol SAML 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference) přihlašování založené na

Přečtěte si [Příručka pro vývojáře Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide) získat další informace o tom, jak vytvořit vlastní vyvinuté aplikaci, která podporuje tyto režimy jednotné přihlašování.

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>Nastavení aplikace jednotné přihlašování režimu

Chcete-li nastavit aplikaci prvku **jednotného přihlašování** režimu, postupujte podle těchto pokynů:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete nakonfigurovat jednotné přihlašování

7.  Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

## <a name="next-steps"></a>Další postup
[Poskytovat jednotné přihlašování do aplikací pomocí Proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md)

