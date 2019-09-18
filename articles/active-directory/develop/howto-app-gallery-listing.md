---
title: Výpis aplikace v galerii aplikací Azure Active Directory | Microsoft Docs
description: Přečtěte si, jak zobrazit seznam aplikací, které podporují jednotné přihlašování v galerii aplikací Azure Active Directory.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2019
ms.author: ryanwi
ms.reviewer: elisol, bryanla
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba4beeff5484f774e801842f06d694af456c4e96
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033755"
---
# <a name="how-to-list-your-application-in-the-azure-active-directory-application-gallery"></a>Postup: Uvedení aplikace v galerii aplikací služby Azure Active Directory

Tento článek ukazuje, jak zobrazit seznam aplikací v galerii aplikací Azure AD, implementovat jednotné přihlašování (SSO) a spravovat výpis.

## <a name="what-is-the-azure-ad-application-gallery"></a>Co je galerie aplikací Azure AD?

- Zákazníci hledají nejlepší možné možnosti jednotného přihlašování.
- Konfigurace aplikace je jednoduchá a minimální.
- Rychlé hledání najde vaši aplikaci v galerii.
- Zákazníci Azure AD, kteří jsou zdarma, Basic a Premium, můžou tuto integraci využívat.
- Mezi vzájemné zákazníky získáte Podrobný kurz konfigurace.
- Zákazníci, kteří používají SCIM, můžou pro stejnou aplikaci použít zřizování.

## <a name="prerequisites"></a>Požadavky

- Pro federované aplikace (otevřené ID a SAML/WS-dodávání) musí aplikace podporovat model SaaS pro získání seznamu v galerii Azure AD. Aplikace podnikové Galerie by měly podporovat více zákaznických konfigurací a ne žádného konkrétního zákazníka.

- V případě otevřeného ID Connect by měla být aplikace založená na víceklientské úrovni a [rozhraní Azure AD pro vyjádření souhlasu](consent-framework.md) by mělo být pro aplikaci správně implementované. Uživatel může odeslat žádost o přihlášení ke společnému koncovému bodu, aby každý zákazník mohl poskytnout souhlas k aplikaci. Můžete řídit přístup uživatelů na základě ID tenanta a hlavního názvu uživatele (UPN) přijatého v tokenu.

- V případě SAML 2.0/WS je nutné, aby vaše aplikace měla schopnost provést integraci jednotného přihlašování SAML/WS v režimu SP nebo IDP. Před odesláním žádosti Prosím zajistěte, aby správně fungovala.

- V případě jednotného přihlašování k heslu zajistěte, aby vaše aplikace podporovala ověřování formuláře, aby bylo možné provést jednotné přihlašování podle očekávání.

- Je potřeba mít stálý účet pro testování s registrovanými alespoň 2 uživateli.

## <a name="submit-the-request-in-the-portal"></a>Odeslat žádost na portálu

