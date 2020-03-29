---
title: Jednotné přihlašování k aplikacím – Služba Azure Active Directory | Dokumenty společnosti Microsoft
description: Zjistěte, jak zvolit metodu jednotného přihlášení při konfiguraci aplikací ve službě Azure Active Directory (Azure AD). Používejte jednotné přihlašování, aby si uživatelé nemuseli pamatovat hesla pro každou aplikaci, a zjednodušte správu správy účtů.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: mimart
ms.reviewer: arvindh, japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: f46bcf412403d8f911e484e12a9d1f421b1666f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366066"
---
# <a name="single-sign-on-to-applications-in-azure-active-directory"></a>Jednotné přihlašování k aplikacím ve službě Azure Active Directory

Jednotné přihlašování (SSO) přidává zabezpečení a pohodlí při přihlášení uživatelů k aplikacím ve službě Azure Active Directory (Azure AD). Tento článek popisuje metody jednotného přihlašování a pomáhá při konfiguraci aplikací zvolit nejvhodnější metodu jednotného přihlašování.

- **Při jednotném přihlašování**se uživatelé přihlašují jednou pomocí jednoho účtu, aby měli přístup k zařízením spojeným s doménou, firemním prostředkům, aplikacím softwaru jako službě (SaaS) a webovým aplikacím. Po přihlášení může uživatel spouštět aplikace z portálu Office 365 nebo z přístupového panelu MyApps Azure AD. Správci mohou centralizovat správu uživatelských účtů a automaticky přidávat nebo odebírat přístup uživatelů k aplikacím na základě členství ve skupině.

- **Bez jednotného přihlášení**si uživatelé musí pamatovat hesla specifická pro aplikaci a přihlásit se ke každé aplikaci. Pracovníci IT potřebují vytvářet a aktualizovat uživatelské účty pro každou aplikaci, jako je Office 365, Box a Salesforce. Uživatelé si musí zapamatovat svá hesla a navíc trávit čas přihlášením ke každé aplikaci.

## <a name="choosing-a-single-sign-on-method"></a>Výběr metody jednotného přihlášení

Existuje několik způsobů konfigurace aplikace pro jednotné přihlašování. Výběr metody jednotného přihlášení závisí na tom, jak je aplikace nakonfigurována pro ověřování.

- Cloudové aplikace mohou používat OpenID Connect, OAuth, SAML, metody založené na heslech, propojené nebo zakázané metody pro jednotné přihlašování. 
- Místní aplikace můžou pro jednotné přihlašování používat metody založené na heslech, integrované ověřování systémem Windows, založené na záhlaví, propojené nebo zakázané metody. Místní volby fungují, když jsou aplikace nakonfigurovány pro proxy aplikace.

Tento vývojový diagram vám pomůže rozhodnout, která metoda jednotného přihlášení je pro vaši situaci nejvhodnější.

![Rozhodovací vývojový diagram pro metodu jednotného přihlašování](./media/what-is-single-sign-on/choose-single-sign-on-method-040419.png)

Následující tabulka shrnuje metody jednotného přihlašování a odkazy na další podrobnosti.

