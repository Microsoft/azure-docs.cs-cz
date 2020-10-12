---
title: Vypsání aplikace v galerii aplikací Azure AD | Microsoft Docs
description: Přečtěte si, jak zobrazit seznam aplikací, které podporují jednotné přihlašování v galerii aplikací Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: how-to
ms.workload: identity
ms.date: 08/20/2020
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 40d299049a0a1bbff81c412a5beb84f28e64ff46
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90706298"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Uvedení aplikace v galerii aplikací služby Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Tento článek ukazuje, jak zobrazit seznam aplikací v galerii aplikací Azure Active Directory (Azure AD), implementovat jednotné přihlašování (SSO) a spravovat výpis.

## <a name="what-is-the-azure-ad-application-gallery"></a>Co je galerie aplikací Azure AD?

- Zákazníci hledají nejlepší možné možnosti jednotného přihlašování.
- Konfigurace aplikace je jednoduchá a minimální.
- Rychlé hledání najde vaši aplikaci v galerii.
- Zákazníci Azure AD, kteří jsou zdarma, Basic a Premium, můžou tuto integraci využívat.
- Mezi vzájemné zákazníky získáte Podrobný kurz konfigurace.
- Zákazníci, kteří používají systém pro správu identit mezi doménami ([SCIM](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)), můžou použít zřizování pro stejnou aplikaci.

## <a name="prerequisites"></a>Požadavky

- Pro federované aplikace (otevřené ID a SAML/WS) musí aplikace podporovat model software jako služba (SaaS), aby mohl být uvedený v galerii aplikací Azure AD. Aplikace podnikové Galerie musí podporovat více zákaznických konfigurací a ne žádného konkrétního zákazníka.
- Pro otevřené ID Connect musí být aplikace víceklientské a musí být pro aplikaci správně implementovaná [Architektura souhlasu služby Azure AD](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) . Uživatel může odeslat žádost o přihlášení ke společnému koncovému bodu, aby každý zákazník mohl poskytnout souhlas k aplikaci. Můžete řídit přístup uživatelů na základě ID tenanta a hlavního názvu uživatele (UPN) přijatého v tokenu.
- V případě SAML 2.0/WS je nutné, aby vaše aplikace měla schopnost provádět integraci jednotného přihlašování SAML/WS v režimu SP nebo IDP. Před odesláním žádosti zajistěte, aby tato funkce fungovala správně.
- V případě jednotného přihlašování k heslu se ujistěte, že vaše aplikace podporuje ověřování prostřednictvím formuláře, aby bylo možné provést jednotné přihlašování, aby bylo možné pracovat s jednotným přihlašováním podle očekávání.
- Budete potřebovat trvalý účet pro testování s alespoň dvěma registrovanými uživateli.

**Jak získat Azure AD pro vývojáře?**

Můžete získat bezplatný zkušební účet se všemi funkcemi služby Azure AD Premium – 90 dní zdarma a prodloužit platnost, pokud s ním budete pracovat: https://docs.microsoft.com/office/developer-program/office-365-developer-program

## <a name="submit-the-request-in-the-portal"></a>Odeslat žádost na portálu

