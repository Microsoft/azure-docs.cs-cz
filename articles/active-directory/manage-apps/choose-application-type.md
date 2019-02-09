---
title: Jak zvolit typ aplikace, který se má použít při přidávání aplikace | Dokumentace Microsoftu
description: Vysvětlení podporované typy aplikací můžete integrovat se službou Azure AD a jejich související možnosti konfigurace
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: celested
ms.openlocfilehash: ec84ea47d7d0d136b744a4f7328a6729d05b0faf
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55961036"
---
# <a name="choosing-the-application-type-when-adding-an-application-in-azure-active-directory"></a>Volba typu aplikace, při přidávání aplikace v Azure Active Directory
Další informace o čtyři typy aplikací, které můžete přidat do služby Azure Active Directory (Azure AD). Při přidávání aplikace v Azure Active Directory, zobrazí se výzva k výběru jednoho typu čtyři aplikace. 

## <a name="what-are-the-types-of-applications"></a>Jaké jsou typy aplikací?

Azure AD podporuje čtyři typy hlavní aplikace, které můžete přidat pomocí **přidat** funkce nalezené pod **podnikové aplikace**. Mezi ně patří:

-   **Galerie aplikací Azure AD** – což je aplikace, který byl předem integrovaných pro jednotné přihlašování s Azure AD.

-   **Proxy aplikace** – aplikaci spuštěnou v místním prostředí, která byste chtěli poskytnout zabezpečené jednotné přihlašování k externě.

-   **Vlastní aplikace** – což je aplikace, která vaše organizace chce vývoj na platformě vývoje aplikací Azure AD, ale nemusí, která ještě neexistuje.

-   **Aplikace mimo galerii** – používání vlastních aplikací. Libovolný webový odkaz, který chcete nebo jakékoliv aplikace, která vykreslí pole uživatelského jména a hesla, podporuje přes protokol SAML nebo OpenID Connect nebo SCIM, kterou chcete integrovat pro jednotné přihlašování s Azure AD podporuje.

## <a name="features-and-capabilities-supported-by-the-application-types"></a>Funkce a možnosti podporované typy aplikací

Žádné typy předchozí čtyři aplikací ve službě Azure AD jsou podporovány následující funkce:

