---
title: Seznam aplikace v galerii aplikací Azure AD | Dokumenty společnosti Microsoft
description: Přečtěte si, jak v galerii aplikací Azure Active Directory uvést aplikaci, která podporuje jednotné přihlašování.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 12/06/2019
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 969193b2c0843c4eb217d2bdb9f9ad9a40ccf9af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154963"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Uvedení aplikace v galerii aplikací služby Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Tento článek ukazuje, jak uvést aplikaci v galerii aplikací Azure Active Directory (Azure AD), implementovat jednotné přihlašování (SSO) a spravovat výpis.

## <a name="what-is-the-azure-ad-application-gallery"></a>Co je galerie aplikací Azure AD?

- Zákazníci najdou nejlepší možné možnosti jednotného přihlašování.
- Konfigurace aplikace je jednoduchá a minimální.
- Rychlé hledání vyhledá vaši aplikaci v galerii.
- Tuto integraci můžou používat všichni zákazníci azure a služby Azure AD zdarma, základní a prémioví.
- Vzájemní zákazníci získají podrobný kurz konfigurace.
- Zákazníci, kteří používají systém pro správu identit mezi doménami[(SCIM),](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)mohou používat zřizování pro stejnou aplikaci.

## <a name="prerequisites"></a>Požadavky

- Pro federované aplikace (Open ID a SAML/WS-Fed) musí aplikace podporovat model softwaru jako služby (SaaS) pro získání uvedené v galerii aplikací Azure AD. Aplikace galerie rozlehlé sítě musí podporovat více konfigurací zákazníků a nikoli žádného konkrétního zákazníka.
- Pro Open ID Connect aplikace musí být víceklientské a [rozhraní souhlasu Azure AD](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) musí být správně implementováno pro aplikaci. Uživatel může odeslat požadavek na přihlášení do společného koncového bodu, aby každý zákazník mohl poskytnout souhlas s aplikací. Přístup uživatele můžete řídit na základě ID klienta a hlavního uživatele přijatého v tokenu.
- Pro SAML 2.0/WS-Fed, vaše aplikace musí mít schopnost provést saml/WS-Fed sso integrace v režimu SP nebo IDP. Před odesláním žádosti se ujistěte, že tato funkce funguje správně.
- Pro heslo jednotného přihlašování, ujistěte se, že vaše aplikace podporuje ověřování formuláře tak, aby úložiště hesel lze provést získat jednotné přihlášení do práce podle očekávání.
- Potřebujete trvalý účet pro testování s nejméně dvěma registrovanými uživateli.

**Jak získat Azure AD pro vývojáře?**

Můžete získat bezplatný testovací účet se všemi prémiovými funkcemi Azure AD – 90 dní zdarma a můžete se rozšířit, pokud s ním pracujete tak dlouho:https://docs.microsoft.com/office/developer-program/office-365-developer-program

## <a name="submit-the-request-in-the-portal"></a>Odeslat žádost na portálu

