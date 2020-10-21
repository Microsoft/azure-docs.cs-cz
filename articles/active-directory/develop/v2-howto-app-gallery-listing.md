---
title: Publikování aplikace v galerii aplikací Azure AD
description: Naučte se, jak zobrazit seznam aplikací, které podporují jednotné přihlašování v galerii aplikací Azure Active Directory.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/19/2020
ms.author: kenwith
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: 5ade98e04853ae8293f762f237b3b3154c876f7e
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92275714"
---
# <a name="publish-your-app-to-the-azure-ad-app-gallery"></a>Publikování aplikace v galerii aplikací Azure AD

Svou aplikaci můžete publikovat v galerii aplikací Azure AD. Po publikování vaší aplikace se zobrazí jako možnost pro zákazníky, když přidávají aplikace do svého tenanta. 

Mezi výhody přidání vaší aplikace do galerie Azure AD patří:

- Zákazníci hledají pro vaši aplikaci nejlepší možnosti jednotného přihlašování.
- Konfigurace aplikace je jednoduchá a minimální.
- Rychlé hledání najde vaši aplikaci v galerii.
- Zákazníci Azure AD, kteří jsou zdarma, Basic a Premium, můžou tuto integraci využívat.
- Mezi vzájemné zákazníky získáte Podrobný kurz konfigurace.

V případě, že vaši zákazníci používají jako poskytovatele identity vaší aplikace službu Azure AD, má navíc spoustu výhod. Zde jsou některá z vylepšení:

