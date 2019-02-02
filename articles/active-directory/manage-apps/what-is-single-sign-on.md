---
title: Jednotné přihlašování k aplikacím – Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, jak vybrat jedinou metodu přihlašování, při konfiguraci aplikací v Azure Active Directory (Azure AD). Použití jednotného přihlašování, aby uživatelé nemuseli si pamatuje hesla pro každou aplikaci a zjednodušit správu správu účtů.
services: active-directory
author: barbkess
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: barbkess
ms.reviewer: arvindh
ms.openlocfilehash: 8216eb9c076111bfa70633b24f36ac97b0ee319a
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55664025"
---
# <a name="single-sign-on-to-applications-in-azure-active-directory"></a>Jednotné přihlašování k aplikacím v Azure Active Directory
Jednotné přihlašování (SSO) přidává zabezpečení a pohodlí při přihlašování uživatelů k aplikacím v Azure Active Directory (Azure AD). Tento článek popisuje metody přihlašování a vám pomůže vybrat nejvhodnější metodu jednotného přihlašování, při konfiguraci vašich aplikací.

- **S jednotným přihlašováním**, uživatelům přihlásit se jednou pomocí jednoho účtu pro přístup k zařízení připojených k doméně, firemním prostředkům, software jako služba (SaaS) aplikací a webových aplikací. Po přihlášení uživatele můžou spouštět aplikace z portálu Office 365 nebo přístupového panelu Azure AD MyApps. Správci mohou centralizovat správu účtů uživatelů a automaticky přidat nebo odebrat přístup uživatelů k aplikacím na základě členství ve skupině. 

- **Bez jednotného přihlašování**, uživatelé musí pamatovat si hesla specifické pro aplikaci a přihlaste se pro danou aplikaci. IT pracovníci musí vytvořit a aktualizovat uživatelské účty pro každou aplikaci, jako je Office 365, Box nebo Salesforce. Uživatelé musí pamatovat si hesla a ztrácet čas přihlášení pro danou aplikaci.

## <a name="choosing-a-single-sign-on-method"></a>Volba metody jednotné přihlašování

Existuje několik způsobů, jak nakonfigurovat aplikaci pro jednotné přihlašování. Volba metody jednotné přihlašování, závisí na konfiguraci aplikace k ověřování. 

- Cloudové aplikace můžete použít OpenID Connect, OAuth, SAML, založené na heslech, propojené nebo zakázaný metody pro jednotné přihlašování. 
- U místních aplikací můžete použít založené na heslech, integrované Windows ověření, založené na hlavičkách, propojené nebo zakázáno metody pro jednotné přihlašování. Volby místní fungovat, když aplikace jsou konfigurovány pro Proxy aplikací.

Tato vývojový diagram pomáhá při rozhodování, jakou metodu jednotné přihlašování je nejvhodnější pro vaše konkrétní potřeby. 

![Zvolte jednotné přihlašování – metoda](./media/what-is-single-sign-on/choose-single-sign-on-method.png)

Následující tabulka shrnuje metody přihlašování a odkazy na další podrobnosti. 