| Metoda jednotného přihlašování | Typy aplikací | Kdy je použít |
| :------ | :------- | :----- |
| [OpenID Connect a OAuth](#openid-connect-and-oauth) | pouze mrak | Při vývoji nové aplikace použijte OpenID Connect a OAuth. Tento protokol zjednodušuje konfiguraci aplikací, má snadno použitelné sady SDK a umožňuje aplikaci používat ms graph.
| [SAML](#saml-sso) | cloud a místní | Zvolte SAML, kdykoli je to možné pro existující aplikace, které nepoužívají OpenID Connect nebo OAuth. Saml pracuje pro aplikace, které se ověřují pomocí jednoho z protokolů SAML.|
| [Na základě hesla](#password-based-sso) | cloud a místní | Při ověřování aplikace pomocí uživatelského jména a hesla zvolte heslo. Jednotné přihlašování založené na heslech umožňuje bezpečné ukládání hesel aplikací a přehrávání pomocí rozšíření webového prohlížeče nebo mobilní aplikace. Tato metoda používá existující proces přihlášení poskytované aplikací, ale umožňuje správci spravovat hesla. |
| [Propojené](#linked-sign-on) | cloud a místní | Zvolte propojené přihlášení, když je aplikace nakonfigurována pro jednotné přihlašování v jiné službě poskytovatele identity. Tato možnost nepřidává jednotné přihlašování do aplikace. Aplikace však již může mít jednotné přihlašování implementováno pomocí jiné služby, jako je například služba Active Directory Federation Services.|
| [Disabled](#disabled-sso) (Zakázáno) | cloud a místní | Pokud aplikace není připravená k konfiguraci pro jednotné přihlašování, zvolte zakázané jednotné přihlašování. Tento režim je výchozí při vytváření aplikace.|
| [Integrované ověřování systému Windows (IWA)](#integrated-windows-authentication-iwa-sso) | pouze v místním prostředí | Zvolte jednotné přihlašování IWA pro aplikace, které používají [integrované ověřování systému Windows (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)nebo aplikace podporující deklarace identity. Pro IWA konektory proxy aplikace používají kerberos omezené delegování (KCD) k ověření uživatelů do aplikace. |
| [Založené na záhlaví](#header-based-sso) | pouze v místním prostředí | Jednotné přihlašování založené na záhlaví použijte, když aplikace používá záhlaví pro ověřování. Jednotné přihlašování založené na záhlaví vyžaduje pingaccess pro Azure AD. Proxy aplikace používá Azure AD k ověření uživatele a pak předá provoz prostřednictvím služby konektoru.  |

## <a name="openid-connect-and-oauth"></a>OpenID Connect a OAuth

Při vývoji nových aplikací použijte moderní protokoly, jako je OpenID Connect a OAuth, abyste dosáhli nejlepšího jednotného přihlašování pro vaši aplikaci na různých platformách zařízení. OAuth umožňuje uživatelům nebo správcům [udělit souhlas](configure-user-consent.md) s chráněnými prostředky, jako je [Microsoft Graph](/graph/overview). Pro vaši aplikaci poskytujeme snadné přijetí [sad SDK](../develop/reference-v2-libraries.md) a navíc bude vaše aplikace připravena k použití [Microsoft Graphu](/graph/overview).

Další informace naleznete v tématu:

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [Příručka pro vývojáře platformy microsoft identit](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

## <a name="saml-sso"></a>SAML SSO

S **saml jednotné přihlašování**, Azure AD ověřuje k aplikaci pomocí účtu Azure AD uživatele. Azure AD sděluje přihlašovací informace do aplikace prostřednictvím protokolu připojení. S jediným přihlašování saml můžete mapovat uživatele na konkrétní role aplikací na základě pravidel, která definujete v deklaracích SAML.

Zvolte jednotné přihlašování založené na SAML, když to aplikace podporuje.

Jednotné přihlašování založené na saml je podporováno pro aplikace, které používají některý z těchto protokolů:

- SAML 2.0
- WS-Federation

Pokud chcete nakonfigurovat aplikaci SaaS pro jednotné přihlašování založené na saml, přečtěte si informace [o konfiguraci jednotného přihlašování založeného na SAML](configure-single-sign-on-non-gallery-applications.md). Mnoho aplikací software jako služba (SaaS) má [také kurz specifický pro aplikaci,](../saas-apps/tutorial-list.md) který vás provede konfigurací jednotného přihlašování založenéna saml.

Chcete-li nakonfigurovat aplikaci pro WS-Federation, postupujte podle stejných pokynů pro konfiguraci aplikace pro jednotné přihlašování založené na saml, viz [Konfigurace jednotného přihlašování založeného na SAML](configure-single-sign-on-non-gallery-applications.md). V kroku konfigurace aplikace pro použití Azure AD, budete muset nahradit přihlašovací adresu URL Azure AD pro ws-federation koncového bodu `https://login.microsoftonline.com/<tenant-ID>/wsfed`.

Pokud chcete nakonfigurovat místní aplikaci pro jednotné přihlašování založené na SAML, přečtěte si informace [o jednotném přihlašování SAML pro místní aplikace s proxy aplikací](application-proxy-configure-single-sign-on-on-premises-apps.md).

Další informace o protokolu SAML naleznete v [tématu Single sign-on SAML protocol](../develop/single-sign-on-saml-protocol.md).

## <a name="password-based-sso"></a>Zabezpečení založené na heslech

Při přihlašování pomocí hesla se uživatelé při prvním přístupu k aplikaci přihlašují pomocí uživatelského jména a hesla. Po prvním přihlášení Azure AD poskytuje uživatelské jméno a heslo do aplikace.

Jednotné přihlašování založené na heslech používá existující proces ověřování poskytovaný aplikací. Když povolíte jednotné přihlašování hesla pro aplikaci, Azure AD shromažďuje a bezpečně ukládá uživatelská jména a hesla pro aplikaci. Pověření uživatele jsou uložena v šifrovaném stavu v adresáři.

Zvolte jednotné přihlašování založené na heslech, pokud:

- Aplikace nepodporuje protokol jednotného přihlašování SAML.
- Aplikace se ověřuje pomocí uživatelského jména a hesla namísto přístupových tokenů a záhlaví.

Jednotné přihlašování založené na heslech je podporováno pro všechny cloudové aplikace, které mají přihlašovací stránku založenou na HTML. Uživatel může používat libovolný z následujících prohlížečů:

- Internet Explorer 11 ve Windows 7 nebo novějším
   > [!NOTE]
   > Aplikace Internet Explorer má omezenou podporu a již nepřijímá nové aktualizace softwaru. Microsoft Edge je doporučený prohlížeč.

- Microsoft Edge ve Windows 10 Anniversary Edition nebo novějším
- Microsoft Edge pro iOS a Android
- Intune Managed Browser
- Chrome ve Windows 7 nebo novějším a na MacOS X nebo novějším
- Firefox 26.0 nebo novější v systému Windows XP SP2 nebo novějším a v systému Mac OS X 10.6 nebo novějším

Pokud chcete nakonfigurovat cloudovou aplikaci pro jednotné přihlašování založené na heslech, přečtěte si informace [o konfiguraci jednotného přihlašování pomocí hesla](configure-password-single-sign-on-non-gallery-applications.md).

Pokud chcete nakonfigurovat místní aplikaci pro jednotné přihlašování přes proxy aplikace, přečtěte si informace [o tom, jak se konting hesel přihlašovat pomocí proxy aplikace.](application-proxy-configure-single-sign-on-password-vaulting.md)

### <a name="how-authentication-works-for-password-based-sso"></a>Jak funguje ověřování pro zabezpečení založené na heslech

Chcete-li ověřit uživatele k aplikaci, Azure AD načte pověření uživatele z adresáře a zadá je do přihlašovací stránky aplikace.  Azure AD bezpečně předá pověření uživatele prostřednictvím rozšíření webového prohlížeče nebo mobilní aplikace. Tento proces umožňuje správci spravovat pověření uživatele a nevyžaduje, aby si uživatelé pamatovali své heslo.

> [!IMPORTANT]
> Přihlašovací údaje jsou obfuscated od uživatele během procesu automatického přihlášení. Pověření jsou však zjistitelné pomocí nástrojů pro ladění webu. Uživatelé a správci musí dodržovat stejné zásady zabezpečení, jako kdyby pověření byla zadána přímo uživatelem.

### <a name="managing-credentials-for-password-based-sso"></a>Správa přihlašovacích údajů pro zabezpečení založené na heslech

Hesla pro každou aplikaci může spravovat správce Azure AD nebo uživatelé.

Když správce Azure AD spravuje přihlašovací údaje:  

- Uživatel nemusí resetovat nebo zapamatovat si uživatelské jméno a heslo. Uživatel má přístup k aplikaci kliknutím na něj na svém přístupovém panelu nebo prostřednictvím poskytnutého odkazu.
- Správce může na pověřeních provést úlohy správy. Správce může například aktualizovat přístup k aplikacím podle členství ve skupinách uživatelů a stavu zaměstnance.
- Správce může pomocí pověření pro správu poskytnout přístup k aplikacím sdíleným mezi mnoha uživateli. Správce může například povolit všem uživatelům, kteří mají přístup k aplikaci, přístup k sociálním médiím nebo aplikaci pro sdílení dokumentů.

Když koncový uživatel spravuje pověření:

- Uživatelé mohou spravovat svá hesla aktualizací nebo odstraněním podle potřeby.
- Správci jsou stále schopni nastavit nová pověření pro aplikaci.

## <a name="linked-sign-on"></a>Propojené přihlášení
Propojené přihlášení umožňuje Azure AD poskytovat jednotné přihlašování k aplikaci, která je již nakonfigurovaná pro jednotné přihlašování v jiné službě. Propojená aplikace se může koncovým uživatelům zobrazovat na portálu Office 365 nebo na portálu Azure AD MyApps. Uživatel může například spustit aplikaci, která je nakonfigurovaná pro jednotné přihlašování ve službě AD FS služby Active Directory Federation Services 2.0 (AD FS) z portálu Office 365. Další vytváření sestav je taky dostupné pro propojené aplikace, které se spouštějí z portálu Office 365 nebo portálu MyApps Azure AD. Informace o konfiguraci aplikace pro propojené přihlášení naleznete v [tématu Konfigurace propojeného přihlášení](configure-linked-sign-on.md).

### <a name="linked-sign-on-for-application-migration"></a>Propojené přihlášení pro migraci aplikací

Propojené přihlašování může zajistit konzistentní uživatelské prostředí při migraci aplikací po určitou dobu. Pokud migrujete aplikace do služby Azure Active Directory, můžete pomocí propojeného přihlášení rychle publikovat odkazy na všechny aplikace, které chcete migrovat.  Uživatelé najdou všechny odkazy na [portálu MyApps](../user-help/active-directory-saas-access-panel-introduction.md) nebo ve [spouštěči aplikací Office 365](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a). Uživatelé nebudou vědět, že přistupují k propojené aplikaci nebo k migrované aplikaci.  

Jakmile se uživatel ověří pomocí propojené aplikace, je třeba vytvořit záznam účtu před tím, než je koncovému uživateli poskytnut přístup jednotného přihlášení. Zřizování tohoto záznamu obchodního vztahu může probíhat automaticky nebo může dojít ručně správcem.

## <a name="disabled-sso"></a>Zakázané přisážací nastavení je neaktivován

Zakázaný režim znamená, že jednotné přihlašování se pro aplikaci nepoužívá. Pokud je zakázáno jednotné přihlašování, uživatelé se možná budou muset dvakrát ověřit. Nejprve se uživatelé ověřují ve službě Azure AD a pak se přihlásí k aplikaci.

Použijte zakázaný režim jednotného přihlašování:

- Pokud nejste připraveni integrovat tuto aplikaci s jedním přihlášením Azure AD, nebo
- Pokud testujete jiné aspekty aplikace, nebo
- Jako vrstva zabezpečení pro místní aplikaci, která nevyžaduje, aby uživatelé ověření. S zakázáno, uživatel potřebuje k ověření.

Všimněte si, že pokud jste nakonfigurovali aplikaci pro jednotné přihlašování založené na sp a změníte režim jednotného přihlašování, aby se zakázal, nezastaví to uživatele v přihlášení k aplikaci mimo portál MyApps. Chcete-li toho dosáhnout, je třeba [zakázat možnost pro uživatele k přihlášení](disable-user-sign-in-portal.md)

## <a name="integrated-windows-authentication-iwa-sso"></a>Integrované ověřování systému Windows (IWA) SSO

[Proxy aplikace](application-proxy.md) poskytuje jednotné přihlašování (SSO) aplikacím, které používají [integrované ověřování systému Windows (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)nebo aplikace podporující deklarace identity. Pokud vaše aplikace používá IWA, proxy aplikace se ověřuje do aplikace pomocí kerberos omezené delegování (KCD). Pro aplikaci podporující deklarace identity, která důvěřuje službě Azure Active Directory, funguje jednotné přihlašování, protože uživatel byl již ověřen pomocí služby Azure AD.

Zvolte Integrovaný režim jednotného přihlašování ověřování systému Windows, abyste zajistili jednotné přihlašování k místní aplikaci, která se ověřuje pomocí IWA.

Pokud chcete nakonfigurovat místní aplikaci pro IWA, [přečtěte si informace o delegování omezeného protokolu Kerberos pro jednotné přihlašování k aplikacím pomocí proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md).

### <a name="how-single-sign-on-with-kcd-works"></a>Jak funguje jednotné přihlašování pomocí KCD
Tento diagram vysvětluje tok, když uživatel přistupuje k místní aplikaci, která používá IWA.

![Diagram tok ověřování služby Microsoft Azure AD](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. Uživatel zadá adresu URL pro přístup k místní aplikaci prostřednictvím proxy aplikace.
1. Proxy aplikace přesměruje požadavek na ověřovací služby Azure AD k předběžnému ověření. V tomto okamžiku Azure AD použije všechny příslušné zásady ověřování a autorizace, jako je například vícefaktorové ověřování. Pokud je uživatel ověřen, Azure AD vytvoří token a odešle jej uživateli.
1. Uživatel předá token proxy aplikace.
1. Proxy aplikace ověří token a načte hlavní jméno uživatele (UPN) z tokenu. Potom odešle požadavek, hlavní název hlavního uživatele a hlavní název služby (SPN) do konektoru prostřednictvím duálně ověřeného zabezpečeného kanálu.
1. Spojnice používá vyjednávání delegování s omezenou delegováním protokolu Kerberos (KCD) s místním službou AD, zosobňující uživatele, aby získal token protokolu Kerberos do aplikace.
1. Služba Active Directory odešle token protokolu Kerberos pro aplikaci do konektoru.
1. Konektor odešle původní požadavek na aplikační server pomocí tokenu Kerberos, který obdržel ze služby AD.
1. Aplikace odešle odpověď na konektor, který je pak vrácen službě Proxy aplikace a nakonec uživateli.

## <a name="header-based-sso"></a>Připojetí k samotaku na základě záhlaví

Jednotné přihlašování založené na záhlaví funguje pro aplikace, které používají k ověřování hlavičky HTTP. Tato metoda přihlášení používá ověřovací službu jiného výrobce s názvem PingAccess. Uživatel potřebuje pouze k ověření azure ad.

Zvolte jednotné přihlašování založené na záhlaví, když jsou pro aplikaci nakonfigurovány proxy aplikace a pingaccess.

Chcete-li nakonfigurovat ověřování založené na záhlaví, [přečtěte si informace o ověřování na základě záhlaví pro jednotné přihlašování pomocí proxy aplikace](application-proxy-configure-single-sign-on-with-ping-access.md).

### <a name="what-is-pingaccess-for-azure-ad"></a>Co je PingAccess pro Azure AD?

Pomocí PingAccess pro Azure AD, uživatelé mohou přistupovat a jednotné přihlašování k aplikacím, které používají záhlaví pro ověřování. Proxy aplikace zachází s těmito aplikacemi jako všechny ostatní, pomocí Azure AD k ověření přístupu a pak předávání provozu prostřednictvím služby konektoru. Po ověření dojde, pingaccess služba převede token přístupu Azure AD do formátu záhlaví, který je odeslán do aplikace.

Uživatelé si při přihlášení k používání podnikových aplikací nevšimnou ničeho jiného. Stále mohou pracovat odkudkoli na jakémkoli zařízení. Proxy aplikace konektory přímé vzdálené přenosy do všech aplikací a budou i nadále automaticky vyrovnávání zatížení.

### <a name="how-do-i-get-a-license-for-pingaccess"></a>Jak získám licenci pro PingAccess?

Vzhledem k tomu, že tento scénář je nabízen prostřednictvím partnerství mezi Azure AD a PingAccess, budete potřebovat licence pro obě služby. Předplatná Azure AD Premium však zahrnují základní licenci PingAccess, která pokrývá až 20 aplikací. Pokud potřebujete publikovat více než 20 aplikací založených na záhlaví, můžete získat další licenci od pingaccess.

Další informace naleznete v [tématu Edice Služby Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="related-articles"></a>Související články
* [Kurzy integrace aplikací SaaS s Azure Active Directory](../saas-apps/tutorial-list.md)
* [Konfigurace jednotného přihlašování založeného na saml](configure-single-sign-on-non-gallery-applications.md)
* [Konfigurace jednotného přihlášení založeného na heslech](configure-password-single-sign-on-non-gallery-applications.md)
* [Konfigurace propojeného přihlášení](configure-linked-sign-on.md)
* [Úvod ke správě přístupu k aplikacím](what-is-access-management.md)
* Odkaz ke stažení: [Plán nasazení jednotného přihlášení](https://aka.ms/SSODeploymentPlan).