- Poskytněte pro uživatele jednotné přihlašování. Pomocí jednotného přihlašování (SSO) snížíte náklady na podporu tím, že zákazníkům usnadníte jednotné přihlašování. Pokud je jednotné přihlašování jedním kliknutím povolené, správci IT nemusí vědět, jak konfigurovat aplikaci pro použití ve své organizaci. Další informace o jednotném přihlašování najdete v tématu [co je jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md).
- Vaše aplikace může být zjistitelná v Microsoft 365 Galerie aplikací, spouštěč Microsoft 365 aplikace a v rámci Microsoft Search on Office.com. 
- Integrovaná správa aplikací. Další informace o správě aplikací ve službě Azure AD najdete v tématu [co je Správa aplikací?](../manage-apps/what-is-application-management.md).
- Vaše aplikace může používat [Graph API](https://docs.microsoft.com/graph/) pro přístup k datům, která řídí produktivitu uživatelů v ekosystému Microsoftu.
- Dokumentace ke konkrétní aplikaci společně s týmem Azure AD pro naše vzájemné zákazníky usnadňuje přijímání.
- Zákazníkům poskytnete možnost plně spravovat ověřování a autorizaci identity svých zaměstnanců a hostů.
- Zadáváme zodpovědnost za správu účtů a dodržování předpisů s vlastníkem zákazníka těchto identit.
- Poskytování možnosti povolit nebo zakázat jednotné přihlašování pro konkrétní poskytovatele identity, skupiny nebo uživatele, aby splnili své obchodní potřeby.
- Zvýšíte svou obchodovatelnost a účinnost. Mnoho velkých organizací vyžaduje, aby jejich zaměstnanci (nebo snažíme) měli bezproblémové prostředí jednotného přihlašování napříč všemi aplikacemi. Zjednodušení jednotného přihlašování je důležité.
- Omezíte tření koncového uživatele, což může zvýšit využití koncovými uživateli a zvýšit vaši tržby.
- Zákazníci, kteří používají systém pro správu identit mezi doménami ([SCIM](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)), můžou použít zřizování pro stejnou aplikaci.
- Přidejte zabezpečení a pohodlí, když se uživatelé přihlásí k aplikacím pomocí jednotného přihlašování Azure AD a odstraňují nutnost samostatných přihlašovacích údajů.

> [!TIP]
> Když nabídnete aplikaci pro použití jinými společnostmi prostřednictvím nákupu nebo předplatného, zpřístupníte ji zákazníkům v jejich vlastních klientech Azure. To se označuje jako vytvoření víceklientské aplikace. Přehled tohoto konceptu najdete v tématu víceklientské [aplikace v Azure a tenant](https://docs.microsoft.com/azure/dotnet-develop-multitenant-applications) [v Azure Active Directory](single-and-multi-tenant-apps.md).

> [!IMPORTANT]
> Pokud chcete publikovat aplikaci v galerii Azure AD, musíte souhlasit s konkrétními podmínkami a ujednáními. Než začnete, nezapomeňte si přečíst [podmínky a ujednání](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/)a souhlasím s nimi.

Postup publikování aplikace v galerii aplikací Azure AD:
1. Vyberte pro vaši aplikaci správný Standard jednotného přihlašování.
2. Implementujte v aplikaci jednotné přihlašování.
3. Vytvořte svého tenanta Azure a otestujte svoji aplikaci.
4. Vytvořte a publikujte dokumentaci.
5. Odešlete svoji aplikaci.
6. Připojte se k programu Microsoft Partner Network.


## <a name="prerequisites"></a>Předpoklady

Budete potřebovat trvalý účet pro testování s alespoň dvěma registrovanými uživateli.


## <a name="step-1---choose-the-right-single-sign-on-standard-for-your-app"></a>Krok 1 – výběr pravého jednotného přihlašování pro aplikaci

Pokud chcete zobrazit seznam aplikací v galerii aplikací Azure AD, musíte implementovat aspoň jednu z podporovaných možností jednotného přihlašování. Pro pochopení možností jednotného přihlašování a způsobu jejich konfigurace ve službě Azure AD se podívejte na téma [Možnosti jednotného přihlašování](../manage-apps/sso-options.md).

V následující tabulce jsou porovnávány hlavní standardy: Otevřete ověřování 2,0 (OAuth 2,0) s OpenID Connect (OIDC), Security Assertion Markup Language (SAML) a specifikace Web Services Federation (WS-dodávání).

| Schopnost| OAuth/OIDC| SAML/WS-Fed |
| - |-|-|
| Webové jednotné přihlašování| √| √ |
| Jednotné odhlašování na základě webu| √| √ |
| Jednotné přihlašování prostřednictvím mobilního telefonu| √| √* |
| Jednotné odhlašování pomocí mobilních zařízení| √| √* |
| Zásady podmíněného přístupu pro mobilní aplikace| √| × |
| Bezproblémové možnosti vícefaktorového ověřování pro mobilní aplikace| √| × |
| Přístup Microsoft Graph| √| × |

*, Ale Microsoft neposkytuje ukázky ani doprovodné materiály.

### <a name="oauth-20-and-openid-connect"></a>OAuth 2.0 a OpenID Connect
OAuth 2,0 je [standardní](https://oauth.net/2/) protokol pro autorizaci. OpenID Connect (OIDC) je [standardní](https://openid.net/connect/) vrstva ověřování identity postavená na protokolu OAuth 2,0. 

**Důvody pro výběr OAuth/OIDC**
- Autorizace, která je na základě těchto protokolů, umožňuje vaší aplikaci přístup k datům uživatelů a organizací s bohatou a integrovanou datovou sadou prostřednictvím rozhraní Microsoft Graph API.
- Zjednodušuje uživatelské prostředí vašich zákazníků při přijímání jednotného přihlašování pro vaši aplikaci. Je možné snadno definovat potřebné sady oprávnění, které se pak automaticky reprezentují pro správce nebo koncového uživatele, který souhlasí.
- Díky těmto protokolům můžou vaši zákazníci používat zásady podmíněného přístupu a Multi-Factor Authentication (MFA) k řízení přístupu k aplikacím. 
- Společnost Microsoft poskytuje knihovny a [ukázky kódu napříč různými technologickými platformami](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) , které pomáhají při vývoji.  

**Některé věci, které je potřeba zvážit**
- Pokud jste už u své aplikace implementovali jednotné přihlašování založené na SAML, možná nebudete chtít implementovat nový standard, abyste mohli aplikaci v galerii získat.

### <a name="saml-20-or-ws-fed"></a>SAML 2,0 nebo WS-Fed

SAML je vyspělý a široce přijatý [Standard jednotného přihlašování](https://www.oasis-open.org/standards#samlv2.0) pro webové aplikace. Další informace o tom, jak Azure používá SAML, najdete v tématu [jak Azure používá protokol SAML](active-directory-saml-protocol-reference.md). 

Specifikace Web Services Federation (WS-dodávání) je [Standardní obor](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) obecně používaný pro webové aplikace vyvinuté pomocí platformy .NET.

**Důvody pro výběr SAML**
- SAML 2,0 je vyspělá Standard a většina technologických platforem podporuje open source knihovny pro SAML 2,0. 
- Zákazníkům můžete poskytnout rozhraní pro správu pro konfiguraci jednotného přihlašování SAML. Můžou nakonfigurovat jednotné přihlašování SAML pro Microsoft Azure AD a libovolného jiného poskytovatele identity, který podporuje SAML.

**Některé věci, které je potřeba zvážit**
- Při použití protokolů SAML 2,0 nebo WSFed pro mobilní aplikace budou mít určité zásady podmíněného přístupu, včetně služby Multi-Factor Authentication (MFA), omezené prostředí.
- Pokud chcete získat přístup k Microsoft Graph, bude nutné implementovat autorizaci prostřednictvím OAuth 2,0 a vygenerovat potřebné tokeny. 

### <a name="password-based"></a>Založené na heslech
Jednotné přihlašování založené na heslech, označované taky jako trezor hesel, umožňuje spravovat přístup uživatelů a hesla k webovým aplikacím, které nepodporují federaci identit. Je to také užitečné ve scénářích, ve kterých několik uživatelů potřebuje sdílet jeden účet, jako jsou například účty aplikací sociálních médií vaší organizace.


## <a name="step-2---implement-single-sign-on-in-your-app"></a>Krok 2 – implementace jednotného přihlašování v aplikaci
Každá aplikace v galerii musí implementovat jednu z podporovaných možností jednotného přihlašování. Další informace o podporovaných možnostech najdete v tématu [Možnosti jednotného přihlašování](../manage-apps/sso-options.md).

Informace o OAuth a OIDC najdete v tématu [pokyny k vzorům ověřování](v2-app-types.md) a [ukázkám kódu Azure Active Directory](sample-v2-code.md).

V případě SAML a WS-krmen musí vaše aplikace mít možnost provádět integraci jednotného přihlašování v režimu SP nebo IDP. Před odesláním žádosti zajistěte, aby tato funkce fungovala správně.

Další informace o ověřování najdete v tématu [co je ověřování?](../azuread-dev/v1-authentication-scenarios.md).

> [!IMPORTANT]
> Pro federované aplikace (OpenID a SAML/WS) musí aplikace podporovat model softwaru jako služby (SaaS). Aplikace Galerie Azure AD musí podporovat více zákaznických konfigurací a neměly by být specifické pro žádného jednoho zákazníka.

### <a name="implement-oauth-20-and-openid-connect"></a>Implementace OAuth 2,0 a OpenID Connect

Pro OpenID Connect musí být aplikace založená na víceklientské úrovni a [rozhraní pro vyjádření souhlasu Azure AD](consent-framework.md) musí být pro aplikaci správně implementované. Uživatel může odeslat žádost o přihlášení ke společnému koncovému bodu, aby každý zákazník mohl poskytnout souhlas k aplikaci. Můžete řídit přístup uživatelů na základě ID tenanta a hlavního názvu uživatele (UPN) přijatého v tokenu.

Konkrétní příklady najdete v tématu [ukázky kódu Microsoft Identity Platform](sample-v2-code.md). 

Příklady specifických pro mobilní zařízení najdete v těchto tématech: 
* [Android](quickstart-v2-android.md)
* [iOS](quickstart-v2-ios.md)
* [Univerzální platforma Windows](quickstart-v2-uwp.md)

### <a name="implement-saml-20"></a>Implementace SAML 2,0

Pokud vaše aplikace podporuje SAML 2,0, můžete ji integrovat přímo s klientem služby Azure AD. Další informace o konfiguraci SAML ve službě Azure AD najdete v tématu [Konfigurace jednotného přihlašování založeného na SAML](../manage-apps/configure-saml-single-sign-on.md).

Microsoft neposkytuje ani nedoporučuje knihovny pro implementace SAML. K dispozici je celá řada Open Source knihoven.

### <a name="implement-ws-fed"></a>Implementovat WS-Fed
Další informace o WS-Fed v ASP.NET Core najdete v tématu [ověřování uživatelů pomocí WS-Federation v ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/authentication/ws-federation).

### <a name="implement-password-vaulting"></a>Implementace trezoru hesel

Vytvořte webovou aplikaci, která má přihlašovací stránku HTML. Zajistěte, aby vaše aplikace podporovala ověřování formuláře, aby bylo možné provést jednotné přihlašování, aby bylo zajištěno fungování jednotného přihlašování podle očekávání.


## <a name="step-3---create-your-azure-tenant-and-test-your-app"></a>Krok 3 – vytvoření tenanta Azure a testování aplikace

K otestování vaší aplikace budete potřebovat tenanta Azure AD. Informace o nastavení vývojového prostředí najdete v tématu [rychlý Start: nastavení tenanta](quickstart-create-new-tenant.md).

Další možností je, že tenant služby Azure AD přináší každé předplatné Microsoft 365. Pokud chcete nastavit bezplatné Microsoft 365 vývojové prostředí, přečtěte si téma [zapojení do programu Microsoft 365 Developer](https://docs.microsoft.com/office/developer-program/microsoft-365-developer-program).

Jakmile budete mít tenanta, musíte povolit a otestovat přístup pro jednotné přihlašování. 

V **případě aplikací OIDC nebo Oath** [Zaregistrujte svoji aplikaci](quickstart-register-app.md) jako víceklientské aplikace. V části Podporované typy účtů vyberte účty v možnosti organizační adresář a osobní účet Microsoft.

**Pro aplikace založené na SAML a WS-based**můžete [nakonfigurovat jednotné přihlašování založené na SAML](../manage-apps/configure-saml-single-sign-on.md) pomocí obecné šablony SAML v Azure AD.

V případě potřeby můžete také [převést aplikaci s jedním klientem na více tenantů](howto-convert-app-to-be-multi-tenant.md) .


## <a name="step-4---create-and-publish-documentation"></a>Krok 4 – Vytvoření a publikování dokumentace

### <a name="documentation-on-your-site"></a>Dokumentace na vašem webu

Snadné přijetí je významným faktorem při rozhodování podnikového softwaru. Jasná dokumentace, která se dá snadno sledovat, podporuje vaše zákazníky v cestě k přijetí a snižuje náklady na podporu. Při práci s tisíci výrobců softwaru si Microsoft viděli, co funguje.

Doporučujeme, aby vaše dokumentace na webu obsahovala minimálně následující položky.

* Seznámení s funkcemi jednotného přihlašování
  * Podporované protokoly
  * Verze a SKU
  * Seznam podporovaných zprostředkovatelů identity s odkazy na dokumentaci
* Licenční informace pro vaši aplikaci
* Řízení přístupu na základě role pro konfiguraci jednotného přihlašování
* Kroky konfigurace jednotného přihlašování
  * Prvky konfigurace uživatelského rozhraní pro SAML s očekávanými hodnotami od poskytovatele
  * Informace o poskytovateli služeb, které se mají předat zprostředkovatelům identity
* Pokud OIDC/OAuth
  * Seznam oprávnění vyžadovaných pro vyjádření souhlasu s obchodními odůvodněními
* Postup testování pro uživatele pilotního nasazení
* Informace o řešení potíží, včetně chybových kódů a zpráv
* Mechanismy podpory pro zákazníky

### <a name="documentation-on-the-microsoft-site"></a>Dokumentace na webu společnosti Microsoft

Při vytváření seznamu aplikací pomocí Azure Active Directory Galerie aplikací, která také publikuje vaši aplikaci v Azure Marketplace, společnost Microsoft vygeneruje dokumentaci pro naše vzájemné zákazníky, kteří vysvětlují podrobný proces. [Tady](https://aka.ms/appstutorial)vidíte příklad. Tato dokumentace je vytvořena na základě odeslání do galerie a můžete ji snadno aktualizovat, pokud provedete změny aplikace pomocí účtu GitHub.


## <a name="step-5---submit-your-app"></a>Krok 5 – odeslání aplikace

Po otestování, že integrace aplikace funguje se službou Azure AD, odešlete žádost o aplikaci na [portál Microsoft Application Network](https://microsoft.sharepoint.com/teams/apponboarding/Apps).

Při prvním pokusu o přihlášení k portálu se zobrazí jedna ze dvou obrazovek. 

Pokud se zobrazí zpráva "že nefungovala", budete muset kontaktovat [integrační tým jednotného přihlašování služby Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Zadejte e-mailový účet, který chcete použít k odeslání žádosti. Je upřednostňována obchodní e-mailová adresa `name@yourbusiness.com` . Tým Azure AD přidá účet na portál Microsoft Application Network.

Pokud se zobrazí stránka "žádost o přístup", zadejte obchodní odůvodnění a vyberte **požádat o přístup**.

Po přidání účtu se můžete přihlásit k portálu Microsoft Application Network a odeslat žádost tak, že na domovské stránce vyberete dlaždici **Odeslat žádost (ISV)** .

![Dlaždice odeslat žádost (ISV) na domovské stránce](./media/howto-app-gallery-listing/homepage.png)

### <a name="issues-on-logging-into-portal"></a>Problémy při přihlašování do portálu

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

### <a name="implementation-specific-options"></a>Možnosti specifické pro implementaci
Pokud chcete přidat aplikaci do seznamu v galerii pomocí OpenID Connect, vyberte **OpenID připojit & OAuth 2,0** , jak je znázorněno na obrázku.

![Výpis aplikace OpenID Connect v galerii](./media/howto-app-gallery-listing/openid.png)

Pokud chcete přidat aplikaci do seznamu v galerii pomocí **SAML 2,0** nebo **WS-** dodávání, vyberte **SAML 2.0/WS-** dodaný, jak je znázorněno na obrázku.

![Výpis aplikace SAML 2,0 nebo WS-Fed v galerii](./media/howto-app-gallery-listing/saml.png)

Pokud chcete přidat aplikaci do seznamu v galerii pomocí jednotného přihlašování k heslům, vyberte **heslo SSO (UserName & heslo)** , jak je znázorněno na obrázku.

![Výpis aplikace pro jednotné přihlašování do galerie](./media/howto-app-gallery-listing/passwordsso.png)

Pokud implementujete koncový bod SCIM 2,0 pro zřizování uživatelů, vyberte možnost, jak je zobrazeno. 

   ![Požadavek na zřizování uživatelů](./media/howto-app-gallery-listing/user-provisioning.png)

### <a name="update-or-remove-an-existing-listing"></a>Aktualizace nebo odebrání existujícího výpisu

Existující aplikaci Galerie můžete aktualizovat nebo odebrat na [portálu Microsoft Application Network](https://microsoft.sharepoint.com/teams/apponboarding/Apps).

![Výpis aplikace SAML v galerii](./media/howto-app-gallery-listing/updateorremove.png)

> [!NOTE]
> Pokud máte s přístupem nějaké problémy, přečtěte si předchozí část vytvoření účtu. Pokud to nepomůže, obraťte se na [integrační tým jednotného přihlašování služby Azure AD](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).


### <a name="timelines"></a>Časové osy

Časová osa pro proces výpisu aplikace SAML 2,0 nebo WS-Fed v galerii je 7 až 10 pracovních dnů.

![Časová osa pro výpis aplikace SAML v galerii](./media/howto-app-gallery-listing/timeline.png)

Časová osa pro proces výpisu aplikace OpenID Connect v galerii je 2 až 5 pracovních dnů.

![Časová osa pro výpis aplikace OpenID Connect v galerii](./media/howto-app-gallery-listing/timeline2.png)

### <a name="escalations"></a>Eskalací zákazníka

Pro všechny eskalace odešlete e-mail do [týmu pro integraci jednotného přihlašování služby Azure AD](mailto:SaaSApplicationIntegrations@service.microsoft.com)a budeme co nejdříve reagovat.


## <a name="step-6---join-the-microsoft-partner-network"></a>Krok 6 – připojení k síti Microsoft Partner Network
Microsoft Partner Network poskytuje okamžitý přístup k exkluzivním prostředkům, programům, nástrojům a připojením. Pokud se chcete připojit k síti a vytvořit svůj přejít k plánu trhu, přečtěte si téma věnované [přístupu ke komerčním zákazníkům](https://partner.microsoft.com/explore/commercial#gtm).


## <a name="next-steps"></a>Další kroky
* [Vytvoření koncového bodu SCIM a konfigurace zřizování uživatelů](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Scénáře ověřování pro Azure AD](authentication-flows-app-scenarios.md)