| Metoda jednotného přihlašování | Typy aplikací | Kdy je použít |
| :------ | :------- | :----- |
| [OpenID Connect a OAuth](#openid-connect-and-oauth) | Jen v cloudu | Při vývoji nových aplikací pomocí OpenID Connect a OAuth. Tento protokol zjednodušuje konfiguraci aplikací, má snadno použitelné sady SDK a umožňuje vaší aplikaci použít MS Graphu.
| [SAML](#saml-sso) | Jen v cloudu | Zvolte SAML, kdykoli je to možné pro existující aplikace, které nepoužívají OpenID Connect nebo OAuth. SAML funguje pro aplikace, které se ověřují pomocí jednoho z protokolů SAML.|
| [Založené na heslech](#password-based-sso) | cloudové a místní | Zvolte založené na heslech, když se aplikace ověřuje pomocí uživatelského jména a hesla. Založené na heslech jednotné přihlašování umožňuje zabezpečené uložení hesel aplikace a jejich přehrání pomocí rozšíření webového prohlížeče nebo mobilní aplikace. Tato metoda používá existující přihlašovací proces poskytovaný aplikací, ale umožňuje správcům spravovat hesla. |
| [Propojené](#linked-sso) | cloudové a místní | Zvolte propojené jednotného přihlašování, pokud je aplikace nakonfigurována pro jednotné přihlašování v jiné službě zprostředkovatele identity. Tato možnost nepřidá jednotné přihlašování k aplikaci. Aplikace může být však již jednotného přihlašování implementované pomocí jiné služby, jako je Active Directory Federation Services.|
| [Disabled](#disabled-sso) (Zakázáno) | cloudové a místní | Zvolte zakázané jednotného přihlašování, pokud aplikace není připraveno ke konfiguraci jednotného přihlašování. Uživatelé potřebují k zadání uživatelského jména a hesla při každém spuštění této aplikace.|
| [Ověření integrované Windows (IWA)](#integrated-windows-authentication-iwa-sso) | jen místně | Zvolte IWA jednotného přihlašování pro aplikace, které používají [integrované ověřování Windows (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication), nebo s deklaracemi identity aplikace. Pro IWA konektory Proxy aplikací pomocí protokolu Kerberos omezené delegování (KCD) k ověřování uživatelů k aplikaci. | 
| [Založené na hlavičkách](#header-based-sso) | jen místně | Použijte založeným na hlavičkách jednotného přihlašování, pokud aplikace používá hlavičky pro ověřování. Založené na hlavičkách jednotného přihlašování vyžaduje PingAccess pro Azure AD. Proxy aplikace ověřuje uživatele pomocí služby Azure AD a potom předává provoz přes službu konektoru.  | 

## <a name="openid-connect-and-oauth"></a>OpenID Connect a OAuth
Při vývoji nových aplikací, použijte k dosažení nejlépe jednotné přihlašování pro vaši aplikaci na různých platformách zařízení moderní protokoly jako OpenID Connect a OAuth. Uživatelům a správcům umožňuje OAuth [udělit souhlas](configure-user-consent.md) pro chráněné zdroje, jako jsou [MS Graphu](/graph/overview). Zajišťuje snadné přijmout [sady SDK](../develop/reference-v2-libraries.md) pro vaši aplikaci a kromě toho vaše aplikace budou připravené k použití [MS Graphu](/graph/overview).

Další informace naleznete v tématu:

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [Příručka pro Azure Active Directory vývojáře](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).

## <a name="saml-sso"></a>JEDNOTNÉ PŘIHLAŠOVÁNÍ SAML
S **SAML jednotného přihlašování**, Azure AD ověří k aplikaci pomocí účtu uživatele Azure AD. Azure AD komunikuje informace přihlašování k aplikaci pomocí připojení protokolu. S založené na SAML jednotného přihlašování můžete mapování uživatelů na konkrétní aplikační role na základě pravidel, které definujete váš SAML deklaracemi identity

Zvolte založené na SAML jednotného přihlašování, pokud aplikace podporuje.


Založené na SAML jednotného přihlašování se podporuje pro aplikace, které používají některá z těchto protokolů:

- SAML 2.0
- WS-Federation

Konfigurace aplikace pro založené na SAML jednotného přihlašování, naleznete v tématu [založené na SAML nakonfigurovat jednotné přihlašování](configure-single-sign-on-portal.md). Mnoho Software jako služba (SaaS) aplikací navíc [specifické pro aplikaci kurzu](../saas-apps/tutorial-list.md) , který jednotlivé kroky konfigurace pro aplikace založené na SAML jednotného přihlašování. 

Další informace o protokolu SAML, naleznete v tématu [jednotné přihlašování – protokol SAML](../develop/single-sign-on-saml-protocol.md).

## <a name="password-based-sso"></a>Jednotné přihlašování pomocí hesla
Založené na heslech přihlašování uživatelé přihlásit k aplikaci pomocí uživatelského jména a hesla při prvním přístupu k jeho. Po první přihlašování Azure AD poskytuje uživatelské jméno a heslo k aplikaci. 

Založené na heslech jednotné přihlašování pomocí stávajícího procesu ověřování poskytované aplikací. Když povolíte heslem jednotného přihlašování pro aplikace, služby Azure AD shromažďuje a bezpečně uloží uživatelské jméno a heslo pro aplikaci. Přihlašovací údaje uživatele jsou uloženy v šifrovaném stavu v adresáři. 

Zvolte založené na heslech single sign-on při:

- Aplikace nepodporuje protokol SAML pro jednotné přihlašování.
- Aplikace se ověřuje pomocí uživatelského jména a hesla místo záhlaví a přístupové tokeny.

Založené na heslech jednotného přihlašování se podporuje pro každou aplikaci založené na cloudu, který má založeného na HTML přihlašovací stránky. Uživatele můžete použít některý z následujících prohlížečů:

- Internet Explorer 11 na Windows 7 nebo novější
- Microsoft Edge ve Windows 10 Anniversary Edition nebo novější 
- Chrome ve Windows 7 nebo novější a v systému MacOS X nebo novější
- Firefox 26.0 nebo novější, Windows XP SP2 nebo novější a na Mac OS X 10.6 nebo novější

Konfigurování cloudové aplikace založené na heslech jednotné přihlašování, naleznete v tématu [konfigurace aplikace pro heslo jednotného přihlašování](application-sign-in-problem-password-sso-gallery.md#configure-the-application-for-password-single-sign-on).

Pokud chcete nakonfigurovat místní aplikaci pro jednotné přihlašování prostřednictvím Proxy aplikací, najdete v článku [heslo vaulting pro jednotné přihlašování s Proxy aplikací](application-proxy-configure-single-sign-on-password-vaulting.md)

### <a name="how-authentication-works-for-password-based-sso"></a>Jak funguje ověřování pro jednotné přihlašování pomocí hesla

Azure AD pro ověření uživatele k aplikaci, načte přihlašovací údaje uživatele z adresáře a přejde na přihlašovací stránku vaší aplikace.  Přihlašovací údaje uživatele Azure AD bezpečně předá prostřednictvím rozšíření webového prohlížeče nebo mobilní aplikace. Tento proces umožňuje správci spravovat přihlašovací údaje uživatele a nevyžaduje, aby uživatelům Zapamatovat heslo.

> [!IMPORTANT]
> Přihlašovací údaje jsou zakódovány od uživatele během procesu automatické přihlašování. Přihlašovací údaje jsou však zjistitelné pomocí nástrojů pro ladění webů. Uživatelé a správci musí postupovat podle stejné zásady pro zabezpečení, jako kdyby jste zadali přihlašovací údaje přímo uživatelem.

### <a name="managing-credentials-for-password-based-sso"></a>Správa přihlašovacích údajů pro jednotné přihlašování pomocí hesla

Hesla pro každou aplikaci je možné spravovat buď pomocí Správce Azure AD nebo uživateli.

Když správce Azure AD spravuje přihlašovací údaje:  

- Uživatel není nutné resetovat nebo zapamatujte si uživatelské jméno a heslo. Má uživatel přístup kliknutím na jejich přístupovém panelu, nebo prostřednictvím poskytnutý odkaz aplikaci.
- Správce může provádět úkoly správy na přihlašovací údaje. Správce může například aktualizovat přístup k aplikacím podle uživatele členství ve skupinách a stav zaměstnance.
- Správce může použít přihlašovací údaje pro správu k zajištění přístupu k aplikacím, které jsou sdílené mezi mnoha uživateli. Správce například můžete umožnit všem uživatelům, kdo má přístup k aplikaci přístup na sociálních médií nebo do dokumentu aplikace pro sdílení obsahu.

Když koncový uživatel spravuje přihlašovací údaje:

- Uživatelé mohou spravovat svá hesla, aktualizace nebo odstranění je podle potřeby. 
- Správci budou mít pořád povolený nastavit nové přihlašovací údaje pro aplikaci.


## <a name="linked-sso"></a>Propojené jednotného přihlašování
Propojené přihlašování umožňuje Azure AD za účelem poskytování jednotného přihlašování k aplikaci, která už je nakonfigurovaný pro jednotné přihlašování v jiné službě. Propojené aplikace může zobrazit koncovým uživatelům na portálu Office 365 nebo portálu Azure AD MyApps. Uživatele můžete například spustit aplikaci, která je nakonfigurovaná pro jednotné přihlašování v Active Directory Federation Services 2.0 (AD FS) z portálu Office 365. Další vytváření sestav je také k dispozici pro propojené aplikace, které jsou spouštěny z portálu Office 365 nebo portálu Azure AD MyApps. 

### <a name="linked-sso-for-application-migration"></a>Propojené jednotné přihlašování pro migraci aplikací

Propojené jednotného přihlašování může poskytovat konzistentní uživatelské prostředí při migraci aplikací po určitou dobu. Pokud se migrace aplikací do Azure Active Directory, můžete použít propojené jednotného přihlašování můžete rychle publikovat odkazy na všechny aplikace, kterou chcete migrovat.  Uživatelé mohou najít všechny odkazy [portálu MyApps](../user-help/active-directory-saas-access-panel-introduction.md) nebo [Spouštěče aplikací Office 365](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a). Uživatelé nebudou vědět, že přistupuje propojené aplikace nebo migrované aplikace.  

Jakmile uživatel byl ověřen s propojených aplikací, je potřeba vytvořit předtím, než se koncovému uživateli poskytuje přístup pomocí jednotného přihlašování záznam účtu. Zřizování tento záznam účtu můžete buď automaticky provedou, nebo to může nastat, ručně správcem.

## <a name="disabled-sso"></a>Jednotné přihlašování zakázáno

Zakázané režim znamená, že jednotné přihlašování se nepoužívá pro aplikaci. Pokud je jednotné přihlašování zakázáno, uživatele může být nutné k ověření dvakrát. Nejdřív ověřit uživatele Azure AD a potom se přihlásí do aplikace. 

Režim jednotného přihlašování zakazuje použití:

- Pokud si nejste připraveni integrovat aplikace s Azure AD jednotného přihlašování, nebo
- Pokud testujete další aspekty aplikace, nebo
- Jako vrstvu zabezpečení, které místní aplikaci, která nevyžaduje, aby uživatele bude možné ověřit. Se zakázanou potřebuje ověřit uživatele. 

## <a name="integrated-windows-authentication-iwa-sso"></a>Integrované ověřování Windows (IWA) jednotného přihlašování

[Proxy aplikací](application-proxy.md) poskytuje jednotné přihlašování (SSO) k aplikacím, které používají [integrované ověřování Windows (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication), nebo s deklaracemi identity aplikace. Pokud vaše aplikace používá IWA, Proxy aplikace ověřuje aplikace s použitím protokolu Kerberos omezené delegování (KCD). Pro aplikace s deklaracemi identity, která vztahy důvěryhodnosti služby Azure Active Directory jednotné přihlašování funguje, protože již byl uživatel ověřen pomocí služby Azure AD.

Zvolte integrované ověřování Windows jednotné přihlašování režim:

- K poskytnutí jednotného přihlašování k místní aplikaci, která se ověřuje pomocí IWA. 

Pokud chcete nakonfigurovat místní aplikaci pro IWA, naleznete v tématu [omezeného delegování protokolu Kerberos pro jednotné přihlašování do aplikací pomocí Proxy aplikace](application-proxy-configure-single-sign-on-with-kcd.md). 

### <a name="how-single-sign-on-with-kcd-works"></a>Jak jednotné přihlašování s KCD funguje
Tento diagram popisuje tok, když uživatel získá přístup k místní aplikaci, která používá IWA.

![Vývojový diagram ověřování Microsoft AAD](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. Uživatel zadá adresu URL pro přístup k místní aplikaci pomocí služby Proxy aplikací.
2. Proxy aplikace přesměruje požadavek preauthenticate ověřování služby Azure AD. V tuto chvíli Azure AD použije všechny příslušné ověřování a zásady autorizace, jako je například vícefaktorové ověřování. Pokud uživatel je ověřen, Azure AD vytvoří token a odešle se uživateli.
3. Uživatel se předá token Proxy aplikací.
4. Proxy aplikace ověří token a načte hlavní název uživatele (UPN) z tokenu. Ke konektoru prostřednictvím zabezpečeného kanálu obousměrně ověřeného pak odešle požadavek, hlavní název uživatele a hlavní název služby (SPN).
5. Konektor používá vyjednávání protokolu Kerberos omezené delegování (KCD) se v místním AD zosobňování uživatele získá token protokolu Kerberos k aplikaci.
6. Služby Active Directory odešle token protokolu Kerberos pro použití konektoru.
7. Konektor odešle původní požadavek na aplikační server pomocí obdrženého ze služby AD token protokolu Kerberos.
8. Aplikace odešle odpověď na konektoru, který je pak vrácen do Proxy aplikace služby a nakonec na uživatele.

## <a name="header-based-sso"></a>Jednotné přihlašování založené na hlavičkách

Založené na hlavičkách jednotné přihlašování funguje pro aplikace, které k ověřování používají hlavičky protokolu HTTP. Tato metoda přihlašování využívá ověřování třetích stran služby zvané PingAccess. Potřebuje uživatel jen pro ověření do služby Azure AD. 

Zvolte založeným na hlavičkách single sign-on při:

- Proxy aplikací a Pingaccessem jsou nakonfigurovány pro aplikaci

Postup konfigurace ověřování založené na hlavičkách, naleznete v tématu [ověřování založené na hlavičkách pro jednotné přihlašování s Proxy aplikací](application-proxy-configure-single-sign-on-with-ping-access.md). 

### <a name="what-is-pingaccess-for-azure-ad"></a>Co je PingAccess pro Azure AD?

Pomocí PingAccess pro Azure AD, uživatelé mohou přístupu a jeden přihlašování k aplikacím, které k ověřování používají hlavičky. Proxy aplikací bude tyto aplikace stejně jako jakýkoli jiný, k ověření přístupu pomocí Azure AD a následným předáním provoz přes službu konektoru. Když dojde k ověření, přeloží služby PingAccess přístupový token Azure AD do záhlaví ve formátu, který je odeslán do aplikace.

Uživatelé nebudou Všimněte si, že něco jinak při přihlášení k vašim podnikovým aplikacím používat. Mohou i nadále pracovat z kdekoli na jakémkoli zařízení. Konektory Proxy aplikací směrovat vzdálený provoz pro všechny aplikace a budou dál k vyrovnávání zatížení automaticky.

### <a name="how-do-i-get-a-license-for-pingaccess"></a>Jak získám licence PingAccess?

Vzhledem k tomu, že tento scénář je dostupná prostřednictvím partnerství mezi službami Azure AD a PingAccess, potřebují licence pro obě služby. Předplatná Azure AD Premium zahrnují však PingAccess licence basic, která zahrnuje až 20 aplikací. Pokud je potřeba publikovat více než 20 aplikací založené na hlavičkách, můžete získat další licenci z PingAccess. 

Další informace najdete v článku [Edice služby Azure Active Directory](../fundamentals/active-directory-whatis.md).


## <a name="related-articles"></a>Související články
* [Kurzy integrace aplikací SaaS pomocí Azure Active Directory](../saas-apps/tutorial-list.md)
* [Kurz ke konfiguraci jednotného přihlašování](configure-single-sign-on-portal.md)
* [Úvod do správy přístupu k aplikacím](what-is-access-management.md)
* Odkaz ke stažení: [Plán nasazení přihlašování](https://aka.ms/SSODeploymentPlan).


