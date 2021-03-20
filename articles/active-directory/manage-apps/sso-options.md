---
title: Možnosti jednotného přihlašování v Azure AD
description: Přečtěte si o dostupných možnostech jednotného přihlašování (SSO) v Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: kenwith
ms.reviewer: arvindh, japere
ms.openlocfilehash: 2bf84a22a384e6079c2d85c833b34ba37eecaa46
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99252351"
---
# <a name="single-sign-on-options-in-azure-ad"></a>Možnosti jednotného přihlašování v Azure AD

Jednotné přihlašování poskytuje mnoho výhod oproti tradičním metodám přihlašování.

- **S jednotným přihlašováním** se uživatelé přihlásí jednou z účtu pro přístup k zařízením připojeným k doméně, prostředkům společnosti, aplikacím SaaS (software jako služba) a webovým aplikacím. Po přihlášení může uživatel spouštět aplikace z portálu Office 365 nebo z mých aplikací. Správci mohou centralizovat správu uživatelských účtů a automaticky přidávat nebo odebírat přístup uživatelů k aplikacím na základě členství ve skupinách.

- **Bez jednotného přihlašování** si uživatelé musí pamatovat hesla pro konkrétní aplikace a přihlašovat se ke každé aplikaci. Pracovníci IT potřebují vytvořit a aktualizovat uživatelské účty pro každou aplikaci, například Microsoft 365, box nebo Salesforce. Uživatelé si musí pamatovat hesla a zasílat čas na přihlášení ke každé aplikaci.

Další informace o jednotném přihlašování najdete v tématu [co je jednotné přihlašování?](what-is-single-sign-on.md).

## <a name="choosing-a-single-sign-on-method"></a>Výběr metody jednotného přihlašování

Existuje několik způsobů, jak nakonfigurovat aplikaci pro jednotné přihlašování. Výběr metody jednotného přihlašování závisí na tom, jak je aplikace nakonfigurovaná pro ověřování.

- Cloudové aplikace můžou používat metody OpenID Connect, OAuth, SAML, založené na heslech, propojené nebo zakázané pro jednotné přihlašování. 
- Místní aplikace můžou používat metody jednotného přihlašování založené na heslech, integrovaném ověřování systému Windows, na základě hlaviček, propojených nebo zakázaných metod. Místní volby fungují, když jsou aplikace nakonfigurované pro proxy aplikace.

Tento vývojový diagram vám pomůže určit, která metoda jednotného přihlašování je pro vaši situaci nejvhodnější.

![Rozhodovací vývojový diagram pro metodu jednotného přihlašování](./media/what-is-single-sign-on/choose-single-sign-on-method-040419.png)

Následující tabulka shrnuje metody jednotného přihlašování a odkazy na další podrobnosti.