-   **Rychlý start** – rychlé zahájení práce s aplikací pomocí následujících [kroky jednoduché nasazení](https://docs.microsoft.com/azure/active-directory/active-directory-integrating-applications-getting-started)

-   **Obecné vlastnosti správy** – získání [přímého odkazu deeplink systému](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) k aplikaci [přizpůsobit branding](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-change-app-logo-user-azure-portal) aplikace, nebo [zakázání aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) pro všechny uživatele.

-   **Správa uživatelů a skupin** – [přiřadit](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) nebo [odebrat](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) uživatelů a skupin k aplikaci a volitelně přiřadit konkrétní aplikační role tito uživatelé a skupiny mají přístup k

-   **Samoobslužný přístup k aplikacím** – umožní vašim uživatelům požádat o [samoobslužného přístupu k aplikacím](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) k aplikaci ze svých přístupových panelech aplikace buď přidáním aplikace přímo nebo [ připojení ke skupině povolené samoobslužné](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management), volitelně vyžadující schválení na cestě

-   **Protokoly přihlášení** – naleznete v tématu [všech přihlášení k aplikaci](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins), nebo všechny aplikace

-   **Protokoly auditu** – viz [podrobné protokoly auditu o úpravy aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs), nebo ke všem vašim aplikacím

-   **Přístup podmíněný a na základě rizik** – při nastavení výkonné [pravidel přístupu na základě podmínky](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) , která vynucují, když se uživatel pokusí přihlásit ke konkrétní aplikaci

-   **Zobrazení oprávnění** – zobrazení všech [oprávnění OAuth2](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent) má přístup k umístění ve vašem adresáři z jedné aplikace

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Jednotné přihlašování a zřizování režimy podporované podle specifických typů aplikací

Následující tabulka popisuje různé jednotné přihlašování a zřizování režimy podporované jednotlivými typy předchozí aplikace. Vám pomůže tato tabulka vám pomůže lépe porozumět aplikaci, pro kterou je třeba přidat pro podporu určitého cíle.

  ![Tabulka typů aplikací](./media/choose-application-type/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Jak zvolit režim jednotného přihlašování

Toto jsou podporované **jednotného přihlašování** režimy pro aplikace Azure AD.

-   **Azure AD jednotné přihlašování zakázáno** – zvolte Azure AD jednotné přihlašování zakázáno **režim jednotného přihlašování** Pokud ještě nejsou připraveny na integraci této aplikace pomocí jednotného přihlašování s Azure AD, nebo ji jednoduše testování

-   **Propojené přihlašování** – zvolte [propojené přihlášení](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) **režim jednotného přihlašování** Pokud máte aplikaci, která je už propojená s existujícím jednotné přihlašování – řešení, nebo pokud chcete jen publikování jednoduchý odkaz na vaši uživatelé v jejich [přístupového panelu aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) nebo [Spouštěče aplikací Office 365](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)

-   **Přihlašování na základě heslo** – zvolte [přihlašování na základě heslo](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) **režim jednotného přihlašování** Pokud vaše aplikace zobrazí uživatelské jméno a heslo pole HTML a vy chcete ukládat tímto uživatelským jménem a heslo do přehrány aplikaci později

-   **Přihlašování na základě SAML** – zvolte [přihlašování na základě SAML](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) jednotné přihlašování v režimu, pokud vaše aplikace podporuje protokol SAML nebo OpenID Connect, nebo chcete být schopni mapování uživatelů na konkrétní aplikační role na základě pravidel můžete definovat vaše SAML deklaracemi identity *

   >[!NOTE]
   >Tato možnost není k dispozici, když proxy aplikací je nakonfigurován pro aplikaci.
   >
   >

-   **Přihlašování na základě záhlaví** – tuto možnost zvolte, [přihlašování na základě záhlaví](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access#what-is-pingaccess-for-azure-ad) jednotné přihlašování režimu Pokud máte aplikaci pomocí PingAccess, který podporuje založený na HTTP hlavičce ověřování, kterou chcete provést jednotné přihlašování k 

   >[!NOTE]
   >Tato možnost je dostupná, pouze pokud proxy aplikací a Pingaccessem je nakonfigurovaná pro aplikaci.
   >
   >

-   **Integrované ověřování Windows** – zvolte [integrované ověřování Windows](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) jednotného přihlašování při vystavení, kterou chcete provést jednotné přihlašování k aplikaci WIA v místním režimu 

   >[!NOTE]
   >Tato možnost je dostupná, jenom když se proxy aplikací je nakonfigurován pro aplikaci.
   >
   >

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Režimy jednotné přihlašování pro vlastní aplikace

V aplikacích, které máte vlastní vznikaly po vlastní vyvinuté aplikaci docházet také podporu další jednotné přihlašování režimy uvedli, mezi které patří:

-   [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) přihlašování založené na

-   [OpenID Connect 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) přihlašování založené na

-   [WS-Federation 1.2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html) přihlašování založené na

-   [Protokol SAML 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference) přihlašování založené na

Přečtěte si [Příručka pro vývojáře Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide) Další informace o tom, jak vytvořit vlastní aplikaci, která podporuje tyto režimy jednotné přihlašování.

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>Nastavení aplikace jednotné přihlašování režimu

Chcete-li nastavit aplikaci prvku **jednotného přihlašování** režimu, postupujte podle těchto pokynů:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

  * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, pro kterou chcete nakonfigurovat jednotné přihlašování.

7.  Po načtení aplikace, klikněte na tlačítko **jednotného přihlašování** levé navigační nabídce aplikace.

## <a name="how-to-choose-a-provisioning-mode"></a>Jak zvolit režim zřizování

-   **Ruční zřizování** – zvolte [ruční](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#provisioning-modes) režim zřizování, pokud máte existující účty, nebo chcete spravovat účty pro tuto aplikaci mimo Azure AD.

-   **Automatické zřizování** – zvolte [automatické](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#configuring-automatic-user-account-provisioning) **režim zřizování** Pokud chcete povolit automatické zřizování založené na rozhraní API a/nebo zrušení zřizování uživatelských účtů do této aplikace 

   >[!NOTE]
   >Tato možnost je dostupná jenom pro aplikace v rámci **vybrané** kategorii [Galerie aplikací Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-whats-new-azure-portal).
   >
   >

-   **Na základě SCIM automatické zřizování** – použijte [na základě SCIM automatické zřizování](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) Pokud vaše aplikace podporuje protokol SCIM pro zjištění změny uživatelů a skupin, které jsou automaticky generované pro změny všechny aplikace integrované s Azure AD 

   >[!NOTE]
   >Tato možnost není uvedena jako konkrétní režim zřizování, ale je povoleno standardně pro všechny aplikace, které jsou integrované s Azure AD.
   >
   >

## <a name="how-to-set-an-applications-provisioning-mode"></a>Jak nastavit aplikaci prvku režim zřizování

Chcete-li nastavit aplikaci prvku **zřizování** režimu, postupujte podle těchto pokynů:

Chcete-li nastavit aplikaci prvku **jednotného přihlašování** režimu, postupujte podle těchto pokynů:

1.  Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **Spolusprávce.**

2.  Otevřít **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní navigační nabídce vlevo.

3.  Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace** levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

  * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

6.  Vyberte aplikaci, pro kterou chcete provést konfiguraci zřizování.

7.  Po načtení aplikace, klikněte na tlačítko **zřizování** levé navigační nabídce aplikace.

## <a name="next-steps"></a>Další postup
[Správa aplikací pomocí Azure Active Directory](what-is-application-management.md)
