---
title: Uvedení aplikace v galerii aplikací Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak v seznamu aplikaci, která podporuje jednotné přihlašování v galerii aplikací Azure Active Directory
services: active-directory
documentationcenter: dev-center-name
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2018
ms.author: celested
ms.reviewer: elisol, bryanla
ms.custom: aaddev
ms.openlocfilehash: 6e15a2c940778427c953bd0a3d469ef55f7e3187
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2018
ms.locfileid: "45632054"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Uvedení aplikace v galerii aplikací služby Azure Active Directory

## <a name="what-is-the-azure-ad-application-gallery"></a>Co je Galerie aplikací Azure AD?

Azure Active Directory (Azure AD) je služba identit v cloudu. [Galerie aplikací Azure AD](https://azure.microsoft.com/marketplace/active-directory/all/) je v obchodě Azure Marketplace, kde jsou všechny konektory aplikace publikována pro jednotné přihlašování a zřizování uživatelů. Zákazníci, kteří používají Azure AD jako zprostředkovatele identity najít různých konektorů aplikací SaaS publikované. Správci IT přidat konektory v galerii aplikací a potom nakonfigurujte a konektory slouží pro jednotné přihlašování a zřizování. Azure AD podporuje všechny hlavní federace protokoly pro jednotné přihlašování, včetně SAML 2.0, OpenID Connect, OAuth, WS-Fed.

## <a name="what-are-the-benefits-of-listing-an-application-in-the-gallery"></a>Jaké jsou výhody uvedení aplikace v galerii?

*  Zákazníci najdou nejlepšího možného jednotné přihlašovací prostředí.

*  Konfigurace aplikace je jednoduché a minimální.

*  Rychlé vyhledávání vyhledá aplikace v galerii.

*  Free, Basic, a všechny služby Azure AD Premium zákazníci můžou využít této integrace.

*  Společných zákazníků získejte návod krok za krokem konfigurace.

*  Zákazníci, kteří používají SCIM můžete použít zřizování pro stejnou aplikaci.

## <a name="prerequisites"></a>Požadavky

- Pro aplikace federativní (Open ID a SAML/WS-Fed) musí aplikace podporovat model SaaS pro dostat na seznam v galerii Azure AD. Podnikové aplikace Galerie by měl podporovat konfigurací s víc zákazníků a nikoli konkrétního zákazníka.

- Pro Open ID Connect, by měla být aplikace s více klienty a [rozhraní Azure AD pro udělování souhlasu](quickstart-v1-integrate-apps-with-azure-ad.md#overview-of-the-consent-framework) by měla být správně implementována pro aplikaci. Uživatel můžete poslat žádost o přihlášení na společný koncový bod tak, aby každý zákazník se může poskytnout souhlas pro aplikaci. Můžete řídit přístup uživatelů na základě ID tenanta a hlavním názvem uživatele přijata v tokenu.

- Pro SAML 2.0 a WS-Fed vaše aplikace potřebuje mít možnost provádět integraci jednotného přihlašování SAML/WS-Fed v režimu SP nebo zprostředkovatele identity. Ujistěte se prosím, že to funguje správně před odesláním požadavku.

- Pro jednotné přihlašování heslem Ujistěte se prosím, že vaše aplikace podporuje ověřování formuláře tak, aby ukládání hesel do trezoru můžete udělat plnit jednotné přihlašování podle očekávání.

- Pro uživatele automatické zřizování požadavky aplikace by měly být uvedeny v galerii se jednotné přihlašování povolenou funkci pomocí kteréhokoli z protokolu federace je popsáno výše. Můžete požádat pro jednotné přihlašování a zřizování společně na portálu, pokud je ještě nejsou uvedená uživatelů.

## <a name="submit-the-request-in-the-portal"></a>Odeslání požadavku na portálu

Jakmile jste otestovali, integraci vašich aplikací funguje s Azure AD, odeslat žádost o přístup na naše [sítě portál Application](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Pokud máte účet Office 365, použijte pro přihlášení k tomuto portálu. Pokud ne, přihlaste se pomocí účtu Microsoft (jako je Outlook nebo Hotmail).

Pokud se po přihlášení se zobrazí následující stránka, obraťte se [týmu Integrace jednotného přihlašování k Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) a zadejte e-mailový účet, který chcete použít pro odeslání žádosti. Tým služby Azure AD se pak přidejte účet na portálu Microsoft aplikace sítě.

![Žádost o přístup na portál SharePoint](./media/howto-app-gallery-listing/errorimage.png)

Po přidání účtu můžete přihlásit k portálu Microsoft aplikace sítě.

A pokud po přihlášení se zobrazí následující stránka, uveďte obchodní odůvodnění pro by potřebovali mít přístup do textového pole a poté vyberte **žádost o přístup**.

  ![Žádost o přístup na portál SharePoint](./media/howto-app-gallery-listing/accessrequest.png)

Náš tým revizí podrobností a získáte přístup k odpovídajícím způsobem. Po schválení vaší žádosti může přihlásit k portálu a odešlete žádost kliknutím **odeslat požadavek (ISV)** dlaždici na domovské stránce formuláře.

![Portál SharePoint domovské stránky](./media/howto-app-gallery-listing/homepage.png)

> [!NOTE]
> Pokud máte problémy týkající se přístup, obraťte se [týmu Integrace jednotného přihlašování k Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implementing-sso-using-federation-protocol"></a>Implementaci jednotného přihlašování pomocí protokolu federace

Pro zobrazení seznamu aplikace v galerii aplikací Azure AD, musíte nejprve implementovat jedno z následujících protokolů federace podporuje Azure AD a souhlasím s podmínkami Galerie aplikací Azure AD a podmínky. Přečtěte si podmínky a ujednání Galerie aplikací Azure AD z [tady](https://azure.microsoft.com/en-us/support/legal/active-directory-app-gallery-terms/).

*   **OpenID Connect**: K integraci vaší aplikace s Azure AD pomocí protokolu Open ID Connect, postupujte [vývojářů pokyny](authentication-scenarios.md).

    ![Časová osa uvedení aplikace v galerii s OpenID Connect](./media/howto-app-gallery-listing/openid.png)

    * Pokud chcete přidat aplikace do seznamu v galerii pomocí OpenID Connect, vyberte **OpenID Connect a OAuth 2.0** jak je uvedeno výše.

    * Pokud máte problémy týkající se přístup, obraťte se [týmu Integrace jednotného přihlašování k Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

*   **Protokol SAML 2.0** nebo **WS-Fed**: Pokud vaše aplikace podporuje SAML 2.0, můžete ji integrovat přímo s tenantem Azure AD s použitím [pokynů a přidejte vlastní aplikaci](../active-directory-saas-custom-apps.md).

    ![Časová osa uvedení aplikace SAML 2.0 nebo WS-Fed do Galerie](./media/howto-app-gallery-listing/saml.png)

    * Pokud chcete přidat aplikace do seznamu v galerii pomocí **SAML 2.0** nebo **WS-Fed**vyberte **SAMl 2.0 a WS-Fed** jak je uvedeno výše.

    * Pokud máte problémy týkající se přístup, obraťte se [týmu Integrace jednotného přihlašování k Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implementing-sso-using-password-sso"></a>Implementaci jednotného přihlašování pomocí hesla jednotného přihlašování

Vytvoření webové aplikace, který má přihlašovací stránku HTML konfigurace [založené na heslech jednotného přihlašování](../manage-apps/what-is-single-sign-on.md). Jednotné přihlašování založené na heslech, také označuje jako heslo vaulting, umožňuje spravovat přístup uživatelů a hesel do webové aplikace, které nepodporují federace identit. Je také užitečné pro scénáře, ve kterých několik uživatelé potřebují sdílet jeden účet, jako například účtům sociálních médií aplikace vaší organizace.

![Časová osa uvedení jednotného přihlašování heslo aplikace do Galerie](./media/howto-app-gallery-listing/passwordsso.png)

* Pokud chcete přidat aplikace do seznamu v galerii pomocí jednotného přihlašování k heslo, vyberte **jednotné přihlašování heslem** jak je uvedeno výše.

* Pokud máte problémy týkající se přístup, obraťte se [týmu Integrace jednotného přihlašování k Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="updateremove-existing-listing"></a>Aktualizace nebo odebrání existující seznam

Aktualizujte nebo odeberte existující aplikace v galerii aplikací Azure AD, musíte nejprve k odeslání žádosti o v [portál Application Network](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Pokud máte účet Office 365, použijte pro přihlášení k tomuto portálu. Pokud ne, přihlaste se pomocí účtu Microsoft (jako je Outlook nebo Hotmail).

* Vyberte příslušnou možnost obrázku níže

    ![Časová osa uvedení aplikace saml do Galerie](./media/howto-app-gallery-listing/updateorremove.png)

    * Pokud chcete aktualizovat existující aplikaci, vyberte **aktualizovat existující aplikace výpis**.

    * Pokud chcete odebrat existující aplikaci z Galerie Azure AD, vyberte **odebrat existující seznam aplikací**

    * Pokud máte problémy týkající se přístup, obraťte se [týmu Integrace jednotného přihlašování k Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). 

## <a name="timelines"></a>Časové osy

Časová osa pro proces uvedení protokol SAML 2.0 nebo WS-Fed aplikace v galerii je 7 až 10 pracovních dní.

   ![Časová osa uvedení aplikace saml do Galerie](./media/howto-app-gallery-listing/timeline.png)

Časová osa pro proces uvedení aplikace v galerii OpenID Connect se 2 až 5 pracovních dní.

   ![Časová osa uvedení aplikace saml do Galerie](./media/howto-app-gallery-listing/timeline2.png)

Časová osa pro proces uvedení aplikace v galerii se podpora zřizování uživatelů se 40-45 pracovních dnů.

   ![Časová osa uvedení aplikace saml do Galerie](./media/howto-app-gallery-listing/provisioningtimeline.png)

## <a name="escalations"></a>Eskalaci

Žádné eskalaci, pošlete e-mail na adresu [týmu Integrace jednotného přihlašování k Azure AD](mailto:SaaSApplicationIntegrations@service.microsoft.com) tedy SaaSApplicationIntegrations@service.microsoft.com a zašleme co nejdříve.