Po otestování, že integrace aplikací funguje s Azure AD, odešlete žádost o přístup na [portálu Aplikační síť](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Pokud máte účet Office 365, použijte ho k přihlášení k tomuto portálu. Pokud ne, přihlaste se pomocí účtu Microsoft, například Outlooku nebo Hotmailu.

Pokud se po přihlášení zobrazí následující stránka, obraťte se na [tým integrace služby Azure AD SSO](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Zadejte e-mailový účet, který chcete použít pro odeslání žádosti. Tým Azure AD přidá účet na portálu Microsoft Application Network.

![Zpráva o žádosti o přístup na sharepointovém portálu](./media/howto-app-gallery-listing/errorimage.png)

Po přidání účtu se můžete přihlásit k portálu Microsoft Application Network.

Pokud se po přihlášení zobrazí následující stránka, zadejte obchodní odůvodnění, že potřebujete přístup do textového pole. Pak vyberte **požádat o přístup**.

  ![Pole Obchodní odůvodnění na SharePoint portal](./media/howto-app-gallery-listing/accessrequest.png)

Náš tým kontroluje podrobnosti a poskytuje vám přístup odpovídajícím způsobem. Po schválení žádosti se můžete přihlásit k portálu a odeslat jej výběrem dlaždice **Odeslat žádost (ISV)** na domovské stránce.

![Dlaždice Odeslat žádost (ISV) na domovské stránce](./media/howto-app-gallery-listing/homepage.png)

## <a name="issues-on-logging-into-portal"></a>Problémy s přihlášením na portál

Pokud vidíte tuto chybu při přihlašování, pak zde jsou podrobnosti o problému a to je, jak ji můžete opravit.

* Pokud bylo vaše přihlášení zablokováno, jak je znázorněno níže:

  ![problémy s řešením aplikace v galerii](./media/howto-app-gallery-listing/blocked.png)

**Co se děje:**

Uživatel typu Host je federován domácímu tenantovi, který je také Azure AD. Uživatel typu Host je ve vysokém riziku. Společnost Microsoft neumožňuje vysoce rizikovým uživatelům přístup ke svým prostředkům. Všichni vysoce rizikoví uživatelé (zaměstnanci nebo hosté / dodavatelé) musí napravit nebo uzavřít své riziko pro přístup k prostředkům společnosti Microsoft. Pro uživatele typu Host toto uživatelské riziko pochází z domácího tenanta a zásady pochází z klienta prostředků (Microsoft v tomto případě).
 
**Bezpečná řešení:**

* MFA registrovaných uživatelů typu Host napravit své vlastní uživatelské riziko. To lze provést uživatelem typu Host, který provádíhttps://aka.ms/sspr) zabezpečenou změnu nebo resetování hesla (u svého domovského tenanta (to potřebuje vícefaktorové ověřování a sspr v domácím tenantovi). Zabezpečené heslo změnit nebo obnovit musí být zahájeno ve službě Azure AD a ne on-prem.

* Uživatelé typu Host mají své administrátory, kteří své riziko nastoupí. V takovém případě správce provede resetování hesla (dočasné generování hesla). To nepotřebuje ochranu identity. Správce uživatele hosta může https://aka.ms/RiskyUsers přejít a kliknout na "Resetovat heslo".

* Uživatelé typu Host mají své administrátory blízko / odmítají své riziko. Opět to nepotřebuje ochranu identity. Správce může přejít https://aka.ms/RiskyUsers a kliknout na 'Odmítnout riziko uživatele'. Nicméně, admin musí udělat due diligence, aby zajistily, že to bylo falešně pozitivní hodnocení rizikpřed uzavřením uživatelskériziko. V opačném případě ohrožují své zdroje a zdroje společnosti Microsoft tím, že potlačují posouzení rizik bez šetření.

> [!NOTE]
> Pokud máte nějaké problémy s přístupem, obraťte se na [tým integrace přihlašujícího službu Azure AD .](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)

## <a name="implement-sso-by-using-the-federation-protocol"></a>Implementace stehovací sazí pomocí federačního protokolu

Chcete-li uvést aplikaci v galerii aplikací Azure AD, musíte nejprve implementovat jeden z následujících federačních protokolů podporovaných službou Azure AD. Musíte také souhlasit s podmínkami galerie aplikací Azure AD. Přečtěte si smluvní podmínky galerie aplikací Azure AD na [tomto webu](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/).

- **OpenID Connect**: Chcete-li integrovat aplikaci s Azure AD pomocí protokolu Open ID Connect, postupujte [podle pokynů vývojářů](v1-authentication-scenarios.md).

    ![Výpis aplikace OpenID Connect v galerii](./media/howto-app-gallery-listing/openid.png)

    * Pokud chcete přidat aplikaci do seznamu v galerii pomocí OpenID Connect, vyberte **OpenID Connect & OAuth 2.0,** jak je znázorněno na obrázku.
    * Pokud máte nějaké problémy s přístupem, obraťte se na [tým integrace přihlašujícího službu Azure AD .](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)

- **SAML 2.0** nebo **WS-Fed**: Pokud vaše aplikace podporuje SAML 2.0, můžete ji integrovat přímo s tenantem Azure AD podle [pokynů pro přidání vlastní aplikace](../active-directory-saas-custom-apps.md).

  ![Výpis SAML 2.0 nebo WS-Fed aplikace v galerii](./media/howto-app-gallery-listing/saml.png)

  * Pokud chcete přidat aplikaci do seznamu v galerii pomocí **SAML 2.0** nebo **WS-Fed**, vyberte **SAML 2.0/WS-Fed,** jak je znázorněno.

  * Pokud máte nějaké problémy s přístupem, obraťte se na [tým integrace přihlašujícího službu Azure AD .](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)

## <a name="implement-sso-by-using-the-password-sso"></a>Implementujte služby SSO pomocí hesla s přiizačním zabezpečení

Vytvořte webovou aplikaci, která má přihlašovací stránku HTML pro konfiguraci [jednotného přihlášení založeného na heslech](../manage-apps/what-is-single-sign-on.md). Zabezpečení založené na heslech, označované také jako trezor hesel, umožňuje spravovat přístup uživatelů a hesla k webovým aplikacím, které nepodporují federaci identit. Je také užitečné pro scénáře, ve kterých několik uživatelů potřebuje sdílet jeden účet, například s účty aplikací sociálních médií vaší organizace.

![Výpis hesla aplikace s přihlašování do galerie](./media/howto-app-gallery-listing/passwordsso.png)

* Pokud chcete přidat aplikaci do seznamu v galerii pomocí hesla s přihlašující, vyberte **heslo při hlášce.**
* Pokud máte nějaké problémy s přístupem, obraťte se na [tým integrace přihlašujícího službu Azure AD .](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)

## <a name="request-for-user-provisioning"></a>Požadavek na zřizování uživatelů

Postupujte podle procesu uvedeného na následujícím obrázku požádat uživatele zřizování.

   ![Požadavek na zřizování uživatelů](./media/howto-app-gallery-listing/user-provisioning.png)

## <a name="update-or-remove-an-existing-listing"></a>Aktualizace nebo odebrání existujícího výpisu

Chcete-li aktualizovat nebo odebrat existující aplikaci v galerii aplikací Azure AD, musíte nejprve odeslat požadavek na [portálu Aplikační síť](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Pokud máte účet Office 365, použijte ho k přihlášení k tomuto portálu. Pokud ne, přihlaste se pomocí účtu Microsoft, například Outlooku nebo Hotmailu.

- Vyberte vhodnou volbu, jak je znázorněno na následujícím obrázku.

    ![Výpis aplikace SAML v galerii](./media/howto-app-gallery-listing/updateorremove.png)

    * Chcete-li aktualizovat existující aplikaci, vyberte příslušnou možnost podle vašeho požadavku.
    * Pokud chcete odebrat existující aplikaci z galerie aplikací Azure AD, vyberte **Odebrat výpis aplikace z galerie**.
    * Pokud máte nějaké problémy s přístupem, obraťte se na [tým integrace přihlašujícího službu Azure AD .](<mailto:SaaSApplicationIntegrations@service.microsoft.com>)

## <a name="list-requests-by-customers"></a>Požadavky na seznam od zákazníků

Zákazníci mohou odeslat žádost o seznam aplikací výběrem **požadavků na aplikace od zákazníků** > **Odeslat nový požadavek**.

![Zobrazuje dlaždici aplikací požadovaných zákazníkem.](./media/howto-app-gallery-listing/customer-submit-request.png)

Zde je tok aplikací požadovaných zákazníkem.

![Zobrazuje tok aplikací požadovaných zákazníkem.](./media/howto-app-gallery-listing/customerrequest.png)

## <a name="timelines"></a>Časové osy

Časová osa pro proces výpisu aplikace SAML 2.0 nebo WS-Fed v galerii je 7 až 10 pracovních dnů.

  ![Časová osa pro zařazení aplikace SAML do galerie](./media/howto-app-gallery-listing/timeline.png)

Časová osa procesu uvedení aplikace OpenID Connect v galerii je 2 až 5 pracovních dnů.

  ![Časová osa pro uvedení aplikace OpenID Connect v galerii](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Eskalace

Pro jakékoli eskalace, pošlete e-mail na SaaSApplicationIntegrations@service.microsoft.com [Azure AD týmu integrace přispoňování](mailto:SaaSApplicationIntegrations@service.microsoft.com) na , a budeme reagovat co nejdříve.