Po otestování, že integrace aplikace funguje se službou Azure AD, odešlete žádost o přístup na [portálu aplikační sítě](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Pokud máte účet Office 365, použijte ho pro přihlášení k tomuto portálu. Pokud ne, přihlaste se pomocí svého účet Microsoft (například Outlook nebo Hotmail).

Pokud se po přihlášení zobrazí následující stránka, obraťte se na [integrační tým jednotného přihlašování služby Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) a zadejte e-mailový účet, který chcete použít k odeslání žádosti. Tým Azure AD pak přidá účet na portál Microsoft Application Network.

![Žádost o přístup na SharePointovém portálu](./media/howto-app-gallery-listing/errorimage.png)

Po přidání účtu se můžete přihlásit k portálu Microsoft Application Network.

A pokud po přihlášení se zobrazí následující stránka, v textovém poli zadejte obchodní odůvodnění, v němž budete potřebovat přístup, a pak vyberte **požádat o přístup**.

  ![Žádost o přístup na SharePointovém portálu](./media/howto-app-gallery-listing/accessrequest.png)

Náš tým si přečte podrobnosti a poskytne jim odpovídající přístup. Po schválení žádosti se můžete přihlásit k portálu a odeslat žádost kliknutím na dlaždici **Odeslat žádost (ISV)** na domovské stránce.

![Domovská stránka portálu SharePoint](./media/howto-app-gallery-listing/homepage.png)

> [!NOTE]
> Pokud máte nějaké problémy týkající se přístupu, obraťte se na [tým Integration Team pro jednotné přihlašování služby Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implementing-sso-using-federation-protocol"></a>Implementace jednotného přihlašování pomocí federačního protokolu

Pokud chcete zobrazit seznam aplikací v galerii aplikací Azure AD, musíte nejdřív implementovat jeden z následujících federačních protokolů, které Azure AD podporuje, a vyjádřit souhlas s podmínkami a ujednáními v galerii aplikací Azure AD. Přečtěte si [tady](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)podmínky a ujednání z Galerie aplikací Azure AD.

- **OpenID Connect**: Pokud chcete svou aplikaci integrovat s Azure AD pomocí protokolu Open ID Connect, postupujte podle [pokynů pro vývojáře](authentication-scenarios.md).

    ![Časová osa výpisu aplikace OpenID Connect do galerie](./media/howto-app-gallery-listing/openid.png)

    * Pokud chcete přidat aplikaci do seznamu v galerii pomocí OpenID Connect, vyberte **OpenID připojit & OAuth 2,0** výše.
    * Pokud máte nějaké problémy týkající se přístupu, obraťte se na [tým Integration Team pro jednotné přihlašování služby Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

- **SAML 2,0** nebo **WS-** podáváno: Pokud vaše aplikace podporuje SAML 2,0, můžete ji integrovat přímo s klientem služby Azure AD pomocí [pokynů k přidání vlastní aplikace](../active-directory-saas-custom-apps.md).

  ![Časová osa výpisu aplikace SAML 2,0 nebo WS-nakrmená do galerie](./media/howto-app-gallery-listing/saml.png)

  * Pokud chcete přidat aplikaci do seznamu v galerii pomocí **SAML 2,0** nebo **WS-** dodávání, vyberte **SAML 2.0/WS-** dopředné.
  * Pokud máte nějaké problémy týkající se přístupu, obraťte se na [tým Integration Team pro jednotné přihlašování služby Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implementing-sso-using-password-sso"></a>Implementace jednotného přihlašování pomocí jednotného přihlašování k heslům

Vytvořte webovou aplikaci, která má přihlašovací stránku HTML pro konfiguraci [jednotného přihlašování založeného na heslech](../manage-apps/what-is-single-sign-on.md). Jednotné přihlašování založené na heslech, označované taky jako trezor hesel, umožňuje spravovat přístup uživatelů a hesla k webovým aplikacím, které nepodporují federaci identit. Je to také užitečné ve scénářích, ve kterých si několik uživatelů potřebuje sdílet jeden účet, jako jsou například účty aplikací sociálních médií vaší organizace.

![Časová osa uvedení aplikace jednotného přihlašování k heslu do galerie](./media/howto-app-gallery-listing/passwordsso.png)

* Pokud chcete přidat aplikaci do seznamu v galerii pomocí jednotného přihlašování k heslům, vyberte **heslo SSO** nahoře.
* Pokud máte nějaké problémy týkající se přístupu, obraťte se na [tým Integration Team pro jednotné přihlašování služby Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="updateremove-existing-listing"></a>Aktualizovat nebo odebrat existující výpis

Pokud chcete aktualizovat nebo odebrat existující aplikaci v galerii aplikací Azure AD, musíte ji nejdřív odeslat na [portálu aplikační síť](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Pokud máte účet Office 365, použijte ho pro přihlášení k tomuto portálu. Pokud ne, přihlaste se pomocí svého účet Microsoft (například Outlook nebo Hotmail).

- Vyberte odpovídající možnost, jak je znázorněno na následujícím obrázku:

    ![Časová osa výpisu aplikace SAML do galerie](./media/howto-app-gallery-listing/updateorremove.png)

    * Pokud chcete aktualizovat existující aplikaci, vyberte **aktualizovat existující seznam aplikací**.
    * Pokud chcete odebrat existující aplikaci z Galerie Azure AD, vyberte **odebrat existující seznam aplikací**.
    * Pokud máte nějaké problémy týkající se přístupu, obraťte se na [tým Integration Team pro jednotné přihlašování služby Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

## <a name="listing-requests-by-customers"></a>Výpis požadavků od zákazníků

Zákazníci mohou odeslat žádost o výpis aplikace kliknutím na žádosti o **aplikace od zákazníků** -> **Odeslat novou žádost**.

![Zobrazuje dlaždici aplikace požadované zákazníky.](./media/howto-app-gallery-listing/customer-submit-request.png)

Níže je uvedený tok aplikací požadovaných zákazníky –

![Zobrazuje požadovaný tok aplikací pro zákazníky.](./media/howto-app-gallery-listing/customer-request.png)

## <a name="timelines"></a>Časové osy

Časová osa pro proces výpisu aplikace SAML 2,0 nebo WS-zakrmená v galerii je 7-10 pracovních dnů.

   ![Časová osa výpisu aplikace SAML do galerie](./media/howto-app-gallery-listing/timeline.png)

Časová osa pro proces výpisu aplikace OpenID Connect v galerii je 2-5 pracovních dnů.

   ![Časová osa výpisu aplikace SAML do galerie](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Eskalací zákazníka

Pro všechny eskalace odešlete e-mail týmu pro [integraci jednotného přihlašování služby Azure AD](mailto:SaaSApplicationIntegrations@service.microsoft.com) , který je SaaSApplicationIntegrations@service.microsoft.com a co nejdříve odpovíme.
