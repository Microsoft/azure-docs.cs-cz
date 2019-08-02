---
title: Jak zvolit typ aplikace, který se má použít při přidávání aplikace | Microsoft Docs
description: Seznamte se s podporovanými typy aplikací, které můžete integrovat s Azure AD, a jejich souvisejícími možnostmi konfigurace
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: fb2c49d6436a14e9b6cbb0a92eb0dfba077c8e4d
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424251"
---
# <a name="choosing-the-application-type-when-adding-an-application-in-azure-active-directory"></a>Výběr typu aplikace při přidávání aplikace v Azure Active Directory

Seznamte se se čtyřmi typy aplikací, které můžete přidat do Azure Active Directory (Azure AD). Když přidáváte aplikaci v Azure Active Directory, zobrazí se výzva k výběru jednoho ze čtyř typů aplikací.

## <a name="what-are-the-types-of-applications"></a>Jaké jsou typy aplikací?

Azure AD podporuje čtyři typy hlavních aplikací, které můžete přidat pomocí funkce **Přidat** , která se nachází v části **podnikové aplikace**. Mezi ně patří:

- **Aplikace Galerie Azure AD** – aplikace, která je předem integrovaná pro jednotné přihlašování pomocí Azure AD.

- Aplikace **proxy aplikací** – aplikace spuštěná v místním prostředí, které chcete pro externě poskytovat zabezpečené jednotné přihlašování.

- **Vlastní aplikace vyvíjené** aplikací – aplikace, kterou vaše organizace chce vyvíjet na platformě pro vývoj aplikací Azure AD, ale ta ještě nemusí existovat.

- **Aplikace mimo galerii** – Přineste si vlastní aplikace! Libovolný webový odkaz, který chcete, nebo libovolnou aplikaci, která vykreslí pole uživatelského jména a hesla, podporuje protokoly SAML nebo OpenID Connect nebo podporuje SCIM, které chcete integrovat pro jednotné přihlašování pomocí Azure AD.

## <a name="features-and-capabilities-supported-by-the-application-types"></a>Funkce a možnosti podporované typy aplikací

Některé z předchozích čtyř typů aplikací v Azure AD podporují následující funkce:

- **Rychlý Start** – rychlé zprovoznění aplikací pomocí [jednoduchých kroků nasazení](https://docs.microsoft.com/azure/active-directory/active-directory-integrating-applications-getting-started)

- **Správa obecných vlastností** – získat [přímý](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) odkaz na aplikaci, [přizpůsobit značku](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-change-app-logo-user-azure-portal) aplikace nebo [Zakázat aplikaci](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) pro všechny uživatele.

- **Správa uživatelů a skupin** – [přiřazení](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) nebo [Odebrání](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) uživatelů a skupin k aplikaci a volitelně přiřazení konkrétních aplikačních rolí, ke kterým mají tito uživatelé a skupiny přístup

- **Samoobslužný přístup k aplikacím** – Umožněte uživatelům požádat o [Samoobslužný přístup](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) k aplikacím z jejich panelů přístupu k aplikacím, a to buď přidáním aplikace, nebo připojením k [samoobslužně povolené skupině. ](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management)volitelně vyžadování obchodního schválení způsobem

- **Protokoly přihlášení** – Podívejte se [na všechna přihlášení do aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins)nebo na všechny vaše aplikace.

- **Protokoly auditu** – Podívejte se na [podrobné protokoly auditu o úpravách aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)nebo všech vašich aplikacích.

- **Podmíněný a rizikový přístup** – nastavte výkonná [pravidla přístupu založená na podmínkách](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) , která se vynutila při pokusu uživatele o přihlášení ke konkrétní aplikaci.

- **Zobrazení oprávnění** – umožňuje zobrazit všechna [oprávnění OAuth2](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent) , ke kterým má aplikace přístup v adresáři z jednoho místa.

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Jednotné přihlašování a režimy zřizování podporované konkrétními typy aplikací

V následující tabulce jsou popsány různé režimy jednotného přihlašování a zřizování podporované každým z předchozích typů aplikací. Tato tabulka vám pomůže pochopit, kterou aplikaci potřebujete přidat k podpoře konkrétního cíle.

  ![Tabulka: Různé režimy jednotného přihlašování a zřizování, které podporuje každý typ aplikace](./media/choose-application-type/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Jak zvolit režim jednotného přihlašování

Níže jsou uvedené podporované režimy **jednotného přihlašování** pro aplikace Azure AD.

- **Jednotné přihlašování Azure AD** – Pokud ještě nejste připraveni k integraci této aplikace s jednotným přihlašováním pomocí Azure AD, vyberte **režim** jednotného PŘIhlašování v Azure AD.

- **Propojené** přihlašování – Pokud máte aplikaci [](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) , která je už připojená k existujícímu řešení jednotného přihlašování, vyberte **režim jednotného přihlašování** , pokud máte aplikaci, která už je propojená s existujícím řešením jednotného přihlašování, nebo pokud chcete jenom publikovat jednoduchý odkaz pro uživatele v jejich [ Panel pro přístup k aplikaci](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) nebo [spouštěč aplikací Office 365](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none)

- **Přihlašování založené** na heslech – Pokud vaše aplikace vykreslí pole uživatelské jméno a heslo ve formátu HTML a chcete bezpečně uložit uživatelské jméno a heslo, aby bylo možné je [znovu](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) přehrát do, vyberte **režim jednotného přihlašování** založený na heslech. aplikace později

- **Přihlašování založené na SAML** – vyberte režim jednotného přihlašování [založený na SAML](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) , pokud vaše aplikace podporuje protokoly SAML nebo OpenID Connect, nebo chcete, aby bylo možné mapovat uživatele na konkrétní aplikační role na základě pravidel definovaných ve vašem SAML. podpory

  >[!NOTE]
  >Tato možnost není k dispozici, pokud je proxy aplikace nakonfigurován pro aplikaci.

- **Přihlášení založené** na hlavičkách – Pokud máte aplikaci využívající PingAccess, která podporuje ověřování založené na hlavičkách http, u kterého chcete provést jednotné přihlašování, vyberte tuto hlavičku jednotného přihlašování [založené](application-proxy-configure-single-sign-on-with-ping-access.md) na hlavičkách.

  >[!NOTE]
  >Tato možnost je dostupná jenom v případě, že proxy aplikace a PingAccess jsou nakonfigurované pro aplikaci.

- **Integrované ověřování systému Windows** – při vystavení místního [ověřování systému Windows](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) při vystavení místní aplikace WIA, pro kterou chcete provést jednotné přihlašování, vyberte režim jednotného přihlašování Windows.

  >[!NOTE]
  >Tato možnost je k dispozici pouze v případě, že je pro aplikaci nakonfigurován proxy aplikace.

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Režimy jednotného přihlašování pro aplikace založené na vlastním vývoji

Aplikace, které jste vlastní vytvořili prostřednictvím prostředí pro vlastní vývoj aplikací, také podporují další režimy jednotného přihlašování, které dříve nejsou uvedené, což zahrnuje tyto možnosti:

- Přihlašování založené na protokolu [OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)

- Přihlášení založené na [OpenID Connect 1,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)

- Přihlášení založené na [WS-Federation 1,2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html)

- Přihlašování založené na [SAML 2,0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference)

Další informace o tom, jak vytvořit aplikaci, která podporuje tyto režimy jednotného přihlašování, najdete v [příručce pro vývojáře Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide) .

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>Jak nastavit režim jednotného přihlašování aplikace

Chcete-li nastavit režim jednotného přihlašování aplikace, postupujte podle těchto pokynů:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**
1. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.
1. Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.
1. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .
1. Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

1. Vyberte aplikaci, pro kterou chcete nakonfigurovat jednotné přihlašování.
1. Po načtení aplikace klikněte na tlačítko **jednotného přihlašování** z nabídky navigace na levé straně aplikace.

## <a name="how-to-choose-a-provisioning-mode"></a>Jak zvolit režim zřizování

- **Ruční zřizování** – Pokud máte existující [](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#provisioning-modes) účty nebo chcete spravovat účty pro tuto aplikaci mimo Azure AD, vyberte režim ručního zřizování.

- **Automatické zřizování** – vyberte režim [automatického](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#configuring-automatic-user-account-provisioning) **zřizování** , pokud chcete povolit Automatické zřizování na základě rozhraní API a/nebo zrušení zřizování uživatelských účtů pro tuto aplikaci. 

  >[!NOTE]
  >Tato možnost je dostupná jenom pro aplikace v rámci **vybrané** kategorie [Galerie aplikací Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-whats-new-azure-portal).

- **Automatické zřizování založené na SCIM** – pomocí [automatického zřizování založeného na SCIM](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) , pokud vaše aplikace podporuje protokol SCIM pro zjišťování změn uživatelů a skupin, které se automaticky generují pro změny v jakékoli integrované aplikaci s Azure AD 

  >[!NOTE]
  >Tato možnost není uvedená jako konkrétní režim zřizování, ale je ve výchozím nastavení povolená pro všechny aplikace, které jsou integrované s Azure AD.

## <a name="how-to-set-an-applications-provisioning-mode"></a>Jak nastavit režim zřizování aplikace

Chcete-li nastavit režim **zřizování** aplikace, postupujte podle těchto pokynů:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce.**
1. Otevřete **rozšíření Azure Active Directory** kliknutím na **všechny služby** v horní části hlavní navigační nabídky vlevo.
1. Zadejte **"Azure Active Directory**" do vyhledávacího pole filtrovat a vybrat **Azure Active Directory** položky.
1. v navigační nabídce Azure Active Directory vlevo klikněte na **podnikové aplikace** .
1. Klikněte na tlačítko **všechny aplikace** zobrazíte seznam všech aplikací.

   * Pokud nevidíte aplikaci, kterou má zobrazit tady, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací** a nastavit **zobrazit** umožňuje **všechny Aplikace.**

1. Vyberte aplikaci, pro kterou chcete nakonfigurovat zřizování.
1. Jakmile se aplikace načte, klikněte na **zřizování** z navigační nabídky aplikace na levé straně.

## <a name="next-steps"></a>Další postup

[Správa aplikací pomocí Azure Active Directory](what-is-application-management.md)