| Metoda jednotného přihlašování | Typy aplikací | Kdy je použít |
| :------ | :------- | :----- |
| [OpenID Connect a OAuth](#openid-connect-and-oauth) | Cloud a místní | Při vývoji nové aplikace použijte OpenID Connect a OAuth. Tento protokol zjednodušuje konfiguraci aplikace, nabízí snadno použitelné sady SDK a umožňuje vaší aplikaci používat MS Graph.
| [SAML](#saml-sso) | Cloud a místní | Pokud je to možné, vyberte pro existující aplikace, které nepoužívají OpenID Connect nebo OAuth, možnost SAML. SAML funguje pro aplikace, které se ověřují pomocí některého z protokolů SAML.|
| [Založené na heslech](#password-based-sso) | Cloud a místní | Pokud se aplikace ověřuje pomocí uživatelského jména a hesla, vyberte možnost založené na hesle. Jednotné přihlašování založené na heslech umožňuje zabezpečené ukládání hesel aplikací a přehrávání pomocí rozšíření webového prohlížeče nebo mobilní aplikace. Tato metoda používá existující proces přihlášení, který je k dispozici v aplikaci, ale umožňuje správcům spravovat hesla. |
| [Spojeného](#linked-sign-on) | Cloud a místní | Vyberte možnost propojené přihlašování, pokud je aplikace nakonfigurována pro jednotné přihlašování v jiné službě zprostředkovatele identity. Tato možnost nepřidá do aplikace jednotné přihlašování. Aplikace však může již mít jednotné přihlašování implementované pomocí jiné služby, například Active Directory Federation Services (AD FS).|
| [Zakázáno](#disabled-sso) | Cloud a místní | Vyberte zakázané jednotné přihlašování, když aplikace není připravená na konfiguraci jednotného přihlašování. Tento režim je při vytváření aplikace výchozí.|
| [Integrované ověřování systému Windows (IWA)](#integrated-windows-authentication-iwa-sso) | pouze místní | Pro aplikace, které používají [integrované ověřování systému Windows (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)nebo aplikace pracující s deklaracemi, vyberte IWA jednotné přihlašování. Pro IWA konektory proxy aplikací používají k ověřování uživatelů v aplikaci omezené delegování (KCD) protokolu Kerberos. |
| [Na základě hlaviček](#header-based-sso) | pouze místní | Použijte jednotné přihlašování založené na hlavičkách, když aplikace používá hlavičky pro ověřování. Proxy aplikací používá k ověření uživatele službu Azure AD a předává přenos prostřednictvím služby konektoru.  |

## <a name="openid-connect-and-oauth"></a>OpenID Connect a OAuth

Při vývoji nových aplikací použijte moderní protokoly jako OpenID Connect a OAuth, abyste dosáhli nejlepšího jednotného přihlašování pro vaši aplikaci napříč různými platformami zařízení. OAuth umožňuje uživatelům nebo správcům [udělit souhlas](configure-user-consent.md) s chráněnými prostředky, jako je [Microsoft Graph](/graph/overview). Nabízíme snadnou přípravu [sad SDK](../develop/reference-v2-libraries.md) pro vaši aplikaci a navíc je vaše aplikace připravená k použití [Microsoft Graph](/graph/overview).

Další informace naleznete v tématu:

- [OAuth 2,0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [Příručka pro vývojáře Microsoft Identity Platform](../develop/index.yml).

## <a name="saml-sso"></a>SAML SSO

Pomocí **jednotného přihlašování SAML** Azure AD ověřuje aplikace pomocí účtu Azure AD uživatele. Azure AD komunikuje s přihlašovacími informacemi k aplikaci prostřednictvím protokolu připojení. Pomocí jednotného přihlašování založené na SAML můžete mapovat uživatele na konkrétní aplikační role na základě pravidel, která definujete v deklaracích SAML.

Vyberte jednotné přihlašování založené na SAML, když je aplikace podporuje.

Jednotné přihlašování založené na SAML je podporované u aplikací, které používají některý z těchto protokolů:

- SAML 2.0
- WS-Federation

Konfigurace aplikace SaaS pro jednotné přihlašování založené na SAML najdete v tématu [Konfigurace jednotného přihlašování založené na SAML](configure-saml-single-sign-on.md). Mnoho aplikací SaaS (software jako služba) navíc obsahuje [kurz specifický pro aplikaci](../saas-apps/tutorial-list.md) , který vás provede konfigurací jednotného přihlašování založeného na SAML.

Pokud chcete nakonfigurovat aplikaci pro WS-Federation, postupujte podle stejných pokynů ke konfiguraci aplikace pro jednotné přihlašování založené na SAML. V kroku konfigurace aplikace tak, aby používala Azure AD, budete muset nahradit přihlašovací adresu URL služby Azure AD pro WS-Federation koncový bod `https://login.microsoftonline.com/<tenant-ID>/wsfed` .

Pokud chcete nakonfigurovat místní aplikaci pro jednotné přihlašování založené na SAML, přečtěte si téma [jednotné přihlašování SAML pro místní aplikace s proxy aplikací](application-proxy-configure-single-sign-on-on-premises-apps.md).

Další informace o protokolu SAML najdete v tématu [protokol SAML jednotného přihlašování](../develop/single-sign-on-saml-protocol.md).

## <a name="password-based-sso"></a>Jednotné přihlašování založené na heslech

Při přihlašování založeném na hesle se uživatelé přihlásí k aplikaci pomocí uživatelského jména a hesla při prvním přístupu k ní. Po prvním přihlášení Azure AD zadá uživatelské jméno a heslo k aplikaci.

Jednotné přihlašování založené na heslech používá stávající proces ověřování, který je k dispozici v aplikaci. Když pro aplikaci povolíte jednotné přihlašování k heslům, Azure AD shromáždí a bezpečně ukládá uživatelská jména a hesla pro aplikaci. Přihlašovací údaje uživatele jsou uloženy v zašifrovaném stavu v adresáři.

Vyberte jednotné přihlašování založené na heslech, když:

- Aplikace nepodporuje protokol jednotného přihlašování SAML.
- Aplikace se ověřuje pomocí uživatelského jména a hesla místo přístupových tokenů a hlaviček.

>[!NOTE]
>U jednotného přihlašování založeného na heslech nemůžete použít zásady podmíněného přístupu ani službu Multi-Factor Authentication.

Jednotné přihlašování založené na heslech se podporuje pro všechny cloudové aplikace, které mají přihlašovací stránku založenou na jazyce HTML. Uživatel může použít kterýkoli z následujících prohlížečů:

- Internet Explorer 11 v systému Windows 7 nebo novějším
   > [!NOTE]
   > Internet Explorer má omezené podpory a už nepřijímá nové aktualizace softwaru. Microsoft Edge je doporučený prohlížeč.

- Microsoft Edge ve Windows 10 výročí Edition nebo novějších verzích
- Microsoft Edge pro iOS a Android
- Intune Managed Browser
- Chrome ve Windows 7 nebo novějším a na macOS X nebo novějším
- Firefox 26,0 nebo novější v systému Windows XP SP2 nebo novějším a na macOS X 10,6 nebo novějším

Pokud chcete nakonfigurovat cloudovou aplikaci pro jednotné přihlašování založené na heslech, přečtěte si téma [Konfigurace jednotného přihlašování pomocí hesla](configure-password-single-sign-on-non-gallery-applications.md).

Pokud chcete nakonfigurovat místní aplikaci pro jednotné přihlašování prostřednictvím proxy aplikací, přečtěte si téma [trezor hesel pro jednotné přihlašování s proxy aplikací](application-proxy-configure-single-sign-on-password-vaulting.md) .

### <a name="how-authentication-works-for-password-based-sso"></a>Jak ověřování funguje pro jednotné přihlašování založené na heslech

K ověření uživatele v aplikaci Azure AD načte přihlašovací údaje uživatele z adresáře a zadá je do přihlašovací stránky aplikace.  Azure AD bezpečně předává přihlašovací údaje uživatele prostřednictvím rozšíření webového prohlížeče nebo mobilní aplikace. Tento proces umožňuje správci spravovat přihlašovací údaje uživatele a nepožaduje, aby si uživatelé zapamatovali své heslo.

> [!IMPORTANT]
> Přihlašovací údaje jsou při automatizovaném procesu přihlašování zakódovány uživatelem. Přihlašovací údaje se ale zjišťují pomocí nástrojů pro ladění na webu. Uživatelé a správci musí dodržovat stejné zásady zabezpečení, jako kdyby zadali pověření přímo uživatelem.

### <a name="managing-credentials-for-password-based-sso"></a>Správa přihlašovacích údajů pro jednotné přihlašování založené na heslech

Hesla pro jednotlivé aplikace může spravovat správce Azure AD nebo uživatelé.

Když správce Azure AD spravuje přihlašovací údaje:  

- Uživatel není muset resetovat ani pamatovat uživatelské jméno a heslo. Uživatel má přístup k aplikaci kliknutím na ni ve svých aplikacích nebo prostřednictvím poskytnutého odkazu.
- Správce může provádět úlohy správy s přihlašovacími údaji. Správce může například aktualizovat přístup k aplikaci podle členství ve skupině uživatelů a podle stavu zaměstnanců.
- Správce může použít přihlašovací údaje správce k poskytnutí přístupu k aplikacím sdíleným mezi mnoha uživateli. Správce může například udělit všem uživatelům, kteří mají přístup k aplikaci, aby měli přístup k aplikaci pro sdílení přes sociální média nebo dokumentů.

Když koncový uživatel spravuje přihlašovací údaje:

- Uživatelé mohou spravovat svá hesla jejich aktualizací nebo jejich odstraněním podle potřeby.
- Správci stále můžou pro aplikaci nastavit nové přihlašovací údaje.

## <a name="linked-sign-on"></a>Propojené přihlašování
Díky připojenému přihlašování může Azure AD poskytovat jednotné přihlašování k aplikaci, která je už nakonfigurovaná pro jednotné přihlašování v jiné službě. Propojená aplikace se může zdát koncovým uživatelům na portálu Office 365 nebo na portálu Azure AD Mojeapl. Uživatel může třeba spustit aplikaci, která je nakonfigurovaná pro jednotné přihlašování v Active Directory Federation Services (AD FS) 2,0 (AD FS) na portálu Office 365. K dispozici jsou také další sestavy pro propojené aplikace spouštěné z portálu Office 365 nebo z portálu Azure AD Mojeapl. Pokud chcete nakonfigurovat aplikaci pro propojené přihlašování, přečtěte si téma [Konfigurace propojených přihlášení](configure-linked-sign-on.md).

### <a name="linked-sign-on-for-application-migration"></a>Propojené přihlašování pro migraci aplikace

Díky připojenému přihlašování můžete zajistit konzistentní uživatelské prostředí při migraci aplikací v časovém intervalu. Pokud migrujete aplikace na Azure Active Directory, můžete k rychlému publikování odkazů na všechny aplikace, které máte v úmyslu migrovat, použít propojené přihlašování.  Uživatelé můžou najít všechny odkazy na [portálu MyApp](../user-help/my-apps-portal-end-user-access.md) nebo ve [Spouštěči aplikací Microsoft 365](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a). Uživatelé nebudou znát přístup k propojené aplikaci nebo migrované aplikaci.  

Jakmile se uživatel s propojenou aplikací ověří, musí se vytvořit záznam účtu, aby byl koncovým uživatelům poskytnutý přístup s jednotným přihlašováním. K tomu, aby se tento záznam účtu mohl vyskytnout automaticky, nebo ho může vyskytnout správce ručně.

>[!NOTE]
>Pro propojenou aplikaci nemůžete použít zásady podmíněného přístupu ani službu Multi-Factor Authentication. Je to proto, že propojená aplikace neposkytuje možnosti jednotného přihlašování prostřednictvím služby Azure AD. Když nakonfigurujete propojenou aplikaci, stačí přidat odkaz, který se zobrazí ve Spouštěči aplikací nebo v portálu Mojeapl. 

## <a name="disabled-sso"></a>Zakázané jednotné přihlašování

Režim zakázané znamená, že pro aplikaci se nepoužívá jednotné přihlašování. Když je jednotné přihlašování zakázané, můžou se uživatelé muset dvakrát ověřit. Nejdřív se uživatelé ověřují ve službě Azure AD a pak se přihlásí k aplikaci.

Použít zakázaný režim jednotného přihlašování:

- Pokud nejste připraveni k integraci této aplikace s jednotným přihlašováním Azure AD nebo
- Pokud testujete jiné aspekty aplikace nebo
- Jako úroveň zabezpečení pro místní aplikaci, která nevyžaduje ověřování uživatelů. V případě zakázání je uživatel musí ověřit.

Všimněte si, že pokud jste nakonfigurovali aplikaci pro jednotné přihlašování založené na SAML pomocí protokolu SAML a změníte režim jednotného přihlašování (SSO), zabráníte uživatelům v přihlašování k aplikaci mimo portál MyApp. Chcete-li toho dosáhnout, je nutné [zakázat možnost přihlášení uživatelů](disable-user-sign-in-portal.md) .

## <a name="integrated-windows-authentication-iwa-sso"></a>Integrované ověřování systému Windows (IWA) SSO

[Proxy aplikací](application-proxy.md) poskytuje jednotné přihlašování (SSO) k aplikacím, které používají [integrované ověřování systému Windows (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication)nebo aplikace pracující s deklaracemi. Pokud vaše aplikace používá IWA, proxy aplikací se ověří v aplikaci pomocí omezeného delegování protokolu Kerberos (KCD). U aplikací pracujících s deklaracemi, které důvěřují Azure Active Directory jednotné přihlašování, protože uživatel byl už ověřený pomocí Azure AD.

Vyberte možnost integrovaný režim jednotného přihlašování Windows, abyste zajistili jednotné přihlašování k místní aplikaci, která se ověřuje pomocí IWA.

Pokud chcete nakonfigurovat místní aplikaci pro IWA, přečtěte si téma [omezené delegování protokolu Kerberos pro jednotné přihlašování k vašim aplikacím pomocí proxy aplikací](application-proxy-configure-single-sign-on-with-kcd.md).

### <a name="how-single-sign-on-with-kcd-works"></a>Jak jednotné přihlašování pomocí KCD funguje
Tento diagram vysvětluje tok, když uživatel přistupuje k místní aplikaci, která používá IWA.

![Diagram toku Microsoft Azure AD ověřování](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. Uživatel zadá adresu URL pro přístup k místní aplikaci prostřednictvím proxy aplikace.
1. Proxy aplikace přesměruje požadavek na služby ověřování Azure AD na předběžné ověření. V tomto okamžiku Azure AD aplikuje jakékoli použitelné zásady ověřování a autorizace, jako je například vícefaktorové ověřování. Pokud je uživatel ověřený, Azure AD vytvoří token a odešle ho uživateli.
1. Uživatel předává token do proxy aplikace.
1. Proxy aplikace ověří token a načte hlavní název uživatele (UPN) z tokenu. Pak odešle požadavek, hlavní název uživatele (UPN) a hlavní název služby (SPN) do konektoru prostřednictvím dvojího ověřeného zabezpečeného kanálu.
1. Konektor používá vyjednávání omezeného delegování protokolu Kerberos (KCD) s místní službou AD a zosobňuje uživatele k získání tokenu protokolu Kerberos do aplikace.
1. Služba Active Directory odesílá token protokolu Kerberos pro aplikaci konektoru.
1. Konektor odešle původní požadavek na aplikační server pomocí tokenu protokolu Kerberos, který přijal ze služby AD.
1. Aplikace pošle odpověď konektoru, která se pak vrátí službě proxy aplikací a nakonec uživateli.

## <a name="header-based-sso"></a>Jednotné přihlašování založené na hlavičkách

Jednotné přihlašování založené na hlavičkách funguje pro aplikace, které k ověřování používají hlavičky protokolu HTTP.

Vyberte jednotné přihlašování založené na hlavičkách, když je proxy aplikace nakonfigurované pro místní aplikaci.

Další informace o ověřování na základě hlaviček najdete v tématu [jednotné přihlašování na základě](application-proxy-configure-single-sign-on-with-headers.md)hlaviček.


## <a name="next-steps"></a>Další kroky
* [Série rychlý Start při správě aplikací](view-applications-portal.md)
* [Plánování nasazení jednotného přihlašování](plan-sso-deployment.md)
* [Jednotné přihlašování s místními aplikacemi](application-proxy-config-sso-how-to.md)