Po otestování, že integrace aplikace funguje se službou Azure AD, odešlete žádost o aplikaci na [portál Microsoft Application Network](https://microsoft.sharepoint.com/teams/apponboarding/Apps).

Pokud se po přihlášení zobrazí následující stránka, obraťte se na [integrační tým jednotného přihlašování služby Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Zadejte e-mailový účet, který chcete použít k odeslání žádosti. Je upřednostňována obchodní e-mailová adresa [name@yourbusiness.com](mailto:name@yourbusiness.com) . Tým Azure AD přidá účet na portál Microsoft Application Network.

![Zpráva s požadavkem na přístup na SharePointovém portálu](./media/howto-app-gallery-listing/errorimage.png)

Po přidání účtu se můžete přihlásit k portálu Microsoft Application Network.

Pokud se po přihlášení zobrazí následující stránka, v textovém poli zadejte obchodní odůvodnění pro potřebu přístupu. Pak vyberte **požádat o přístup**.

  ![Pole obchodní odůvodnění na portálu služby SharePoint](./media/howto-app-gallery-listing/accessrequest.png)

Náš tým si přečte podrobnosti a poskytne jim odpovídající přístup. Po schválení žádosti se můžete přihlásit k portálu a odeslat žádost tak, že na domovské stránce vyberete dlaždici **Odeslat žádost (ISV)** .

![Dlaždice odeslat žádost (ISV) na domovské stránce](./media/howto-app-gallery-listing/homepage.png)

## <a name="issues-on-logging-into-portal"></a>Problémy při přihlašování do portálu

Pokud se tato chyba zobrazuje při přihlašování, pak tady najdete podrobnosti o problému a to je způsob, jak ho opravit.

* Pokud vaše přihlášení bylo blokované, jak je uvedeno níže:

  ![problémy při řešení aplikace v galerii](./media/howto-app-gallery-listing/blocked.png)

**Co se děje:**

Uživatel typu Host je federovaný pro domácího tenanta, který je také Azure AD. Uživatel typu host má vysoké riziko. Microsoft nepovoluje vysokým rizikovým uživatelům přístup k jeho prostředkům. Všichni uživatelé s vysokým rizikem (zaměstnanci nebo hosty/dodavatelé) musí opravit/zavřít své riziko pro přístup k prostředkům Microsoftu. Pro uživatele typu Host přijde toto riziko uživatele z domovského tenanta a zásada pochází z tenanta prostředků (Microsoft v tomto případě).
 
**Zabezpečená řešení:**

* Registrovaní uživatelé typu Host napravují vlastní riziko uživatele. To může provést uživatel typu Host, který provádí zabezpečené změny nebo resetování hesla ( https://aka.ms/sspr) na svém domovském tenantovi (to vyžaduje MFA a SSPR v domovském tenantovi). Zabezpečená změna nebo resetování hesla je nutné iniciovat ve službě Azure AD, nikoli v Prem.

* Uživatelé typu Host mají nápravu svého rizika. V takovém případě správce provede resetování hesla (dočasné generování hesla). Tato nepotřebuje ochranu identity. Správce hostovaného uživatele může přejít na adresu https://aka.ms/RiskyUsers a kliknout na resetovat heslo.

* Uživatelé typu Host mají své riziko zavřít/zavřít. Tento příkaz nepotřebuje ochranu identity. Správce může přejít na adresu https://aka.ms/RiskyUsers a kliknout na tlačítko "Zavřít riziko uživatele". Správce však musí provést v důsledku náležité opatrnosti, aby se zajistilo, že se jedná o falešně pozitivní hodnocení rizika před uzavřením rizika uživatele. V opačném případě se jedná o rizika svých zdrojů a prostředků Microsoftu tím, že potlačí posouzení rizik bez šetření.

> [!NOTE]
> Pokud máte s přístupem nějaké problémy, obraťte se na [integrační tým jednotného přihlašování služby Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-federation-protocol"></a>Implementace jednotného přihlašování pomocí federačního protokolu

Pokud chcete zobrazit seznam aplikací v galerii aplikací Azure AD, musíte nejdřív implementovat jeden z následujících federačních protokolů, které Azure AD podporuje. Musíte také souhlasit s podmínkami a ujednáními v galerii aplikací Azure AD. Přečtěte si podmínky a ujednání Galerie aplikací Azure AD na [tomto webu](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/).

- **OpenID Connect**: Pokud chcete svou aplikaci integrovat s Azure AD pomocí protokolu Open ID Connect, postupujte podle [pokynů pro vývojáře](v1-authentication-scenarios.md).

    ![Výpis aplikace OpenID Connect v galerii](./media/howto-app-gallery-listing/openid.png)

    * Pokud chcete přidat aplikaci do seznamu v galerii pomocí OpenID Connect, vyberte **OpenID připojit & OAuth 2,0** , jak je znázorněno na obrázku.
    * Pokud máte s přístupem nějaké problémy, obraťte se na [integrační tým jednotného přihlašování služby Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

- **Saml 2,0** nebo **WS-** dodaný: Pokud vaše aplikace podporuje SAML 2,0, můžete ji integrovat přímo s klientem Azure AD podle [pokynů pro přidání vlastní aplikace](../manage-apps/view-applications-portal.md).

  ![Výpis aplikace SAML 2,0 nebo WS-Fed v galerii](./media/howto-app-gallery-listing/saml.png)

  * Pokud chcete přidat aplikaci do seznamu v galerii pomocí **SAML 2,0** nebo **WS-** dodávání, vyberte **SAML 2.0/WS-** dodaný, jak je znázorněno na obrázku.

  * Pokud máte s přístupem nějaké problémy, obraťte se na [integrační tým jednotného přihlašování služby Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-password-sso"></a>Implementace jednotného přihlašování pomocí jednotného přihlašování k heslům

Vytvořte webovou aplikaci, která má přihlašovací stránku HTML pro konfiguraci [jednotného přihlašování založeného na heslech](../manage-apps/what-is-single-sign-on.md). Jednotné přihlašování založené na heslech, označované taky jako trezor hesel, umožňuje spravovat přístup uživatelů a hesla k webovým aplikacím, které nepodporují federaci identit. Je to také užitečné ve scénářích, ve kterých několik uživatelů potřebuje sdílet jeden účet, jako jsou například účty aplikací sociálních médií vaší organizace.

![Výpis aplikace pro jednotné přihlašování do galerie](./media/howto-app-gallery-listing/passwordsso.png)

* Pokud chcete přidat aplikaci do seznamu v galerii pomocí jednotného přihlašování k heslům, vyberte **heslo SSO (UserName & heslo)** , jak je znázorněno na obrázku.
* Pokud máte s přístupem nějaké problémy, obraťte se na [integrační tým jednotného přihlašování služby Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="request-for-user-provisioning"></a>Požadavek na zřizování uživatelů

Pro vyžádání zřizování uživatelů použijte postup uvedený na následujícím obrázku.

   ![Požadavek na zřizování uživatelů](./media/howto-app-gallery-listing/user-provisioning.png)

## <a name="update-or-remove-an-existing-listing"></a>Aktualizace nebo odebrání existujícího výpisu

Pokud chcete aktualizovat nebo odebrat existující aplikaci v galerii aplikací Azure AD, musíte ji nejdřív odeslat na [portálu aplikační síť](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Pokud máte pracovní nebo školní účet, použijte ho k přihlášení k tomuto portálu. Pokud ne, přihlaste se pomocí svého účet Microsoft, jako je například Outlook nebo Hotmail.

- Vyberte odpovídající možnost, jak je znázorněno na následujícím obrázku.

    ![Výpis aplikace SAML v galerii](./media/howto-app-gallery-listing/updateorremove.png)

    * Pokud chcete aktualizovat existující aplikaci, vyberte odpovídající možnost podle vašeho požadavku.
    * Pokud chcete odebrat existující aplikaci z Galerie aplikací Azure AD, vyberte **z Galerie odebrat můj seznam aplikací**.
    * Pokud máte s přístupem nějaké problémy, obraťte se na [integrační tým jednotného přihlašování služby Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="list-requests-by-customers"></a>Vypsat žádosti od zákazníků

Zákazníci můžou odeslat žádost o seznam aplikací tak, že si vyberou **žádosti o aplikace od zákazníků**  >  **Odeslat novou žádost**.

![Zobrazuje dlaždici aplikace požadované zákazníky.](./media/howto-app-gallery-listing/customer-submit-request.png)

Tady je postup pro aplikace vyžadované zákazníky.

![Zobrazuje tok aplikací požadovaných zákazníkem.](./media/howto-app-gallery-listing/customerrequest.png)

## <a name="timelines"></a>Časové osy

Časová osa pro proces výpisu aplikace SAML 2,0 nebo WS-Fed v galerii je 7 až 10 pracovních dnů.

  ![Časová osa pro výpis aplikace SAML v galerii](./media/howto-app-gallery-listing/timeline.png)

Časová osa pro proces výpisu aplikace OpenID Connect v galerii je 2 až 5 pracovních dnů.

  ![Časová osa pro výpis aplikace OpenID Connect v galerii](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Eskalací zákazníka

Pro všechny eskalace odešlete e-mail týmu pro [integraci jednotného přihlašování do služby Azure AD](mailto:SaaSApplicationIntegrations@service.microsoft.com) na adrese SaaSApplicationIntegrations@service.microsoft.com a budeme co nejdříve reagovat.